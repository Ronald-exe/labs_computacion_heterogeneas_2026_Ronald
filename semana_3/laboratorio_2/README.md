# Resultados - Multiplicación de Matrices 2048x2048 (SIMD) - Laboratorio 2

## Ronald Duarte Barrantes : 2021004089

## AVX2

| Métrica | Valor |
|---|---|
| Tamaño matriz | 2048x2048 |
| Repeticiones | 1 |
| Checksum | 86972906452.000000 |
| C[0][0] | 26800.500000 |
| C[1023][1023] | 26836.500000 |
| Operaciones | 17179869184 |
| Tiempo | 1.712993 s |
| Rendimiento | 10.029152 GFLOP/s |

## Escalar

| Métrica | Valor |
|---|---|
| Tamaño matriz | 2048x2048 |
| Repeticiones | 1 |
| Checksum | 86972906452.000000 |
| C[0][0] | 26800.500000 |
| C[1023][1023] | 26836.500000 |
| Operaciones | 17179869184 |
| Tiempo | 3.879003 s |
| Rendimiento | 4.428939 GFLOP/s |

## Análisis
Como se nota, el paralelismo de la operación vectorial, el cual opera en 8 floats ideales, permite que la operación de multiplicación sea más rápida; en contraparte, la operación de multiplicación con el escalar es más lenta debido a la alta serialización que tiene.

Esto se puede justificar mediante la gráfica de la Ley de Amdahl:


$$P = \frac{1 - 0.441553}{0.875} = \frac{0.558447}{0.875} = 0.638225$$

Notamos que la parte vectorial contempla un 63.82% de paralelismo, lo que puede mejorarse optimizando más el código, pero la parte escalar es mayormente serial, por eso es más eficiente la multiplicación vectorial.

Eso lo notamos tambien en la diferencia de tiempo de ejecucion: 

AVX2 vs escalar: (1.712993 s → 3.879003 s) siendo el AVX2 **2.26** mas rapido que el escalar.

**Resultado:**

| Variable | Valor |
|---|---|
| P (fracción vectorizada) | 0.638225 (**63.82%**) |