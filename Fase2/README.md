# Proyecto Centinela — Fase 2
## Sistema de alerta temprana del poder adquisitivo del SMLV

**Autores:** Estefanny Ruíz González · Miguel Alarcón Ojeda  
**Curso:** Profundización I — Redes Neuronales / Deep Learning  
**Maestría en Ciencia de Datos — Universidad Santo Tomás**  
**Entrega:** julio 2026

---

## Descripción

La Fase 2 extiende el sistema Centinela incorporando dos modalidades de datos:

- **Rama B:** TRM diaria con RNN simple, LSTM y GRU (regresión de retornos)
- **Rama A:** Matrices de recurrencia con ResNet18 vs EfficientNet-B0 (clasificación)
- **Rama C:** Fusión multimodal con estudio de ablación

---

## Estructura del repositorio

```
Fase2/
├── notebooks/
│   ├── Centinela_Fase2_RamaB_v2.ipynb
│   ├── Centinela_Fase2_RamaA_v2.ipynb
│   └── Centinela_Fase2_RamaC_v2.ipynb
├── pesos/
│   ├── centinela_fase2_gru.pt
│   ├── centinela_fase2_lstm.pt
│   ├── centinela_fase2_rnn.pt
│   ├── centinela_fase2_resnet18.pt
│   ├── centinela_fase2_efficientnet_b0.pt
│   ├── centinela_fase2_fusion.pt
│   ├── centinela_fase2_solo_gru.pt
│   └── centinela_fase2_solo_cnn.pt
├── visualizaciones_cliente/
│   ├── viz_cliente_1_trm_regimen.pdf
│   ├── viz_cliente_2_distribucion_anual.pdf
│   └── viz_cliente_3_alerta_actual.pdf
├── figuras/
│   ├── curvas_fase2.pdf
│   ├── gradiente_fase2.pdf
│   ├── fig_curvas_comparacion.pdf
│   ├── fig_cm_comparacion.pdf
│   ├── fig_gradcam_v2.pdf
│   ├── fig_curvas_ablacion.pdf
│   └── fig_cm_ablacion.pdf
├── trm_diaria_limpia.csv
├── Informe_Fase2_Centinela.pdf
├── Presentacion_Fase2_Limpia.pdf
├── Bitacora_IA_Fase2.md
└── README.md
```

---

## Datos

- **Fuente:** Superintendencia Financiera de Colombia vía Datos Abiertos Colombia
- **Serie:** TRM diaria 2000–2026 (6.308 días hábiles)
- **Variable objetivo Rama B:** retorno porcentual diario
- **Variable objetivo Rama A:** régimen cambiario (Depreciación / Estable / Apreciación)

## Resultados principales

| Rama | Modelo | Métrica principal |
|---|---|---|
| B — Serie temporal | GRU | RMSE = 0.83% · MAE = 0.64% |
| A — Imágenes | EfficientNet-B0 | Accuracy = 49.7% · F1 = 0.343 |
| C — Fusión | Solo CNN (mejor) | Accuracy = 47.0% · F1 = 0.341 |

## Reproducibilidad

Todos los notebooks corren en Google Colab con GPU T4.  
Semilla fija: `SEMILLA = 42`  
Requiere: `trm_diaria_limpia.csv` en el mismo directorio.
