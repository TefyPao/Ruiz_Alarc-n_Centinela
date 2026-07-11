# Bitácora de uso de Inteligencia Artificial — Fase 2
### Proyecto Centinela · Ramas B, A y C

**Autores:** Estefanny Ruíz González · Miguel Alarcón Ojeda
**Herramienta:** Claude (Anthropic) — conversacional
**Período:** 7–12 julio 2026

---

## Propósito

Registrar el uso de IA durante la Fase 2, documentando qué se aceptó,
qué se modificó y qué se rechazó. Ninguna salida de la IA se adoptó
sin revisión crítica. Las decisiones de fondo fueron tomadas por los autores.

---

## Sugerencias aceptadas

**Usar retorno porcentual diario como variable objetivo (Rama B).**
La IA identificó que el nivel absoluto generaba extrapolación severa —
errores de ~1.300 pesos. Se verificó con datos reales y se aceptó tras
confirmar que los retornos son estacionarios (RMSE final: 0.83%).

**Pre-calcular matrices de recurrencia antes del entrenamiento (Rama C).**
La primera versión calculaba las matrices dentro del Dataset en cada batch,
causando ~25 minutos por época. La IA propuso pre-calcularlas una sola vez.
Se aceptó y el tiempo bajó a 1-2 minutos por época.

**Matrices de recurrencia como modalidad de imagen (Plan B autónomo).**
Al no recibir respuesta del docente sobre imágenes, la IA propuso matrices
de recurrencia. Se aceptó porque no requería datos externos y cumplía el
mínimo de 500 imágenes por clase. Se notificó al docente.

**Separar slides densas en la presentación.**
La IA propuso dividir las slides de CNN y Fusión que estaban sobrecargadas.
Se aceptó. La presentación final tiene 15 slides bien espaciadas.

**Data augmentation en Rama A.**
La IA propuso rotación ±15°, flip horizontal y variación de brillo ±20%
solo en entrenamiento. Se aceptó porque la rúbrica lo exige explícitamente.

**Comparar ResNet18 vs EfficientNet-B0.**
La rúbrica exige dos arquitecturas. La IA propuso EfficientNet-B0 como
segunda opción. Se aceptó. Resultado: EfficientNet-B0 marginalmente mejor
(0.497 vs 0.496) con menos de la mitad de los parámetros.

**Referencias APA 7 inline.**
La clase tg_usta no resuelve correctamente las citas con el sistema harvard.
La IA propuso reemplazarlas por citas inline en formato APA 7. Se aceptó.

---

## Sugerencias modificadas

**Grad-CAM con hooks en capas congeladas.**
La primera implementación fallaba porque los hooks no capturaban gradientes
en capas congeladas. La IA propuso tres versiones sucesivas. Se adoptó la
versión final que habilita gradientes temporalmente solo para el cálculo.

**Reconstrucción del nivel TRM desde retornos.**
La primera versión acumulaba error en 917 días y producía valores de ~5.250
pesos cuando debía anclar en ~4.089. Se diagnosticó el bug (acumulación en
toda la serie de test) y se corrigió limitando a primeros 120 días.

**Layout de las slides 8 y 9.**
La IA propuso varias versiones de layout con figuras. Se rechazaron dos
versiones por verse sobrecargadas o cortadas. La versión final aceptada
separa la comparación de arquitecturas, los resultados y el Grad-CAM en
slides distintas.

---

## Sugerencias rechazadas

**k-fold para validación cruzada.**
La IA mencionó TimeSeriesSplit como alternativa más robusta. Se rechazó
para mantener consistencia con la Fase 1 y con la instrucción del docente
de usar partición cronológica 70/15/15.

**Fine-Tuning profundo en la CNN.**
La IA señaló que podría mejorar el 34% de exactitud. Se rechazó por
limitación de tiempo y porque la justificación de Feature Extraction es
académicamente más sólida con los datos disponibles.

**Insertar visualizaciones para el cliente dentro del informe.**
La IA propuso incluirlas como figuras. Se rechazó porque superaría las
8 páginas máximas y mezclaría dos audiencias distintas en un mismo documento.
Se mantuvieron como archivos independientes en el repositorio.

---

## Decisiones tomadas de forma autónoma (sin sugerencia de IA)

**Ejecutar el Plan B sin esperar respuesta del docente.**
Al no recibir orientación antes del jueves 10 de julio, los autores
decidieron proceder con matrices de recurrencia para cumplir la entrega
del domingo 12. Se notificó al docente por mensaje.

**Documentar el sobreajuste del GRU como resultado honesto.**
En lugar de intentar forzar mejores resultados, se documentó el
sobreajuste del GRU en clasificación como hallazgo del estudio de
ablación, extrayendo el principio de que la fusión solo mejora cuando
cada embedding es de calidad.

**Seleccionar EfficientNet-B0 sobre ResNet18.**
Ante resultados prácticamente iguales, los autores decidieron reportar
EfficientNet-B0 como arquitectura seleccionada por su eficiencia en
parámetros (5.3 M vs 11.2 M), criterio relevante para despliegue.

---

## Reflexión final

La Fase 2 fue más compleja que la Fase 1 en términos de dependencia de la IA,
con más iteraciones de código y más decisiones de diseño. Sin embargo, las
decisiones de fondo — qué variable predecir, cómo manejar la ausencia de
respuesta del docente, cómo interpretar el sobreajuste, qué arquitectura
seleccionar — fueron tomadas por los autores con criterio propio.

El episodio del pre-cálculo de matrices y el bug de reconstrucción de la TRM
ilustran que la IA puede proponer soluciones técnicas útiles, pero que deben
verificarse en la práctica antes de aceptarlas. Los autores asumen plena
responsabilidad sobre el contenido de todos los entregables y están en
capacidad de sustentar cada decisión.
