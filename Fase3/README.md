# Proyecto Centinela — Fase 3
## Pipeline, GPU y Despliegue

**Autores:** Estefanny Ruíz González · Miguel Alarcón Ojeda  
**Curso:** Profundización I — Redes Neuronales / Deep Learning  
**Maestría en Ciencia de Datos — Universidad Santo Tomás**  


---

## Descripción

La Fase 3 industrializa el sistema multimodal construido en la Fase 2, asumiendo
el rol de ingeniería MLOps. El escenario de referencia del enunciado
(clasificación de hojas de café para AGROSAVIA) se adapta al problema propio
del proyecto: predicción del régimen cambiario de la TRM y alerta temprana del
poder adquisitivo del SMLV.

| Rol en el enunciado | Nuestra implementación |
|---|---|
| Rama visual (CNN) | ResNet18 sobre matrices de recurrencia de la TRM |
| Rama temporal (RNN/LSTM/GRU) | GRU sobre retornos diarios de la TRM |
| Despliegue offline en tablet | Rama A cuantizada INT8, exportada a LiteRT |
| Despliegue en la nube | Rama B exportada a ONNX, servida por API REST |

**GPU utilizada:** Tesla T4 (Google Colab), 15.6 GB VRAM, CUDA 13.0. PyTorch 2.11.0+cu128, TensorFlow 2.20.0.

---

## Estructura del repositorio

```
Fase3/
├── Centinela_Fase3_Despliegue.ipynb
├── pesos/
│   ├── centinela_fase3_resnet18.pth
│   ├── centinela_fase3_resnet18.onnx
│   ├── centinela_fase3.keras
│   ├── centinela_fase3_resnet18_float.tflite
│   ├── centinela_fase3_resnet18_int8.tflite
│   ├── centinela_fase3_gru.onnx
│   └── centinela_fase3_gru.onnx.data
├── trm_diaria_limpia.csv
├── Informe_Fase3_Centinela .pdf
├── Bitacora_IA_Fase3 .md
└── README.md
```

Los pesos (`.pth`, `.keras`, `.tflite`, `.onnx`) se versionan con Git LFS, igual
que los `.pt` de la Fase 2.

---

## Qué se hizo

1. **Pipeline de datos:** las 6.277 matrices de recurrencia (PNG 30×30) se
   materializan en disco (`imagenes/{train,val,test}/<clase>/`) y se comparan
   los tiempos de carga por época entre `tf.data` y `torch.utils.data.DataLoader`.
2. **Data augmentation:** 4 transformaciones justificadas (rotación ±15°, flip
   horizontal, brillo, recorte redimensionado); +2.7 pp de accuracy de
   validación frente a no usar augmentation.
3. **Entrenamiento en GPU con precisión mixta:** ResNet18 (Feature Extraction)
   en FP32 vs. AMP/FP16, con checkpointing cada 5 épocas y manejo documentado
   de `CUDA OutOfMemoryError`.
4. **Optimización edge:** cuantización post-entrenamiento INT8 vía
   `tf.lite.TFLiteConverter` con `representative_dataset` de imágenes reales.
5. **Exportación multi-formato y verificación:** `.pth` → `.onnx` → `.keras` →
   `.tflite` (float y INT8) para la rama visual; `.onnx` para la rama temporal
   (GRU), con verificación numérica `np.allclose(atol=1e-4)` entre PyTorch y
   ONNX Runtime.
6. **Ruta de despliegue dual:** rama visual offline en tablet vía LiteRT; rama
   temporal servida en la nube vía API REST (ONNX Runtime + FastAPI).

## Resultados principales

**Data augmentation (10 épocas, ResNet18 Feature Extraction):**

| Configuración | Accuracy val | Diferencia |
|---|---|---|
| Con augmentation | 0.497 | +0.027 |
| Sin augmentation | 0.470 | ref. |

**FP32 vs. precisión mixta (AMP), 20 épocas, GPU Tesla T4:**

| Configuración | Tiempo (min) | Pico VRAM (MB) | Mejor val loss |
|---|---|---|---|
| FP32 | 4.31 | 347 | 1.0824 |
| AMP (FP16) | 4.21 | 299 | 1.0824 |

Ahorro de VRAM: 13.9 % (menor al ~50% teórico porque Adam mantiene los pesos maestros en FP32).

**Cuantización INT8 — Rama A visual (ResNet18), test set:**

| Métrica | Antes (float32) | Después (INT8) | Cambio |
|---|---|---|---|
| Tamaño modelo (MB) | 16.04 | 4.90 | −69.4 % |
| Latencia CPU (ms/img) | 21.17 | 20.36 | 1.04× |
| Accuracy (test) | 0.335 | 0.338 | +0.003 pp |
| F1 macro (test) | 0.167 | 0.203 | +0.036 pp |

La mejora de accuracy/F1 post-cuantización se reporta como marginal (n=942,
3 clases desbalanceadas no permite conclusiones firmes sobre diferencias tan
pequeñas); el beneficio real de INT8 es la reducción de tamaño para tablets sin GPU.

## Reproducibilidad

El notebook corre en Google Colab con GPU T4.  
Requiere: `trm_diaria_limpia.csv` en el mismo directorio.  
Incluye una prueba de humo (Sección 0.5) que valida la cadena de exportación
`.pth → .onnx → .keras → .tflite` con un modelo diminuto antes de entrenar
nada, y una ruta alterna (`ai-edge-torch`) por si `onnx2tf` falla en el
entorno de ejecución.
