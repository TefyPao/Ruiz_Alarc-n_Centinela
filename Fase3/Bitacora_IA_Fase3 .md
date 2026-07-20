# Bitácora de uso de Inteligencia Artificial — Fase 3
### Proyecto Centinela · Pipeline, GPU y Despliegue

**Autores:** Estefanny Ruíz González · Miguel Alarcón Ojeda  
**Herramienta:** Claude (Anthropic) — Claude Code + conversacional  

---

## Propósito

Documentar el uso de IA durante la Fase 3, que tuvo un perfil distinto a las
fases anteriores: el reto principal no fue el modelado sino la **ingeniería
de despliegue** — instalación de paquetes, compatibilidad de versiones y
cadenas de exportación. La IA fue intensamente útil en depuración técnica,
pero todas las decisiones de arquitectura de despliegue fueron tomadas por
los autores.

---

## Sugerencias aceptadas

**Estrategia de despliegue dual (rama visual offline, rama temporal en nube).**
La IA propuso separar el despliegue: EfficientNet-B0 a tablet con LiteRT,
GRU a API REST en la nube. Se aceptó porque la exportación de capas
recurrentes a LiteRT es notoriamente inestable, y el GRU de todas formas
necesita datos de TRM actualizados que requieren conectividad.

**Usar `subprocess.check_call` en lugar de `!pip` para instalaciones.**
La IA propuso esta estrategia para que los paquetes queden disponibles en
el proceso Python activo sin necesitar reinicio del kernel. Se aceptó tras
verificar que resolvía el problema de `ModuleNotFoundError` post-instalación.

**Exportar `.keras` directamente desde TensorFlow sin `onnx2tf`.**
Después de múltiples fallos con `onnx2tf` (incompatibilidad con depthwise
convolutions de EfficientNet-B0), la IA propuso construir la arquitectura
equivalente en Keras y exportarla directamente. Se aceptó — es la ruta más
robusta para esta arquitectura.

**`opset_version=18` en lugar de 13 para exportación ONNX.**
PyTorch ≥ 2.0 usa por defecto opset 18 y la conversión a 13 falla. La IA
identificó el error y propuso el cambio. Se aceptó tras verificar que
ONNX Runtime 1.21.0 soporta opset 18.

**Cuantización INT8 con `representative_dataset` de imágenes reales.**
La IA propuso usar 100 imágenes reales de entrenamiento para calibrar los
rangos de activación, en lugar de datos aleatorios. Se aceptó porque es
el requisito técnico para una cuantización INT8 correcta (datos aleatorios
no representan la distribución real de activaciones).

**`os.makedirs(..., exist_ok=True)` antes de cada `.save()`.**
Error recurrente: las celdas de exportación fallaban porque las carpetas
destino no existían. La IA identificó el patrón y lo corrigió en todas
las celdas de una vez. Se aceptó.

---

## Sugerencias modificadas

**Versiones de paquetes en la celda de instalación.**
La IA propuso versiones sucesivas de `onnxruntime` (1.18.0 → 1.20.0 →
1.21.0) hasta encontrar la compatible con numpy 2.x de Colab. Cada versión
fue probada por los autores antes de aceptar la siguiente iteración. La
versión final (1.21.0) fue validada por los autores ejecutando la celda de
verificación de importaciones.

**Tabla de augmentation.**
La IA completó la tabla de augmentation con los valores de la Fase 2
(0.497 con augmentation vs 0.470 sin), ya que la Fase 3 no reentrenó desde
cero sino que refinó el modelo existente. Los autores verificaron que esta
comparación era válida para el propósito del informe.

---

## Sugerencias rechazadas

**Usar `ai-edge-torch` como ruta de exportación a `.tflite`.**
La IA sugirió esta librería como alternativa a `onnx2tf`. Se rechazó porque
`ai-edge-torch` no produce un archivo `.keras` — solo `.tflite` — y el
enunciado exige los tres formatos. Se prefirió la ruta TF directa que
produce tanto `.keras` como `.tflite`.

**`onnxruntime-gpu` en lugar de `onnxruntime`.**
La IA mencionó esta opción para aprovechar la GPU en inferencia ONNX.
Se rechazó porque el escenario de despliegue del GRU es un servidor en
la nube donde la inferencia en CPU es suficiente para la latencia requerida
(retornos diarios, no en tiempo real).

**Usar `torch.quantization.quantize_dynamic`.**
La IA propuso esta ruta PyTorch para la cuantización. Se rechazó porque
el enunciado pide exportación a LiteRT (TFLite), y la cuantización debe
hacerse en el formato TFLite para que sea efectiva en el despliegue
objetivo (tablet). La cuantización PyTorch no reduce el tamaño del `.tflite`.

---

## Decisiones tomadas de forma autónoma

**Documentar el ahorro de VRAM de AMP como 13.9% y no ~50%.**
El ahorro teórico de FP16 en activaciones es ~50%, pero el medido fue
13.9%. Los autores decidieron reportar el valor real con la explicación
técnica (Adam mantiene pesos maestros en FP32), en lugar de el valor
teórico. Un resultado honesto es más valioso que uno que confirma la teoría.

**Reportar la mejora de accuracy post-cuantización como marginal.**
La cuantización INT8 subió la accuracy de 0.335 a 0.338 y el F1 de
0.167 a 0.203. Los autores decidieron reportar esto como "estadísticamente
marginal por la varianza del conjunto de test" en lugar de presentarlo
como una mejora real, porque n=942 con 3 clases desbalanceadas no permite
conclusiones firmes sobre diferencias tan pequeñas.

**Mantener ResNet18 para la Fase 3 en lugar de migrar a EfficientNet-B0.**
El notebook de Claude Code usaba ResNet18. Aunque en la Fase 2 seleccionamos
EfficientNet-B0 como modelo final, los autores decidieron mantener ResNet18
en la Fase 3 para documentar la cadena completa de exportación sin
reentrenar desde cero, dado el tiempo disponible.

---

## Reflexión final

La Fase 3 evidenció que la IA es especialmente útil en **depuración técnica
de entornos**: identificar versiones incompatibles, encontrar el orden
correcto de instalación y corregir errores de API que cambian entre versiones
de PyTorch/TensorFlow. Sin embargo, las decisiones de ingeniería de
mayor impacto — qué arquitectura va a Edge, cómo interpretar el trade-off
de cuantización, qué reportar honestamente — fueron tomadas por los autores.

Los autores asumen plena responsabilidad sobre el contenido de todos los
entregables y están en capacidad de sustentar cada decisión técnica.
