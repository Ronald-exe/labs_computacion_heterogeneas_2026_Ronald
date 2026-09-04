# Práctica de Clase #3 y #4 — Datos y plantilla de informe

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

`gcc 15.2.0` | `nproc` = 12

---

## Práctica 3 — Ejercicio A: cpu-naive vs cpu-affinity

Modificación: `NUM_THREADS` (constante) → parametrizado por argumento (`./cpu-naive <n>`, `./cpu-affinity <n>`).

| Hilos | cpu-naive real (s) | cpu-affinity real (s) |
|---|---|---|
| 1  | 4.600 | 4.599 |
| 2  | 4.739 | 4.782 |
| 3  | 5.830 | 4.781 |
| 4  | 5.947 | 4.820 |
| 5  | 6.537 | 5.860 |
| 6  | 6.682 | 6.087 |
| 7  | 6.856 | 6.184 |
| 8  | 7.029 | 6.284 |
| 9  | 7.243 | 6.304 |
| 10 | 7.394 | 6.397 |
| 11 | 7.450 | 6.560 |
| 12 | 7.718 | 7.700 (repetición; primera corrida: 9.912, outlier) |

| Hilos | naive speedup | aff speedup | naive eff | aff eff |
|---|---|---|---|---|
| 1  | 1.000 | 1.000 | 1.000 | 1.000 |
| 2  | 0.971 | 0.962 | 0.485 | 0.481 |
| 3  | 0.789 | 0.962 | 0.263 | 0.321 |
| 4  | 0.773 | 0.954 | 0.193 | 0.239 |
| 5  | 0.704 | 0.785 | 0.141 | 0.157 |
| 6  | 0.688 | 0.756 | 0.115 | 0.126 |
| 7  | 0.671 | 0.744 | 0.096 | 0.106 |
| 8  | 0.654 | 0.732 | 0.082 | 0.091 |
| 9  | 0.635 | 0.730 | 0.071 | 0.081 |
| 10 | 0.622 | 0.719 | 0.062 | 0.072 |
| 11 | 0.617 | 0.701 | 0.056 | 0.064 |
| 12 | 0.596 | 0.597 | 0.050 | 0.050 |

Gráficos: `grafico_tiempo.png`, `grafico_speedup.png`, `grafico_eficiencia.png`

---

## Práctica 3 — Ejercicio B: matmul_tiled_openmp vs softmax_openmp

matmul: matriz 512x512, tile 32, 50 repeticiones. softmax: NUM_ELEMENTS=1000 (fijo en código), 100000 repeticiones. Tiempos reportados por la propia aplicación (`omp_get_wtime`).

| Hilos | matmul (s) | softmax (s) |
|---|---|---|
| 1  | 4.774 | 1.136 |
| 2  | 2.459 | 2.494 |
| 3  | 2.103 | 3.182 |
| 4  | 1.957 | 3.324 |
| 5  | 1.486 | 4.330 |
| 6  | 1.265 | 4.346 |
| 7  | 1.187 | 4.706 |
| 8  | 1.127 | 5.197 |
| 9  | 1.010 | 5.907 |
| 10 | 0.956 | 6.471 |
| 11 | 0.925 | 5.979 |
| 12 | 0.945 | 7.517 |

| Hilos | matmul speedup | softmax speedup | matmul eff | softmax eff |
|---|---|---|---|---|
| 1  | 1.000 | 1.000 | 1.000 | 1.000 |
| 2  | 1.941 | 0.455 | 0.971 | 0.228 |
| 3  | 2.270 | 0.357 | 0.757 | 0.119 |
| 4  | 2.439 | 0.342 | 0.610 | 0.085 |
| 5  | 3.213 | 0.262 | 0.643 | 0.052 |
| 6  | 3.774 | 0.261 | 0.629 | 0.044 |
| 7  | 4.022 | 0.241 | 0.575 | 0.034 |
| 8  | 4.236 | 0.219 | 0.530 | 0.027 |
| 9  | 4.727 | 0.192 | 0.525 | 0.021 |
| 10 | 4.994 | 0.176 | 0.499 | 0.018 |
| 11 | 5.161 | 0.190 | 0.469 | 0.017 |
| 12 | 5.052 | 0.151 | 0.421 | 0.013 |

Gráficos: `grafico_tiempo_b.png`, `grafico_speedup_b.png`, `grafico_eficiencia_b.png`

---

## Práctica 4 — Ejercicio A: bench-static

`./build/bin/bench-static 1000000 1000 1.0 2.0`

| Métrica | Total (µs) | Por iteración (µs) |
|---|---|---|
| fill A | 279810.742 | 279.811 |
| fill B | 269950.949 | 269.951 |
| add    | 867645.463 | 867.645 |
| total  | 1417408.737 | — |

Tamaño `libvectorops.a`: **1.8K**

---

## Práctica 4 — Ejercicio B: bench-dynamic

`./build/bin/bench-dynamic 1000000 1000 1.0 2.0`

| Métrica | Total (µs) | Por iteración (µs) |
|---|---|---|
| fill A | 615528.334 | 615.528 |
| fill B | 606738.935 | 606.739 |
| add    | 1149751.990 | 1149.752 |
| total  | 2372020.307 | — |

Tamaño `libvectorops.so`: **17K**

| Métrica (µs/iter) | static | dynamic |
|---|---|---|
| fill A | 279.811 | 615.528 |
| fill B | 269.951 | 606.739 |
| add    | 867.645 | 1149.752 |
| tamaño biblioteca | 1.8K | 17K |

---

## Plantilla — estructura del informe

1. **Introducción**

Este informe tiene como objetivo dar a comprender cómo la organización del hardware en programas que utilizan múltiples hilos y cómo aprovechar el hardware de una mejor manera para obtener toda la potencia del dispositivo, básicamente entender cómo implementar adecuadamente el paralelismo, para ello se analiza los efectos de alineamiento, multihilos y afinidad.

También en el laboratorio cuatro se denota la diferencia en cuanto al desempeño y el tamaño de los archivos generados al momento de utilizar bibliotecas dinámicas, estáticas y funciones de static line.

2. **Práctica 3 — Ejercicio A**

   En el inciso A, lo primero que se hizo fue una modificación en los códigos respectivos a este apartado, básicamente antes se tenían unos threads constantes #define NUM_THREADS 8, lo que se hizo fue una modificación utilizando malloc para los arreglos fijos que se encontraban en el documento, esto se hizo con la idea de facilitar al momento de cambiar los threads con los cuales va a correr el programa sin la necesidad de cambiar el código de manera repetitiva al momento que se quiera utilizar otra cantidad de hilos.

   Escalabilidad del programa: a medida que los threads van aumentando en el programa el tiempo también aumenta, esto nos indica que a pesar de que se utilicen más hilos para paralelizar el proceso este no está funcionando, es puede ser debido a que no hay condición de carrera ni sincronización entre ellos, todos los cores comparten el mismo bus de memoria y el mismo ancho de banda hacia la RAM, por lo cual entre más hilos haya esto hace que haya un tráfico mayor al cual darle respuestas, ya que cada uno de estos hilos compiten por un espacio en la RAM.

   Si lo analizamos con la ley de Amdahl observamos que a pesar de que el código intenta paralelizar el programa, este no aprovecha los hilos disponibles del CPU de manera adecuada, lo cual genera un comportamiento serial en los hilos, haciendo que a medida que estos aumenten más interrupción de comunicación entre los datos habrá, volviéndolo más lento.

   En cuanto a ambos programas, la eficiencia es pésima, esto lo podemos ver con los speedup/N, como se ven en las tablas, el N aumenta más rápido que el speedup, haciendo que la eficiencia disminuya a medida que se aumentan los hilos, generando en sí una contradicción en cuanto a los esperados al momento de usar más hilos. El affinity tiene una mejor eficiencia, esto puede ser debido a que el hilo se queda en el mismo core todo el tiempo haciendo que no tenga que buscar las task en otro proceso o hilo.

3. **Práctica 3 — Ejercicio B**

   Primero se debe tomar en cuenta una pequeña modificación para que me corriera el código, tuve que disminuir la cantidad de repeticiones de 10 mil a 50 repeticiones, ya que la computadora no me daba ningún resultado, esto es importante recalcar porque los resultados sw mult y softmax contemplan esta restricción para hacerlo justo.


    En cuanto a la escalabilidad del programa, a medida que aumentan la cantidad de hilos los tiempos de matmul mejora y en cambio los tiempos de softmax empeora, esto puede es debido a que el costo de la creación del hilo en el softmax lo perjudica más haya de ayudarlo, ya que tiene tres regiones paralelas, lo hilos no llegan a aportar mucho, pero si se deben gastar procesos al momento de crear el hilo y sincronizarlo, en cambio matmul sí utiliza los hilos de mejor manera con una sola región paralela que de verdad aprovecha los hilos, ya que estos tienen un mayor trabajo que hacer.

    En cuanto a la eficiencia de ambos programas, notamos que el matmul tiene una eficiencia mayor que el de softmax debido a la mejor utilización de hilos, pero también vemos que a medida que aumentamos los hilos tanto el matmul y el softmax van disminuyendo, esto debido a que hay un punto óptimo de la cantidad de hilos que son necesarios al momento de correr un programa, aumentar la cantidad de hilos a partir de ahí hace que la carga de trabajo de cada hilo sea menos significativa y que el costo de creación del hilo no valga la pena pero que ralentiza el proceso.

4. **Práctica 4 — Ejercicio A y B**

Dynamic es más lento porque se compila con -fPIC lo cual hace que el compilador tenga un menor capacidad para optimizar entre el archivo binario y la biblioteca que se desea llamar. Static, en cambio, se enlaza directo en el binario.

En cuanto a la comparación del .so con respecto al .a, resulta que el .so es mucho más grande tomando en cuenta que el .a es solo el código objeto y el .so es un binario ELF completo con tabla de símbolos y resulta que dynamic depende libvectorops.so