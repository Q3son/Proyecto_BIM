# Criterio: diseño del acero longitudinal (vertical)

> **ESTADO: TRANSCRITO DE [PRE] y [DIS]/[P2C]** (pendiente de que el usuario lo confirme).

## 1. Acero en los extremos (núcleo), estimación [PRE M26]
```
As_ext = (ρreq − ρmin) · em · Lm / 2        [cm²]
```
Ejemplo: (0.0051 − 0.0025)·30·180/2 = 7.02 cm².
Luego se elige un arreglo de la lista B52:C64 con área ≥ As_ext. Las opciones son 6/8/10/12 barras de 5/8", 3/4" o 1", y 20Ø3/4". Ejemplo: 6Ø5/8" = 11.94 cm².
- `⚠ VERIFICAR`: ¿cómo eliges entre arreglos? ¿El de menor área que cumpla, o el que llene mejor el núcleo?

## 2. Acero vertical del alma [PRE P23:Q29]
```
As_alma = 0.0025 · 100 · em                     [cm²/m]   (Q26)
s_inicial = Ab · capas · 100 / As_alma           [cm]      (Q27)
s_final = elegido a mano (Q28)
ρv = capas · Ab / (s_final · em)                            (Q29)
```
- Datos que se ingresan: diámetro (Q23, p. ej. 3/8") y número de capas (Q24 = 2).
- Ejemplo: 7.5 cm²/m; s = 0.71·2·100/7.5 = 18.93 → s_final = 17.5 → ρv = 0.00270.
- ⚠ Nota: As_alma usa la **cuantía mínima 0.0025 fija**, no ρreq.
- `⚠ VERIFICAR`: regla para s_final. Claude asume "el valor de la lista estándar (7.5, 10, …, 25) inmediatamente menor o igual a s_inicial", hasta que lo confirmes.

## 3. Cuantía mínima en los bordes [PRE L57:O67]
```
Zona de borde = 0.15·Lm
ρmin_borde = 1.6·√f'c / fy
As_borde = As_núcleo (M61) + As adicional (O61) + acero de alma dentro de la zona (M62, lista F52:G60)
ρ_borde = As_borde / (em · 0.15·Lm)
CUMPLE si ρ_borde ≥ ρmin_borde; si no: "AUMENTAR CUANTÍA DE BORDE"
```
Ejemplo: 0.15·180 = 27 cm; ρmin = 1.6·√350/4200 = 0.00713.
- En el archivo guardado esta verificación está **sin llenar** (#N/A).

## 4. Distribución geométrica (AutoCAD, según el video)
1. Primero las barras de esquina, luego las del núcleo y al final las del alma, **centradas simétricamente** entre núcleos.
2. Separación libre máxima de **15 cm** entre barras longitudinales sujetas por esquina de estribo o gancho (del resumen P4A).
3. La separación horizontal entre barras restringidas del núcleo (hx) se limita según [03](03_acero_transversal.md) §2.

## 5. Verificación por flexocompresión [DIS / P2C]

### 5.1 Diagrama de interacción
- Se obtiene **de CSiCOL ya como curva de diseño (φ aplicado)**, a 0° y 90°, y se pega en la hoja "CSI COL" (A8 en adelante: Point, Load, Moment).
- La hoja calcula la curva **nominal** dividiendo entre φ:
  ```
  φ = 0.75 si Load > 0 (compresión);  φ = 0.9 si Load ≤ 0 (tracción)
  Pn = Pφ/φ ;  Mn = Mφ/φ
  ```
  `⚠ VERIFICAR`: ¿φ = 0.75 en compresión es lo que usa tu CSiCOL? En E.060 y ACI, φ para elementos con estribos es 0.70 o 0.65, y entre compresión y tracción hay una transición. Dividir por un φ escalón es una aproximación. **Claude no cambiará esto sin tu confirmación.**
- La curva se refleja (M → −M) para graficar ambos lados.

### 5.2 Demandas
- Se toman de ETABS DEFINITIVO: 9 combinaciones (Comb1 y Comb2 a Comb5 Max/Min) × Top/Bottom = **18 puntos** por nivel.
- Para el gráfico a 0° se usan (P, M22); para el de 90°, (P, M33). Así está en la hoja: columnas B:E son "EJE Y‑Y" con P, M33, V22, y F:I son "EJE X‑X" con P, M22, V33.
  - `⚠ VERIFICAR`: confirmar qué momento va con qué curva. Depende de la orientación del eje local 2 (ver [04](04_placas_irregulares.md) §2).

### 5.3 Criterio de aceptación
Todos los puntos (Pu, Mu) deben quedar **dentro de la curva de diseño** (azul), a 0° y a 90°.

### 5.4 Qué nivel se verifica
- Se elige uno en "Story" (N32). En DIS: N3; en P2C: N2; en el video: N1.
- `⚠ VERIFICAR`: ¿se verifica cada nivel donde cambia el armado, o solo el crítico?

## 6. Si no cumple: cómo iterar (del video P2)
1. Ver en qué dirección falla.
2. Aumentar el diámetro de los núcleos (#5 → #6).
3. Aumentar el diámetro del alma del tramo que controla (#3 → #4) y/o reducir s (0.175 → 0.10).
4. Agregar barras puntuales en los bordes.
5. Volver a generar el diagrama y verificar de nuevo.
Claude presenta cada iteración como **propuesta**, y reporta también las que fallaron.

## 7. Variación en altura, empalmes y anclajes
`PENDIENTE`. No aparece en ningún Excel.

---
## Registro de cambios
- 2026-11-26: plantilla creada; luego transcrita desde los JSON.
- 2026-11-26: **reescrito con las fórmulas de [PRE] y [DIS]/[P2C]**.
