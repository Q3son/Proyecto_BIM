# Criterio: placas irregulares (L, T, C, E) y orientación de ejes

> **ESTADO: PARCIAL.** Transcrito del JSON del video P2 (placa en L). Las placas en T, C y E no tienen ejemplo propio: Claude aplica esta estrategia por analogía **solo después de confirmarlo contigo**.

## 1. Estrategia por tramos `[JSON P2, design_methodology_overview]`
1. En ETABS cada tramo tiene su propio **Pier label** (P2a = tramo en X, P2b = tramo en Y).
2. **Cada tramo se diseña como una placa independiente** con todo el flujo: predimensionamiento de núcleos, acero del alma y verificación P‑M a 0° y 90°.
   - Al aislar un tramo se incluye un **pequeño retorno del ala en la esquina (40 cm)** `[JSON P2, paso 3]`.
   - `⚠ VERIFICAR`: ¿los 40 cm de retorno salen de la longitud del núcleo de esquina, o son una regla fija?
3. Luego se **unifican** los tramos bajo un solo Pier label (P2) en otro modelo de ETABS ("un solo pier") y se verifica la sección compuesta completa en L con las fuerzas de ese pier.
4. En la sección completa aparecen **tres zonas de núcleo**: los dos extremos libres y la esquina central `[JSON P2, paso 5]`.
5. Que cada tramo cumpla por separado **no garantiza** que cumpla la sección completa. En P2 la sección completa falló a 0° y hubo que iterar.

## 2. Regla de orientación de ejes (obligatoria) `[JSON P2]`
- En la sección que se usa para el diagrama de interacción, el **eje local 2 del pier en ETABS (flecha verde)** debe apuntar hacia **+Y (arriba)**.
- Si en ETABS el eje 2 apunta a +X (derecha), se **rota la sección 90°** antes de extraer las coordenadas.
- Así las curvas a 0° y 90° corresponden a los momentos M2 y M3 de ETABS.
- `⚠ VERIFICAR`: ¿cuál curva (0° o 90°) se compara con M2 y cuál con M3? No está escrito explícitamente.
- `⚠ VERIFICAR`: ¿el sentido de giro (horario o antihorario) importa en secciones que no son simétricas, como la L?

## 3. Origen de coordenadas `[JSON P2, paso 3]`
- La esquina inferior izquierda de la sección se lleva a (0, 0). Las coordenadas de vértices y barras se trabajan en cm.

## 4. Armado final de P2, como ejemplo de resultado `[JSON P2, paso 5]`
| Zona | Acero longitudinal | Confinamiento |
|---|---|---|
| Esquina (núcleo central) | 12Ø3/4" | 1Ø3/8" + 2Ø3/8" @ .10 |
| Extremo izquierdo/inferior | 8Ø5/8" | Ø3/8" @ .10 |
| Extremo derecho/superior | 8Ø3/4" | Ø3/8" @ .10 |
| Alma del tramo más exigido | Ø1/2" @ .175 / @ .10 | — |
| Alma del otro tramo | Ø3/8" @ .175 | — |
| Ganchos | Ø3/8" @ .30 | — |

`⚠ VERIFICAR`: el resumen dice "Ø1/2" @ .175 / @ .10". ¿Dónde va cada espaciamiento?

## 5. Lo que confirman los archivos de `grabacion/`
- `Diseño/P1 COMPLETE.xlsx` y `P2 COMPLETE.xlsx`: libros de verificación de las secciones completas.
- `EXCEL DATAEX/PL2B.xlsx` y `PL2COMPLETE.xlsx`: coordenadas de barras extraídas de AutoCAD; la conversión es m → cm (×100).
- En P2 COMPLETE, el nivel verificado es N2, Lm = 150 y b = 30 en la hoja de cálculo.
- ⚠ En P2 COMPLETE falta la combinación **Comb3 Min** (ver [hallazgos_excel.md](../referencias/hallazgos_excel.md) #1).

---
## Registro de cambios
- 2026-11-26: creado desde el JSON P2.
- 2026-11-26: se agregó lo que confirman los archivos de `grabacion/`.
