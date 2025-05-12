# MergeSort Experiment: Evaluación de Rendimiento del Algoritmo MergeSort Externo

1.  Introducción
    El archivo mergesort_experiment.cpp implementa un marco experimental diseñado específicamente para evaluar el rendimiento del algoritmo MergeSort externo bajo diferentes condiciones. Este componente permite medir métricas de rendimiento críticas y verificar la correctitud del algoritmo cuando se enfrenta a diversos tipos y tamaños de datos con diferentes restricciones de memoria.

2.  Objetivo del Experimento
    El objetivo principal de mergesort_experiment.cpp es evaluar exhaustivamente el algoritmo MergeSort externo para:

        * Medir su rendimiento en términos de tiempo de ejecución
        * Cuantificar las operaciones de entrada/salida a disco
        * Verificar la correctitud de la ordenación bajo diferentes escenarios
        * Evaluar la escalabilidad del algoritmo con tamaños crecientes de datos
        * Analizar la eficiencia del auto-tuning de los parámetros del algoritmo

3.  Implementación
    3.1 Estructura Principal
    El experimento está organizado alrededor de la función runMergeSortExperiment que toma tres parámetros principales:

```cpp
void runMergeSortExperiment(std::size_t size, const std::string& dataType, std::size_t M) {
  // Generar o cargar datos
  // Ejecutar MergeSort y medir rendimiento
  // Verificar resultados y reportar métricas
}
```

3.2 Proceso Experimental
El proceso experimental sigue estos pasos:

    * Generación de datos: Se crean conjuntos de datos del tipo y tamaño especificados
    * Preparación: Se resetean contadores y se preparan temporizadores
    * Ejecución: Se aplica el algoritmo MergeSort con auto-tuning
    * Verificación: Se comprueba que el resultado esté correctamente ordenado
    * Medición: Se registran tiempo de ejecución y operaciones de disco
    * Registro: Se almacenan los resultados en formato CSV para análisis posterior

3.3 Estructura de Ejecución
La función principal configura y ejecuta experimentos con múltiples combinaciones de parámetros:

```cpp
int main(int argc, char* argv[]) {
  // Configuración del archivo de resultados
  // Definición de diferentes tamaños de datos
  // Definición de diferentes tipos de datos
  // Definición de diferentes limitaciones de memoria
  // Iteración por todas las combinaciones de parámetros
  // Ejecución del experimento para cada combinación
}
```

4.  Parámetros Experimentales
    4.1 Tipos de Datos
    El experimento evalúa cuatro distribuciones de datos fundamentales:

        * Random: Datos generados aleatoriamente sin orden previo
        * Sorted: Datos ya ordenados ascendentemente
        * Reverse: Datos ordenados en orden descendente
        * Partial: Datos parcialmente ordenados (70% ordenados)

    4.2 Tamaños de Entrada
    Se utilizan diferentes tamaños de datos para evaluar la escalabilidad:

```cpp
std::vector<std::size_t> sizes = {1000, 10000, 100000};
if (argc > 1 && std::string(argv[1]) == "large") {
  sizes.push_back(1000000);
}
```

4.3 Limitaciones de Memoria
El experimento utiliza diferentes limitaciones de memoria para simular escenarios de ordenamiento externo:

```cpp
std::vector<std::size_t> memorySizes = {100, 1000, 10000};
```

La relación entre el tamaño de los datos y la memoria disponible se verifica para garantizar experimentos significativos:

```cpp
if (M > size / 10) continue;
```

5.  Métricas y Resultados
    5.1 Métricas Capturadas
    El experimento captura las siguientes métricas para cada ejecución:

        * `iempo de ejecución: Medido con precisión en segundos
        * Lecturas de disco: Número total de operaciones de lectura
        * Escrituras de disco: Número total de operaciones de escritura
        * Correctitud: Verificación de que la secuencia resultante está ordenada

    5.2 Formato de Salida en Consola
    El experimento proporciona una salida detallada en la consola:

```bash
MergeSort (random, n=10000, M=1000):
  Time: 0.235648 seconds
  Disk reads: 120
  Disk writes: 80
  Correctly sorted: Yes
```

5.3 Almacenamiento de Resultados
Los resultados se almacenan en un archivo CSV para análisis posterior:

```cvs
DataType,Size,MemorySize,Time,DiskReads,DiskWrites,Sorted
random,10000,1000,0.235648,120,80,1
sorted,10000,1000,0.152331,78,56,1
reverse,10000,1000,0.241294,125,84,1
partial,10000,1000,0.198372,102,70,1
```

6.  Integración con el Proyecto
    `mergesort_experiment.cpp` es parte del sistema completo de evaluación de algoritmos de ordenamiento externo:

        		* Utiliza las mismas clases de ordenamiento que el resto del sistema
        		* Comparte utilidades para la generación, verificación y medición con otros experimentos
        		* Sus resultados pueden ser analizados junto con los de quicksort_experiment.cpp y comparison_experiment.cpp 7. Ejecución del Experimento

    Para ejecutar el experimento:

```bash
# Modo básico (tamaños pequeños y medianos)
./mergesort_experiment

# Con conjuntos de datos grandes
./mergesort_experiment large
```

8.  Análisis de Errores
    El experimento incluye gestión de errores y validación:

        * Verificación de correctitud: Garantiza que los resultados estén correctamente ordenados
        * Captura de excepciones: Maneja errores durante la generación de datos o el proceso de ordenamiento
        * Reporte detallado: Proporciona información específica sobre la naturaleza de cualquier error encontrado

```cpp
if (!isSorted) {
  std::string error = getSortingViolation<int64_t>(longData);
  std::cerr << "Error in MergeSort: " << error << std::endl;
}
```
