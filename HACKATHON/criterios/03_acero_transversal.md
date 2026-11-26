# Criterio: diseño del acero transversal

> **ESTADO: TRANSCRITO DE [PRE] (confinamiento) y [DIS]/[P2C] (corte)** (pendiente de que el usuario lo confirme).
> ⚠ Nota del propio usuario en [DIS] "CSI COL" R7: **"OJO FALTA CORREGIR LO DEL DISEÑO POR CORTE"**.

## 1. Diseño por cortante del alma [DIS filas 86-157]
Se hace en **dos direcciones**. Mn y Mua se **leen a mano del diagrama de interacción**.

| | Dirección 3‑3 (usa V33) | Dirección 2‑2 (usa V22) |
|---|---|---|
| Vu act | max \|V33\| de las 18 combinaciones | max \|V22\| de las 18 combinaciones |
| Mn | del diagrama a **90°** (N75) | del diagrama a **0°** (N59) |
| Mua | momento actuante que se elige (N74) | momento actuante que se elige (N58) |
| R | 6 | 6 |
| b | N108 = longitud de la sección (N9) | N146 = espesor (N10) |
| d | N109, **a mano** (en DIS = 25 − 5; en P2C = 25) | N147 = 0.8·longitud (N9) |

Fórmulas (las mismas en ambas direcciones):
```
Vu_diseño = Vu_act · min( Mn/Mua , R )
Vc  = 0.53·√f'c · b · d / 1000                 [tonf]
Vs  = Vu_diseño / 0.85 − Vc                     [tonf]
Av  = ramales · Ab                              [cm²]   (Ab con la tabla de DIS: π·d²/4)
s   = Av · fy · d / (Vs·1000)                   [cm]
```
- Datos que se ingresan: ramales (p. ej. 2) y diámetro (3/8" o 1/2").
- Ejemplo P2C, dirección 2‑2: Vu = 22.84; Mn/Mua = 200/94 = 2.13; Vu_d = 48.60; Vc = 35.70; Vs = 21.48; Av = 2·1.267; s = 59.4 cm.
- Ejemplo P2C, dirección 3‑3: Vu = 10.37; Mn/Mua = 250/54 = 4.63; Vu_d = 48.0; Vc = 37.18; Vs = 19.28; s = 13.8 cm.

**Lo que la hoja NO verifica** (Claude no lo agrega sin tu criterio, solo lo señala):
- Límite máximo de Vn o Vs.
- Cuantía horizontal mínima ni espaciamiento máximo.
- Qué hacer si Vs ≤ 0 (la fórmula daría un s negativo).
- `⚠ VERIFICAR`: ¿cómo pasas de "s calculado" al espaciamiento final (p. ej. 31 → 30 o 20 cm; 59.4 → ?), y qué tope de espaciamiento usas?
- `⚠ VERIFICAR`: en la dirección 3‑3 (fuera del plano del muro) se usa b = longitud y d = espesor − rec. ¿Es intencional diseñar el alma también fuera del plano?
- `⚠ VERIFICAR`: ¿Mua es el momento de la misma combinación que da Vu, o el máximo?

## 2. Confinamiento del núcleo [PRE L69:S121]
Se calcula por núcleo (NÚCLEO 01 y 02).

**Separación horizontal entre barras restringidas:**
```
hx_max = min( 2·b/3 , 35 )        [cm]
hx_real: se ingresa a mano (ej. 11)
```
`⚠ VERIFICAR`: la hoja no compara hx_real con hx_max. ¿Se verifica a ojo?

**Espaciamiento vertical de estribos:**
```
so    = 10 + (35 − hx_real)/3
s_max = min( 6·db_long , b/3 , so )
s_real: se ingresa a mano (ej. 10)
```
Ejemplo: db = 5/8" (1.5875) → min(9.53; 10; 18) = **9.53 cm**, y se adoptó **s_real = 10 cm**.
- ⚠ **Hallazgo:** en el ejemplo, s_real (10) > s_max (9.53), y la hoja no lo marca. `⚠ VERIFICAR` si aceptas esa tolerancia o es un descuido. Claude, por defecto, reportará **NO CUMPLE** cuando s_real > s_max.

**Área de estribos** (en cada dirección del núcleo):
```
bc = b − 4 ;  hc = h − 4       [cm]   (b y h del núcleo, ingresados a mano; ej. 25 × 75)
Ag = b·h ;  Ach = bc·hc
Av = ramales · Ab
Av1 = 0.3·(Ag/Ach − 1)·(f'c/fy)·s·bc
Av2 = 0.09·s·bc·f'c/fy
CUMPLE si Av ≥ max(Av1, Av2); si no: "Aumentar ramales o diámetro estribo"
```
Ejemplo: en la dirección horizontal, 3 ramales Ø3/8" → 2.13 ≥ 1.575, CUMPLE. En la vertical, 6 ramales → 4.26 < 5.325, **NO CUMPLE** (así quedó guardado el archivo).
- `⚠ VERIFICAR`: el "−4" de bc y hc, ¿es 2 × 2 cm de recubrimiento al estribo?
- `⚠ VERIFICAR`: la hoja usa b = 25 y h = 75 para el núcleo de una placa de 30 cm con Leb = 40. ¿Son las dimensiones del núcleo al eje del estribo? ¿Cómo se definen?

## 3. Notación en el plano (del resumen P4A y P2)
`1Ø1/2"@0.175`, `1Ø3/8"@0.20`, `16Ø3/4"`, `1 []Ø3/8"@.10`, ganchos `Ø3/8"@.30`.

---
## Registro de cambios
- 2026-11-26: plantilla creada; luego transcrita desde los JSON.
- 2026-11-26: **reescrito con las fórmulas de [PRE] y [DIS]/[P2C]**.
