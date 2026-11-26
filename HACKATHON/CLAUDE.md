# Diseño de placas estructurales (muros de concreto armado) — Instrucciones para Claude

Este proyecto existe para que Claude diseñe placas **aplicando exclusivamente el criterio de diseño del usuario**.
Los criterios están en la carpeta [criterios/](criterios/). Claude no los reemplaza, no los "mejora" y no los completa con conocimiento propio.

---

## 1. Reglas de oro (no negociables)

1. **Cero suposiciones.** Si falta un dato (geometría, cargas, materiales, fuerzas internas, recubrimiento, etc.), Claude **se detiene y pregunta**. No usa valores "típicos", "usuales" ni "razonables".
2. **Cero alucinaciones normativas.** Claude no cita artículos, fórmulas, coeficientes ni límites de una norma (E.060, ACI 318, etc.) que no estén escritos en `criterios/` o que el usuario no haya dado en el chat. Si cree que una verificación normativa falta, lo **señala como observación**, no la inventa ni la aplica.
3. **El criterio del usuario manda.** Si el criterio del usuario difiere de lo que Claude conoce de una norma, se aplica el criterio del usuario. Claude puede dejar una nota breve de la diferencia, pero no cambia el cálculo.
4. **Trazabilidad total.** Cada valor numérico del diseño debe indicar su origen con una de estas etiquetas:
   - `[DATO]` — dado por el usuario (planos, modelo, chat, archivo de entrada).
   - `[CRITERIO §x]` — regla tomada de un archivo de `criterios/`, indicando archivo y sección.
   - `[CÁLCULO]` — obtenido con una fórmula del criterio, mostrando la fórmula y la sustitución numérica.
   - `[CONFIRMADO]` — valor que Claude propuso y el usuario aprobó explícitamente en el chat.
   No existe otra fuente válida. Un valor sin etiqueta es un error.
5. **Criterio incompleto = no se diseña esa parte.** Si un archivo de `criterios/` está marcado `PENDIENTE` o no cubre un caso (p. ej. placa en "T", "L", con aberturas, esbelta vs. robusta), Claude dice exactamente qué caso no está cubierto y pide el criterio. No lo resuelve por su cuenta.
6. **Ambigüedad = preguntar.** Si una regla del criterio admite dos lecturas, Claude muestra ambas y pregunta cuál aplica antes de calcular.
7. **Unidades explícitas** en cada dato, fórmula y resultado. Claude no convierte unidades sin mostrarlo. El sistema de unidades del proyecto lo define [criterios/00_generales.md](criterios/00_generales.md).
8. **Cálculo verificable.** Las operaciones numéricas se hacen con script (Python) o se muestran paso a paso; no se dan resultados "de memoria". Se redondea solo al final y según la regla del criterio.
9. **No ocultar fallas.** Si un elemento no cumple una verificación del criterio, se reporta como **NO CUMPLE**, con el valor obtenido y el límite. Claude no ajusta datos para "hacerlo cumplir" sin permiso del usuario; puede proponer alternativas, marcadas como propuestas.
10. **Honestidad sobre el alcance.** Al final de cada diseño, Claude lista lo que **no** verificó (porque el criterio no lo pide o porque faltó información).

---

## 2. Flujo de trabajo obligatorio

Para cada placa o grupo de placas:

### Paso 0 — Verificar criterios disponibles
Leer todos los archivos de `criterios/`. Si alguno necesario está `PENDIENTE`, avisar antes de empezar.

### Paso 1 — Recopilar y listar datos de entrada
Usar la plantilla [proyectos/_plantilla_datos_entrada.md](proyectos/_plantilla_datos_entrada.md).
Mostrar al usuario una **tabla de datos recibidos** y una **lista de datos faltantes**.
**No avanzar** hasta que la lista de faltantes esté vacía (o el usuario indique explícitamente cómo tratar cada faltante).

### Paso 2 — Predimensionamiento de núcleos (elementos de borde)
Aplicar [criterios/01_predimensionamiento_nucleos.md](criterios/01_predimensionamiento_nucleos.md).
Resultado: longitud de cada núcleo, con la regla aplicada y la verificación correspondiente.

### Paso 3 — Diseño del acero longitudinal
Aplicar [criterios/02_acero_longitudinal.md](criterios/02_acero_longitudinal.md).
Resultado: acero en núcleos y en alma, cuantías, distribución, verificaciones (p. ej. flexocompresión) **solo las que pida el criterio**.

### Paso 4 — Diseño del acero transversal
Aplicar [criterios/03_acero_transversal.md](criterios/03_acero_transversal.md).
Resultado: acero horizontal del alma por corte, confinamiento de núcleos (estribos/ganchos), espaciamientos.

### Placas irregulares (L, T, C, E)
Aplicar además [criterios/04_placas_irregulares.md](criterios/04_placas_irregulares.md): primero se diseña cada tramo por separado y luego se verifica la sección completa, respetando la regla de orientación del eje local 2.

### Paso 5 — Resumen y observaciones
Entregar el formato de salida de la sección 3.

Entre pasos, si el usuario lo pide, Claude se detiene para revisión antes de continuar.

---

## 3. Formato de salida de cada diseño

1. **Identificación**: nombre de la placa, nivel(es), proyecto.
2. **Datos de entrada**: tabla con valor, unidad y etiqueta `[DATO]`.
3. **Desarrollo por paso** (2, 3 y 4): regla → fórmula → sustitución → resultado → cumple / no cumple.
4. **Resumen del armado**: tabla final (núcleos: longitud, barras, estribos; alma: acero vertical, acero horizontal, espaciamientos).
5. **Supuestos confirmados**: lista de todo lo marcado `[CONFIRMADO]`.
6. **No verificado / fuera de alcance**.
7. **Observaciones** de Claude (dudas, diferencias con norma, sugerencias), separadas claramente del diseño.

Los cálculos se guardan en `proyectos/<nombre_proyecto>/` (datos, script y resultados), para que sean reproducibles.

---

## 4. Lo que Claude NO debe hacer

- Inventar fuerzas internas (Pu, Mu, Vu), dimensiones, f'c, fy o recubrimientos.
- Leer valores de planos/imágenes con baja confianza sin confirmarlos con el usuario (si un número de un plano o captura es dudoso, se pregunta).
- Aplicar factores de amplificación, reducción (φ) o combinaciones de carga que no estén en el criterio o en los datos.
- Elegir diámetros de barra fuera de la lista permitida por el criterio.
- Decir "según norma" sin que la regla esté en `criterios/`.
- Dar por terminado un diseño con datos faltantes.

---

## 5. Mantenimiento de los criterios

- Cuando el usuario entregue o corrija su criterio, Claude lo transcribe a `criterios/` **tal cual**, sin reinterpretarlo, y luego muestra un resumen para que el usuario confirme que la transcripción es fiel.
- Si el usuario entrega el criterio en PDF, Excel, imagen o ejemplo resuelto, Claude extrae las reglas, las escribe en el archivo correspondiente y marca con `⚠ VERIFICAR` cualquier parte que haya tenido que interpretar.
- Cada archivo de criterio lleva un registro de cambios al final.
- Fuentes recibidas hasta ahora:
  - `grabacion/`: archivos reales del ejemplo P2 (Excel de predimensionamiento y de diseño, CSiCOL, DWG, modelo ETABS). **Las fórmulas de estos Excel son el criterio oficial.**
  - [referencias/](referencias/): resúmenes JSON (P4A y video P2) y [hallazgos_excel.md](referencias/hallazgos_excel.md).
  - **Jerarquía de fuentes:** lo que el usuario confirma en el chat > fórmulas de los Excel > resúmenes JSON.
- El motor de cálculo debe **reproducir los mismos números** que los Excel del usuario con los mismos datos de entrada, antes de usarse en placas nuevas.
