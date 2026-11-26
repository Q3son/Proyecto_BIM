# Criterios generales

> **ESTADO: TRANSCRITO DE LOS EXCEL (pendiente de que el usuario lo confirme).**
> Fuentes (en `grabacion/`):
> - **[PRE]** `PREDIMENSIONAMIENTO/PREDIMENSIONAMIENTO P4A.xlsx`, hojas DATOS DE ETABS, COMBINACIONES y M. Rectangulares.
> - **[DIS]** `Diseño/22. Diseño de Muros Estructurales - Flexion corte V3.0.xlsx` (plantilla).
> - **[P2C]** `Diseño/P2 COMPLETE.xlsx` (el mismo libro, lleno con la placa P2 completa).
> Lo marcado `⚠ VERIFICAR` lo interpretó Claude o es una inconsistencia que hay que confirmar.

## 1. Normativa
- E.060 Concreto Armado (Perú). [DIS] Título: "DISEÑO DE COLUMNAS SEGÚN NORMA E-060".

## 2. Unidades
| Magnitud | Unidad | Fuente |
|---|---|---|
| P, V | tonf | [PRE] DATOS DE ETABS fila 2 |
| M, T | tonf·m | [PRE] |
| f'c, fy, esfuerzos de verificación | kg/cm² | [PRE] D5, D6 |
| Lm, em, c, longitudes de núcleo, espaciamientos | cm | [PRE] D9, D10 |
| Hm (altura total) | **m** | [PRE] D11 (34.5) |
| δu | cm | [PRE] D12 |
| σ de compresión de ETABS | Ton/m² → se divide entre 10 para pasar a kg/cm² | [PRE] M44 = M20/10 |
| Coordenadas en AutoCAD | m → ×100 = cm | DATAEX (F2 = C2*100) |

## 3. Materiales [PRE]
- f'c y fy: los ingresa el usuario. En P2: 350 y 4200 kg/cm².
- Ec = 15100·√f'c (G5). Es = 2 000 000 kg/cm² (G6).
- **β1** (G10): 0.85 si f'c ≤ 280; 0.65 si f'c ≥ 560; entre esos valores se interpola linealmente. Con f'c = 350, β1 = 0.80.
- Øf = 0.9 y Øc = 0.85 (G11, G12). `⚠ VERIFICAR`: no encontré ninguna fórmula de PRE que use G11 o G12.

## 4. Barras: tabla de áreas
Las dos hojas usan tablas **distintas**:

| Barra | [PRE] área (cm²) | [DIS] área = π·d²/4 (cm²) | d (cm) |
|---|---|---|---|
| 1/4" | 0.32 | 0.317 | 0.635 |
| 8 mm | 0.50 | 0.503 | 0.80 |
| 3/8" | 0.71 | 0.7126 | 0.9525 |
| 12 mm | 1.13 | 1.131 | 1.20 |
| 1/2" | 1.27 | 1.2668 | 1.27 |
| 5/8" | 1.99 | 1.9793 | 1.5875 |
| 3/4" | 2.85 | 2.8502 | 1.905 |
| 1" | 5.10 | 5.0671 | 2.54 |

- **Regla para Claude:** en el predimensionamiento se usa la tabla de PRE; en la verificación final y el cortante, la de DIS. Así se reproducen los resultados de tus hojas.
- Espaciamientos estándar que lista PRE (P3:P10): **7.5, 10, 12.5, 15, 17.5, 20, 22.5, 25 cm**.

## 5. Recubrimiento
- [DIS]/[P2C] rec = 5 cm (N11). En PRE no aparece.
- En AutoCAD se dibujan offsets de 0.05 y 0.03 m (según el video). `⚠ VERIFICAR` qué representa cada uno.

## 6. Casos de carga y combinaciones

### 6.1 Predimensionamiento [PRE, COMBINACIONES]
Los casos que se leen de ETABS son CM, CV, SDX AMP Max y SDY AMP Max. El sismo se toma **solo como "Max"** (envolvente) y el signo se aplica con el factor ±1:

| Comb | Fórmula |
|---|---|
| COMB 1 | 1.4 CM + 1.7 CV |
| COMB 2 / 3 | 1.25 CM + 1.25 CV ± 1.0 SDX AMP |
| COMB 4 / 5 | 1.25 CM + 1.25 CV ± 1.0 SDY AMP |
| COMB 6 / 7 | 0.9 CM ± 1.0 SDX AMP |
| COMB 8 / 9 | 0.9 CM ± 1.0 SDY AMP |

- Los momentos sísmicos M2 y M3 se multiplican por el **signo** que el usuario elige en "M. Rectangulares" D17 y D18 (+ o −).
- Se lee **un solo nivel y una sola ubicación** (D15 = N1, D16 = Bottom en el ejemplo).

### 6.2 Verificación final [DIS / P2C]
Las combinaciones vienen ya calculadas desde ETABS (Pier Forces): Comb1, Comb2 Max/Min, Comb3 Max/Min, Comb4 Max/Min, Comb5 Max/Min, cada una en Top y Bottom del nivel elegido. Los textos de la hoja son:
- U1 = 1.4 CM + 1.7 CV
- U2 = 1.25(CM+CV) + 1.0 CSX
- U3 = 1.25(CM+CV) + 1.0 CSY
- U4 = 0.9 CM + 1.0 CSX
- U5 = 0.9 CM + 1.0 CSY

En P2C los nombres llevan "SQ" (p. ej. "Comb1 SQ"). Los nombres tienen que coincidir **exactamente** con los de ETABS.

### 6.3 Convención de signos [DIS]
- P en ETABS es negativa en compresión. Al pasar a ETABS DEFINITIVO se cambia el signo (P = −P_ETABS), así que la compresión queda positiva.
- Los signos de M2 y M3 los elige el usuario (+/−).
- **Nota del propio usuario en la hoja CSI COL (R7):** "TODAVIA HAY QUE CORREGIR AQUÍ EL TEMA DE LOS DIAGRAMAS Y LOS MOMENTOS … EL TEMA DE SIGNOS … Y OJO FALTA CORREGIR LO DEL DISEÑO POR CORTE." `⚠ VERIFICAR`: ¿esto sigue pendiente?

### 6.4 Placa "redundante" [DIS O17]
Si "REDUNDANTE?" = SI, **todas** las fuerzas (P, M, V) se multiplican por 1.25. En P2: NO.

## 7. Redondeo
- Espaciamientos: se elige un valor de la lista estándar menor o igual al calculado (p. ej. 18.93 → 17.5).
- Longitud de núcleo: ver [01](01_predimensionamiento_nucleos.md).

---
## Registro de cambios
- 2026-11-26: plantilla creada; luego transcrita desde los JSON.
- 2026-11-26: **reescrito desde las fórmulas de los Excel** [PRE], [DIS] y [P2C].
