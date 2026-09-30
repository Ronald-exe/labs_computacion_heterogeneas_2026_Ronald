# Práctica de Clase #6 — Programación en CUDA: suma de vectores, producto punto y softmax

## Ronald Duarte Barrantes : 2021004089 | Computación Heterogénea

## Plataforma de pruebas

| Característica | Valor |
|---|---|
| Equipo | Lenovo ThinkPad E14 Gen 4 |
| Procesador | Intel i7-1255U |
| Arquitectura | x86-64 |
| Set vectorial | AVX2 (sin AVX512) |
| Núcleos / hilos | 10 (2P+8E) / 12 |
| Frecuencia base/turbo | 1.7 GHz / 4.7 GHz |
| Tipo de RAM | DDR4-3200 |
| Capacidad RAM | 16 GB |
| L1 Instrucciones | 576 KiB (10 inst.) |
| L1 Datos | 352 KiB (10 inst.) |
| L2 | 6.5 MiB (4 inst.) |
| L3 (LLC) | 12 MiB (1 inst.) |
| Sistema operativo | Pop!_OS 24.04 |
| GPU (Google Colab) | NVIDIA Tesla T4 |
| Memoria GPU | 15360 MiB (16 GB GDDR6) |
| Potencia máxima GPU | 70 W |
| Driver NVIDIA / CUDA | 580.82.07 / 13.0 |
| Compilador | nvcc 12.8 (V12.8.93) |

Nota: la laptop solo tiene una Intel Iris Xe, así que los programas CUDA se compilaron y ejecutaron en Google Colab con la Tesla T4.

---

## Ejercicio A: suma de vectores (`vector-add/`)

Modificación: se completó el kernel con `c[i] = a[i] + b[i];`.

| N | Bloques | Resultado |
|---|---|---|
| 1048576 | 4096 | OK |
| 2097152 | 8192 | OK |

```
./vector_add 1048576
vector-add n=1048576: OK
./vector_add 2097152
vector-add n=2097152: OK
```

1. **¿Cuántos bloques se lanzan cuando N=1048576 y cada bloque tiene 256 hilos?**

   1048576 / 256 = **4096** bloques.

2. **¿Qué ocurre si N no es múltiplo del tamaño del bloque?**

   Se lanza un bloque extra con hilos sobrantes, la condición `i < n` evita que esos hilos accedan fuera de los arreglos.

3. **¿Qué transferencias de memoria ocurren entre CPU y GPU?**

   A y B se copian de CPU a GPU, y el resultado C se copia de GPU a CPU para verificarlo.

---

## Ejercicio B: producto punto (`dot-product/`)

Modificación: se completó el producto `value = a[i] * b[i];` y la reducción `cache[tid] += cache[tid + stride];`.

`__syncthreads()` es una barrera que asegura que todos los hilos del bloque terminen de escribir en memoria compartida antes de que otro hilo lea esos valores.

| N | Parciales | GPU | CPU | Error | Resultado |
|---|---|---|---|---|---|
| 1048576 | 4096 | -21.250000 | -21.250000 | 0.000000 | OK |
| 4194304 | 16384 | -0.500000 | -0.500000 | 0.000000 | OK |

```
./dot_product 1048576
dot-product n=1048576: gpu=-21.250000 cpu=-21.250000 error=0.000000 OK
./dot_product 4194304
dot-product n=4194304: gpu=-0.500000 cpu=-0.500000 error=0.000000 OK
```

1. **¿Por qué este ejercicio no puede resolverse solamente escribiendo un valor independiente por hilo?**

   Porque el resultado es un solo valor que depende de todos los elementos, se necesita una reducción donde los hilos combinen sus resultados.

2. **¿Cuántos valores parciales se copian de GPU a CPU?**

   Uno por bloque: **4096** para N = 1048576 y **16384** para N = 4194304.

3. **¿Qué pasaría si se elimina alguna sincronización dentro de la reducción?**

   Habría condición de carrera, un hilo podría leer un valor que aún no se ha actualizado y el resultado sería incorrecto.

---

## Ejercicio C: softmax (`softmax/`)

Modificación: se completaron la reducción del máximo, el cálculo de `expf(input[idx] - row_max)`, la reducción de la suma y la normalización. También se agregó un `__syncthreads()` después de leer `row_max`, ya que sin este el hilo 0 podía sobrescribir `cache[0]` antes de que los demás hilos leyeran el máximo.

| Rows | Cols | Resultado |
|---|---|---|
| 128 | 1024 | OK |
| 256 | 2048 | OK |

```
./softmax 128 1024
softmax rows=128 cols=1024: OK
./softmax 256 2048
softmax rows=256 cols=2048: OK
```

1. **¿Por qué se calcula primero el máximo de cada fila?**

   Para evitar que `expf` se desborde, al restar el máximo todos los exponentes quedan menores o iguales a cero.

2. **¿Qué partes del algoritmo requieren cooperación entre hilos del mismo bloque?**

   La reducción del máximo y la reducción de la suma de exponenciales.

3. **¿Qué limitación tiene usar un solo bloque por fila cuando `cols` crece mucho?**

   Un bloque tiene un máximo de hilos y corre en un solo SM, entonces cada hilo procesa más elementos en serie y no se aprovecha el resto de la GPU.
