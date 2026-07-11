# Proyecto Centinela — Fase 1: línea base con un MLP
### Sistema de alerta temprana del poder adquisitivo del salario mínimo (Bogotá)
 
**Curso:** Profundización I — Redes Neuronales / Deep Learning 
**Maestría en Ciencia de Datos — Universidad Santo Tomás**
 
**Autores:** Estefanny Ruíz González, Miguel Alarcón Ojeda
 
---
 
## Descripción
 
Este repositorio contiene la entrega de la Fase 1 del Proyecto Centinela: la construcción de la línea base de un sistema de alerta temprana que predice si el poder adquisitivo del salario mínimo legal vigente (SMLV) en Bogotá está cayendo en un mes dado, usando un perceptrón multicapa (MLP) entrenado desde cero en PyTorch.
 
El problema se enmarca en el proyecto de grado de los autores sobre poder adquisitivo del SMLV en Colombia, adaptado aquí al formato de clasificación binaria que exige la actividad.
 
## Contenido del repositorio
 
| Archivo | Descripción |
|---|---|
| `Notebook_Centinela_Fase1.ipynb` | Notebook completo: EDA, ingeniería de variables, partición cronológica, clase MLP, bucle de entrenamiento, regularización con Dropout, curvas de pérdida y matriz de confusión. Listo para ejecutar en Google Colab. |
| `consolidado_bogota.csv` | Base de datos consolidada (mensual, 2000–2026) a partir de fuentes oficiales (DANE, Banco de la República). |
| `Informe_Tecnico_Etico_Centinela.tex` / `.pdf` | Informe técnico-ético (máx. 4 páginas): problema, metodología, resultados, análisis ético y limitaciones. |
| `Bitacora_IA_Centinela.tex` / `.pdf` | Bitácora de uso de inteligencia artificial: sugerencias aceptadas, modificadas y rechazadas, con su justificación. |
| `Propuesta_de_Datos_Ruiz_Alarcón.md` | Propuesta de datos de la Fase 1. |
| `curvas_perdida.pdf`, `matriz_confusion.pdf` | Figuras incluidas en el informe. |
| `tg_usta.cls`, `logo-usta.eps` | Plantilla institucional para compilar los documentos `.tex`. |
 
## Cómo ejecutar el notebook
 
1. Abrir `Notebook_Centinela_Fase1.ipynb` en [Google Colab](https://colab.research.google.com).
2. Subir `consolidado_bogota.csv` al entorno de Colab (o montar Google Drive).
3. Ejecutar las celdas en orden (Entorno de ejecución → Ejecutar todas).
## Cómo compilar los documentos `.tex`
 
Requiere una distribución LaTeX completa (MiKTeX o TeX Live) con el paquete `harvard`.
 
1. Colocar `tg_usta.cls`, `logo-usta.eps` y los `.tex` en la misma carpeta.
2. Compilar con `pdflatex` (por ejemplo, desde VS Code con la extensión LaTeX Workshop).
**Nota sobre `tg_usta.cls`:** esta plantilla fue heredada de un curso distinto (Visualización de Datos) y se le hicieron dos ajustes para esta entrega:
- Se cambió la codificación de entrada de `latin1` a `utf8` (línea `\RequirePackage{inputenc}`), para que los acentos se procesen correctamente con la codificación estándar de los editores actuales.
- Se corrigió el nombre del curso, que venía fijo en la plantilla como "Visualización de Datos", reemplazándolo por "Redes Neuronales / Deep Learning".
## Resumen metodológico
 
- **Variable objetivo:** `riesgo = 1` si la variación anual del salario mínimo real es negativa.
- **Desbalance de clases:** ~9.6 % de los registros corresponden a la clase de riesgo.
- **Variables de entrada:** variación mensual y trimestral del IPC, y brecha entre el crecimiento del salario nominal y la inflación (features de dinámica, no niveles crudos).
- **Partición:** cronológica 70/15/15. El conjunto de prueba no contiene casos de riesgo (concentración histórica del fenómeno); las métricas de detección se reportan sobre el conjunto de validación.
- **Resultados (validación):** precisión 0.917, recall 0.846, F1 0.880.
## Limitaciones y trabajo futuro
 
Muestra reducida (~312 meses utilizables), una sola ciudad piloto (Bogotá), y datos parcialmente proyectados para 2026. Como extensiones futuras, sugeridas por la asesora del proyecto de grado, se contemplan un índice de asequibilidad (canasta familiar / SMLV) y un agrupamiento jerárquico por *Dynamic Time Warping* (DTW) para la fase multi-ciudad.
 
