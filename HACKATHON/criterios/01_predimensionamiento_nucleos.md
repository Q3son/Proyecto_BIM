# Criterio: predimensionamiento de núcleos (elementos de borde)

> **ESTADO: TRANSCRITO DE [PRE] "M. Rectangulares"** (pendiente de que el usuario lo confirme).
> Las celdas se citan como en el Excel. Nombres definidos: `fcc` = D5 (f'c), `fy` = D6, `lm` = D9 (Lm), `em` = D10 (espesor), `hm` = D11 (Hm, en m).

## 1. Datos de entrada
| Celda | Dato | Ejemplo (estado guardado del archivo) |
|---|---|---|
| D5, D6 | f'c, fy (kg/cm²) | 350, 4200 |
| D9 | Lm, longitud del muro o tramo (cm) | 180 ⚠ (el video usó 150 para P2b) |
| D10 | em, espesor (cm) | 30 |
| D11 | Hm, altura total (m) | 34.5 |
| D12 | δu, desplazamiento en el tope (cm), con el sismo amplificado en la dirección del tramo | 4.6 |
| D15, D16 | Nivel y ubicación de diseño | N1, Bottom |
| D17, D18 | Signo de M2 y M3 | +, + |
| M14 | **Combinación crítica elegida a mano** | COMB 3 |
| M17 | ρreq (reporte ETABS Shear Wall Design) | 0.0051 |
| M18 | ρmin | 0.0025 |
| Q17, Q18 | c izq / c der de ETABS | 55 / 55 |
| M20 | σ comp de ETABS (Ton/m²) | 852 |
| M32/O32, M33/O33 | Acero supuesto en el núcleo a tracción (As) y a compresión (A's), elegido de la lista B52:B64 | 6Ø5/8" / 6Ø5/8" |

> ⚠ **Importante:** **Q17 y Q18 (c de ETABS) no los usa ninguna fórmula.** La hoja **calcula su propia c** (M38). El valor de c que da ETABS es solo referencial. `⚠ VERIFICAR`.
> ⚠ La combinación crítica (M14) se elige a mano. `⚠ VERIFICAR`: ¿con qué criterio? En el ejemplo es la de mayor compresión (COMB 3, P = −339.6 t).

## 2. Profundidad del eje neutro, c (M38)
```
c = [ (−Pu·1000) + As·fy + ρv·em·Lm·fy − A's·fy ] / [ 0.85·f'c·em·β1 + 2·ρv·em·fy ]
```
- Pu = P de la combinación crítica (N14, en tonf, negativa en compresión; por eso −Pu·1000 da kg de compresión).
- As = R32 + S32 y A's = R33 + S33 (cm², de la tabla de PRE).
- ρv = Q29, la cuantía vertical del alma ya adoptada (ver [02](02_acero_longitudinal.md) §2).
- Ejemplo guardado: c = 51.26 cm.

## 3. ¿Necesita elemento de borde? Dos criterios
**3a. Por desplazamiento** (M40, L42):
```
c_lim = Lm / ( 600 · max(0.005, δu / (Hm·100)) )
Necesita EB si c ≥ c_lim
```
Ejemplo: 180 / (600 · max(0.005; 4.6/3450 = 0.00133)) = 180/3 = 60 cm. Como 51.26 < 60, **no necesita**.

**3b. Por esfuerzos** (M44, M45, L47):
```
σ = σ_ETABS / 10     (Ton/m² → kg/cm²)
σ_lim = 0.2·f'c
Necesita EB si σ ≥ σ_lim
```
Ejemplo: 85.2 ≥ 70, **necesita**.

- `⚠ VERIFICAR`: la hoja muestra los dos resultados por separado. ¿Basta con que **uno** diga "necesita" para colocar núcleo confinado? En el ejemplo uno dice no y otro sí, y se colocó.

## 4. Longitud del elemento de borde
**Inicial** (M52):
```
Leb_i = max( c/2 , c − 0.1·Lm )
```
Ejemplo: max(25.63; 33.26) = 33.26 cm.

**Adoptada** (M53): **se ingresa a mano**. Ejemplo: 40 cm.
- Video P2b: 31.43 → 30 → 40 cm (el retoque fue por la distribución de barras en la esquina de la L).
- Resumen P4A: 48 → 40 cm.
- `⚠ VERIFICAR — regla de adopción`: ¿se puede adoptar un valor **menor** que Leb_i, como 48 → 40 o 31.43 → 30? ¿Con qué tolerancia, o hay un mínimo absoluto? **Hasta que lo aclares, Claude adopta Leb ≥ Leb_i**, redondeado hacia arriba al múltiplo de 5 cm, y lo marca como propuesta.

## 5. Siguiente paso indicado en la hoja (M54)
> "Dibujar placa en CSI COL, de aquí sacar el acero que se encuentra dentro de 0.15Lm"

Ver la verificación de cuantía de borde en [02](02_acero_longitudinal.md) §3.

## 6. Placas irregulares
Ver [04_placas_irregulares.md](04_placas_irregulares.md). Este predimensionamiento se hace **tramo por tramo**, con la hoja "M. Rectangulares".

---
## Registro de cambios
- 2026-11-26: plantilla creada; luego transcrita desde los JSON.
- 2026-11-26: **reescrito con las fórmulas de [PRE]**.
