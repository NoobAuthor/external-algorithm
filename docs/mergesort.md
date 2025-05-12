# Algoritmo MergeSort: Informe Técnico

1.  Introducción
    El algoritmo MergeSort es un algoritmo de ordenamiento eficiente y estable que implementa la estrategia "divide y vencerás". Desarrollado por John von Neumann en 1945, MergeSort es conocido por su eficiencia y consistente rendimiento en diferentes escenarios. Este informe analiza su funcionamiento, implementación y aplicaciones, con especial énfasis en su variante externa para conjuntos de datos que no caben completamente en memoria.

2.  Fundamentos del MergeSort
    2.1 Estrategia Divide y Vencerás
    MergeSort opera bajo tres principios fundamentales:

        * Dividir: Separar recursivamente el array en mitades hasta tener subarrays de tamaño 1 o 0.
        * Conquistar: Considerar cada subarray de tamaño 1 como ordenado.
        * Combinar: Unir (merge) los subarrays ordenados en secuencia ascendente.

    2.2 Algoritmo Básico
    El algoritmo de MergeSort en memoria se implementa de la siguiente manera:

```cpp
mergeSort(arr, left, right):
    si left < right:
        medio = (left + right) / 2
        mergeSort(arr, left, medio)
        mergeSort(arr, medio + 1, right)
        merge(arr, left, medio, right)
```

La función merge combina dos subarrays ordenados en uno solo:

```cpp
merge(arr, left, mid, right):
    crear arrays temporales L y R
    copiar datos a los arrays temporales
    recorrer ambos arrays combinándolos ordenadamente en arr
```

3. MergeSort Externo
   3.1 Desafío del Ordenamiento Externo
   El ordenamiento externo se utiliza cuando los datos son demasiado grandes para caber en la memoria principal, requiriendo operaciones de entrada/salida a disco durante el proceso de ordenamiento.

3.2 Fases del MergeSort Externo
El algoritmo implementado en el código sigue estas fases principales:

    * Creación de runs iniciales: Se dividen los datos en fragmentos (runs) del tamaño de la memoria disponible, se ordenan en memoria y se escriben en archivos temporales.
    * Merge multinivel: Se combinan los runs utilizando una estrategia de a-vías (a-way merge) donde 'a' es la aridad del algoritmo.
    * Procesamiento final: Combinación de todos los archivos intermedios en el resultado final.

3.3 Estructura de Datos Clave: Min-Heap
Se utiliza un min-heap para eficientar el proceso de merge k-way:

```cpp
struct HeapNode {
  int64_t value;
  size_t runIndex;
  size_t posInRun;
  bool operator>(const HeapNode& other) const { return value > other.value; }
};
```

Esta estructura permite identificar el valor mínimo entre k elementos de diferentes runs en O(log k) tiempo.

4. Implementación de MergeSort Externo
   4.1 Creación de Runs Iniciales

```cpp
std::vector<std::string> MergeSort::createInitialRuns(
    const std::vector<int64_t>& arr, size_t runSize,
    const std::string& tempDir) {
    // ...
    // Divide el array en fragmentos de tamaño runSize
    // Ordena cada fragmento en memoria
    // Escribe cada run en un archivo temporal
    // ...
}
```

4.2 Proceso de Merge K-way

```cpp
void MergeSort::mergeRuns(const std::vector<std::string>& runFiles,
                          const std::string& outputFile, size_t M, size_t a) {
    // ...
    // Si hay más runs que la aridad permitida, realiza merge recursivamente
    // Utiliza buffers y un min-heap para combinar eficientemente los runs
    // ...
}
```

4.3 Optimización del Buffer y Aridad
La implementación optimiza automáticamente:

    * Ta`año de buffer: Calculado para maximizar el uso efectivo de la memoria disponible
    * Aridad óptima: Determina el número de runs a combinar simultáneamente

```cpp
void MergeSort::autoExternalSort(std::vector<int64_t>& arr, size_t M) {
  size_t elementSize = sizeof(int64_t);
  size_t blockSize = 4096;
  size_t bufferElements = blockSize / elementSize;
  size_t optimalArity = calculateOptimalArity(M, bufferElements);
  // ...
}
```

5. Análisis de Complejidad
   5.1 Complejidad Temporal
   _ MergeSort en memoria: O(n log n) en todos los casos
   _ MergeSort externo: O(n log n) operaciones de comparación, pero las operaciones de I/O afectan el rendimiento real

5.2 Complejidad Espacial
_ MergeSort en memoria: O(n) espacio auxiliar
_ MergeSort externo: Limitado por el parámetro M (memoria disponible), pero requiere O(n) espacio en disco

5.3 Operaciones de I/O
El algoritmo implementado cuenta y optimiza las operaciones de lectura/escritura a disco:

```cpp
disk_read_count++;  // Incrementado en cada operación de lectura
disk_write_count++; // Incrementado en cada operación de escritura
```
