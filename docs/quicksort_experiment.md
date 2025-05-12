# QuickSort Experiment: Evaluación de Rendimiento del Algoritmo QuickSort Externo

1.  Introducción
    El archivo quicksort_experiment.cpp implementa un marco experimental diseñado específicamente para evaluar el rendimiento del algoritmo QuickSort externo bajo diferentes condiciones. Este componente permite medir métricas de rendimiento críticas y verificar la correctitud del algoritmo cuando se enfrenta a diversos tipos y tamaños de datos con diferentes restricciones de memoria y configuraciones de aridad.

2.  Objetivo del Experimento
    El objetivo principal de quicksort_experiment.cpp es evaluar exhaustivamente el algoritmo QuickSort externo para:

        * Medir su rendimiento en términos de tiempo de ejecución
        * Cuantificar las operaciones de entrada/salida a disco
        * Verificar la correctitud de la ordenación bajo diferentes escenarios
        * Evaluar la escalabilidad del algoritmo con tamaños crecientes de datos
        * Analizar el impacto del parámetro de aridad (número de particiones) en el rendimiento
        * Establecer una base empírica para comparaciones con otros algoritmos

3.  Implementación
    3.1 Estructura Principal
    El experimento está organizado alrededor de la función runQuickSortExperiment que toma cuatro parámetros principales:

```cpp
void runQuickSortExperiment(std::size_t size, const std::string& dataType,
                            std::size_t M, std::size_t a) {
  // Generar o cargar datos
  // Ejecutar QuickSort y medir rendimiento
  // Verificar resultados y reportar métricas
}
```

3.2 Proceso Experimental
El proceso experimental sigue estos pasos:

    * Generación de datos: Se crean conjuntos de datos del tipo y tamaño especificados
    * Preparación: Se resetean contadores y se preparan temporizadores
    * Ejecución: Se aplica el algoritmo QuickSort con los parámetros especificados
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
// Para tamaños pequeños (≤ 10000)
runQuickSortExperiment(size, type, 100, 2);
runQuickSortExperiment(size, type, 100, 4);
runQuickSortExperiment(size, type, 100, 8);
runQuickSortExperiment(size, type, 1000, 2);
// ...

// Para tamaños medianos (≤ 100000)
runQuickSortExperiment(size, type, 100, 2);
// ...
runQuickSortExperiment(size, type, 10000, 8);

// Para tamaños grandes (> 100000)
runQuickSortExperiment(size, type, 1000, 2);
// ...
runQuickSortExperiment(size, type, 10000, 4);
```

4.4 Variación de Aridad
Un aspecto distintivo de QuickSort externo es la aridad (número de particiones), que se varía sistemáticamente:

    * Aridad = 2: División binaria tradicional
    * Aridad = 4: División en cuatro particiones
    * Aridad = 8: División en ocho particiones (para tamaños pequeños y medianos)

5.  Métricas y Resultados
    5.1 Métricas Capturadas
    El experimento captura las siguientes métricas para cada ejecución:

        * Tiempo de ejecución: Medido con precisión en segundos
        * Lecturas de disco: Número total de operaciones de lectura
        * Escrituras de disco: Número total de operaciones de escritura
        * Correctitud: Verificación de que la secuencia resultante está ordenada

    5.2 Formato de Salida en Consola
    El experimento proporciona una salida detallada en la consola:

```bash
QuickSort (random, n=10000, M=1000, a=4):
  Time: 0.198537 seconds
  Disk reads: 115
  Disk writes: 76
  Correctly sorted: Yes
```

5.3 Almacenamiento de Resultados
Los resultados se almacenan en un archivo CSV para análisis posterior:

```cvs
DataType,Size,MemorySize,Subarrays,Time,DiskReads,DiskWrites,Sorted
random,10000,1000,4,0.198537,115,76,1
sorted,10000,1000,4,0.142853,86,54,1
reverse,10000,1000,4,0.215678,122,81,1
partial,10000,1000,4,0.176429,98,67,1
```

6. Manejo de Errores
   El experimento incluye mecanismos de detección y reporte de errores:

```cpp
if (!isSorted) {
  std::string error = getSortingViolation<int64_t>(longData);
  std::cerr << "Error in QuickSort: " << error << std::endl;
}
```

Esta validación es crucial ya que QuickSort externo, debido a su complejidad y los procesos de particionamiento en disco, puede encontrar desafíos que afecten la correctitud del resultado final.

7.  Diferencias con MergeSort Experiment
    A diferencia del experimento de MergeSort, el experimento de QuickSort:

        * `ncorpora el parámetro de aridad: Evalúa explícitamente cómo diferentes valores de aridad afectan el rendimiento
        * Utiliza una matriz de combinaciones más amplia: Prueba más combinaciones de parámetros para capturar mejor la sensibilidad del algoritmo
        * Maneja particionamiento específico: Refleja la naturaleza del QuickSort externo, que se basa en pivotes y particiones
        * Incluye datos de pivotes: Implícitamente, a través del algoritmo QuickSort subyacente, evalúa diferentes estrategias de selección de pivotes

8.  Integración con el Proyecto
    `quicksort_experiment.cpp` es parte del sistema completo de evaluación de algoritmos de ordenamiento externo:

        * Utiliza las mismas utilidades para generación de datos, medición y verificación que otros experimentos
        * Comparte formatos de salida compatibles con las herramientas de visualización
        * Sus resultados pueden ser analizados en paralelo con los de MergeSort para comparaciones directas

9.  Ejecución del Experimento
    Para ejecutar el experimento:

```bash
# Modo básico (tamaños pequeños y medianos)
./quicksort_experiment

# Con conjuntos de datos grandes
./quicksort_experiment large
```
