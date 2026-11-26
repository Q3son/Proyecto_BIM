# Hallazgos al revisar los Excel (2026-11-26)

Son observaciones de Claude al leer las fórmulas. **No se corrigió nada en los archivos originales.**

| # | Archivo / celda | Hallazgo | Impacto |
|---|---|---|---|
| 1 | P2 COMPLETE.xlsx → ETABS SELECCIÓN Q13 | Dice "Comb3 SQ **Max**" (duplicado de Q12). Debería ser "Comb3 SQ **Min**". | **La combinación Comb3 Min no se está verificando** en el diagrama de interacción ni en el cortante de P2. En la plantilla V3.0 está bien ("Comb3 Min"). |
| 2 | PREDIMENSIONAMIENTO → M. Rectangulares Q17, Q18 | La c de ETABS (55 cm) se ingresa, pero ninguna fórmula la usa. La hoja calcula su propia c (M38). | Cuál c manda: definir. |
| 3 | PREDIMENSIONAMIENTO → D9 | Lm = 180, pero el pier es P2b, que en el video mide 150 cm. El nombre del archivo dice P4A. | El estado guardado no corresponde al video (Leb_i = 33.26, no 31.43). |
| 4 | PREDIMENSIONAMIENTO → M87 vs M89 | s_max de estribos = 9.53 cm, y se adoptó s_real = 10 cm, sin aviso. | Posible incumplimiento que no se reporta. |
| 5 | PREDIMENSIONAMIENTO → P121 | El núcleo en la dirección vertical da "Aumentar ramales o diametro estribo" (4.26 < 5.33 cm²). | El archivo quedó con una verificación que no cumple. |
| 6 | Diseño → CSI COL R7 | Nota del autor: faltan corregir los signos de momentos y el diseño por corte. | El criterio de corte puede no ser el definitivo. |
| 7 | Diseño → HOJA DE CÁLCULO, corte | No hay límite de Vn, ni cuantía o espaciamiento máximo horizontal, ni control de Vs ≤ 0. | Hay que definir si se agregan. |
| 8 | Tablas de áreas | PRE usa áreas redondeadas (5/8" = 1.99) y DIS usa π·d²/4 (1.979). | Diferencias pequeñas; para reproducir las hojas se respeta cada tabla. |
| 9 | Diseño → CSI COL | φ escalonado: 0.75 en compresión y 0.9 en tracción, sin transición. | Confirmar que coincide con lo que usa CSiCOL. |

Archivos que Claude **no** pudo leer: `.CDB` (CSiCOL), `.dwg` (AutoCAD) y `.EDB` (ETABS). Para usarlos hace falta exportarlos: los diagramas de CSiCOL ya vienen en las hojas "CSI COL"; para AutoCAD, exportar a **DXF**.
