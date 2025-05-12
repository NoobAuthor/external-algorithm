# Algoritmo QuickSort: Informe Técnico

1.  Introducción
    El algoritmo QuickSort es un método de ordenamiento eficiente basado en la estrategia "divide y vencerás", desarrollado por Tony Hoare en 1959 y publicado en 1961. Es ampliamente reconocido por su rendimiento promedio superior y ha sido implementado en numerosas bibliotecas estándar de programación. Este informe examina su funcionamiento, implementación y aplicaciones, con especial énfasis en su variante externa para grandes volúmenes de datos.

2.  Fundamentos del QuickSort
    2.1 Estrategia Divide y Vencerás
    QuickSort opera bajo los siguientes principios:

        * Selección de pivote: Elegir un elemento de la lista como pivote.
        * Particionado: Reorganizar la lista de manera que todos los elementos menores que el pivote estén a su izquierda y todos los mayores a su derecha.
        * Recursividad: Aplicar los pasos 1 y 2 recursivamente a las sublistas izquierda y derecha.

    2.2 Algoritmo Básico
    El algoritmo básico de QuickSort en memoria puede representarse de la siguiente manera:

```cpp
quickSort(arr, left, right):
    si left < right:
        pivotIndex = partition(arr, left, right)
        quickSort(arr, left, pivotIndex - 1)
        quickSort(arr, pivotIndex + 1, right)
```

La función partition es el componente central:

```cpp
partition(arr, left, right):
    pivot = arr[right]
    i = left - 1

    para j desde left hasta right - 1:
        si arr[j] <= pivot:
            i = i + 1
            intercambiar arr[i] y arr[j]

    intercambiar arr[i + 1] y arr[right]
    retornar i + 1
```

3. QuickSort Externo
   3.1 Desafío del Ordenamiento Externo
   Al igual que con MergeSort externo, el QuickSort externo se utiliza cuando los datos no caben completamente en memoria. Sin embargo, QuickSort presenta desafíos adicionales debido a su naturaleza de particionamiento in-situ.

3.2 Fases del QuickSort Externo
La implementación de QuickSort externo sigue estas fases:

    * Selección de múltiples pivotes: Se eligen a-1 pivotes para crear a particiones.
    * Particionado a-vías: Los datos se dividen en a subarrays, donde cada subarray contiene elementos en un rango específico.
    * Procesamiento recursivo: Cada partición se ordena recursivamente mediante QuickSort externo.
    * Combinación: Las particiones ordenadas se concatenan para formar el resultado final.

3.3 Selección de Pivotes
La selección efectiva de pivotes es crucial para el rendimiento:

```cpp
size_t sampleSize = std::min(arr.size(), size_t(1000));
size_t step = arr.size() / sampleSize;
std::vector<int64_t> samples;
for (size_t i = 0; i < arr.size(); i += step) {
  samples.push_back(arr[i]);
}
std::sort(samples.begin(), samples.end());

std::vector<int64_t> pivots;
if (effective_a > 1) {
  size_t pivotStep = samples.size() / effective_a;
  for (size_t i = 1; i < effective_a; i++) {
    pivots.push_back(samples[i * pivotStep]);
  }
}
```

Este enfoque de muestreo ayuda a obtener pivotes que dividan el conjunto de datos de manera más equitativa.

4. Implementación de QuickSort Externo
   4.1 Gestión de Particiones
   El algoritmo escribe cada partición en archivos separados para procesamiento posterior:

```cpp
std::vector<std::vector<int64_t>> partitionBuffers(effective_a);
std::vector<std::string> partitionFiles(effective_a);

// ...

for (const auto& element : readBuffer) {
  size_t partitionIdx = 0;
  while (partitionIdx < pivots.size() && element >= pivots[partitionIdx]) {
    partitionIdx++;
  }

  partitionBuffers[partitionIdx].push_back(element);

  if (partitionBuffers[partitionIdx].size() >= bufferSize) {
    appendInt64DataToFile(partitionBuffers[partitionIdx],
                          partitionFiles[partitionIdx]);
    partitionBuffers[partitionIdx].clear();
  }
}
```

4.2 Recursión y Manejo de Errores
La implementación incluye manejo robusto de errores y estrategias de recuperación:

```cpp
try {
  std::vector<int64_t> partition = readInt64DataFromFile(partitionFiles[i]);
  externalQuickSort(partition, M, effective_a);
  arr.insert(arr.end(), partition.begin(), partition.end());
  std::filesystem::remove(partitionFiles[i]);
} catch (const std::exception& e) {
  std::cerr << "Error processing partition " << i << ": " << e.what() << std::endl;
  // Implementación de estrategia de recuperación...
}
```

4.3 Optimización de Aridad
La implementación ajusta automáticamente el número de particiones (a) basado en la memoria disponible:

```cpp
size_t effective_a = a;
if (a > arr.size() / 100) {
  effective_a = std::max(size_t(2), std::min(a, arr.size() / 100));
}
```

5. Análisis de Complejidad
   5.1 Complejidad Temporal
   _ Caso promedio: O(n log n)
   _ Peor caso: O(n²) cuando el pivote divide el array de manera muy desigual \* Mejor caso: O(n log n) cuando el pivote divide el array en partes aproximadamente iguales
   En QuickSort externo, la complejidad teórica se mantiene, pero el rendimiento real está influenciado por las operaciones de I/O.

5.2 Complejidad Espacial
_ QuickSort en memoria: O(log n) para la pila de recursión
_ QuickSort externo: O(M) en memoria principal, O(n) en almacenamiento secundario
5.3 Eficiencia de Acceso a Disco
El algoritmo implementado monitorea y optimiza las operaciones de disco:

```cpp
appendInt64DataToFile(partitionBuffers[partitionIdx], partitionFiles[partitionIdx]);
disk_write_count++;
```

6.  Mecanismos de Manejo de Fallos
    La implementación incluye tres niveles de estrategias de recuperación:

        * Implementación principal: Particionamiento múltiple y ordenamiento recursivo
        * Enfoque simplificado: División en fragmentos que caben en memoria
        * Fallback completo: Ordenamiento en memoria con escritura a disco

```cpp
try {
  // Estrategia principal
} catch (const std::exception& e) {
  try {
    // Estrategia simplificada
  } catch (const std::exception& e) {
    // Fallback final
  }
}
```
