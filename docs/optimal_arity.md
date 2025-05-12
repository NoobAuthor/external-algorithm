# Optimal Arity Finder: Optimización de Parámetros para Algoritmos de Ordenamiento Externo

1.  Introducción
    El componente optimal_arity_finder.cpp implementa un sistema inteligente para determinar el valor óptimo de aridad (número de divisiones o fusiones simultáneas) para los algoritmos de ordenamiento externo. Este parámetro crítico afecta significativamente el rendimiento de los algoritmos MergeSort y QuickSort externos, especialmente cuando procesan grandes volúmenes de datos. Este informe detalla su funcionamiento, implementación y rol dentro del flujo de trabajo experimental del proyecto.

2.  Propósito y Objetivos
    El Optimal Arity Finder tiene como objetivos principales:

        * Determinar automáticamente el valor óptimo de aridad que minimiza el tiempo de ejecución
        * Proporcionar un valor de referencia consistente para todos los experimentos subsecuentes
        * Balancear el uso de memoria, las operaciones de I/O y el rendimiento general
        * Adaptar el algoritmo a las características específicas del hardware subyacente

3.  Fundamentación Teórica
    La aridad en el contexto de algoritmos de ordenamiento externo representa:

        * En MergeSort: El número de subarrays a fusionar simultáneamente
        * En QuickSort: El número de particiones a crear durante la fase de división

    La elección óptima de este parámetro depende de varios factores:

        * Memoria disponible (M): Una aridad mayor requiere más buffers en memoria
        * Tamaño del conjunto de datos: Afecta la cantidad de fusiones necesarias
        * Características de I/O del sistema: Velocidades de lectura/escritura a disco
        * Patrones de acceso a memoria: Localidad y eficiencia de caché

4.  Implementación
    4.1 Arquitectura del Sistema
    El Optimal Arity Finder se estructura alrededor de tres componentes principales:

        * Función de prueba (testMergeSortArity): Mide el rendimiento para un valor específico de aridad
        * Algoritmo de búsqueda (smartBinarySearch): Implementa una estrategia sofisticada para encontrar el valor óptimo
        * Función principal (main): Coordina el proceso y almacena el resultado

    4.2 Función de Prueba

```cpp
double testMergeSortArity(const std::vector<int64_t>& data, size_t M, size_t a) {
  std::cout << "Testing arity: " << a << std::endl;

  size_t elementSize = sizeof(int64_t);
  size_t totalBuffers = a + 1;
  size_t bufferSize = (M * 0.9) / (totalBuffers * elementSize);

  if (bufferSize < 1000) {
    std::cout << "  Buffer size too small (" << bufferSize
              << " elements), skipping" << std::endl;
    return std::numeric_limits<double>::max();
  }

  size_t testSize = std::min(data.size(), size_t(10000000));
  std::vector<int64_t> testData(data.begin(), data.begin() + testSize);

  resetDiskCounters();
  MergeSort sorter;
  Timer timer;

  timer.start();
  sorter.externalSort(testData, M, a);
  timer.stop();

  double duration = timer.getDuration();
  std::cout << "  Time: " << duration << " seconds" << std::endl;
  std::cout << "  Disk reads: " << getDiskReadCount()
            << ", writes: " << getDiskWriteCount() << std::endl;

  // Verificación de correctitud
  if (!isSorted<int64_t>(testData)) {
    std::cerr << "  Error: Result is not sorted for arity " << a << std::endl;
    return std::numeric_limits<double>::max();
  }

  return duration;
}
```

Esta función:

    * Calcula el tamaño de buffer resultante para la aridad dada
    * Verifica que el tamaño de buffer sea viable
    * Ejecuta MergeSort con la aridad específica
    * Mide el tiempo de ejecución y las operaciones de disco
    * Verifica que el resultado esté correctamente ordenado

4.3 Algoritmo de Búsqueda Inteligente
La búsqueda del valor óptimo se implementa mediante un algoritmo en dos fases:

```cpp
size_t smartBinarySearch(
    const std::vector<int64_t>& data,
    std::function<double(const std::vector<int64_t>&, size_t, size_t)> testWithArity) {

  // Fase 1: Probar valores de referencia
  const std::vector<size_t> landmarks = {2, 4, 8, 16, 32, 64, 128, 256, 512};
  // Encontrar el mejor landmark

  // Fase 2: Refinamiento alrededor del mejor landmark
  // Explorar valores cercanos para encontrar el óptimo local

  return bestArity;
}
```

Fase 1: Evaluación de Landmarks
La primera fase prueba valores de referencia (2, 4, 8, ..., 512) para identificar rápidamente la región del espacio de búsqueda que contiene el óptimo:

```cpp
std::cout << "Phase 1: Testing landmark arity values" << std::endl;
for (size_t a : landmarks) {
  double time = testWithArity(data, M_SIZE, a);
  if (time < std::numeric_limits<double>::max()) {
    results.push_back({a, time});
    std::cout << "  Arity " << a << ": " << time << " seconds" << std::endl;
  }
}
```

Fase 2: Refinamiento Estratégico
La segunda fase refina la búsqueda alrededor del mejor valor encontrado en la fase 1:

```cpp
if (upperBound - lowerBound <= 10) {
  // Búsqueda lineal en un rango pequeño
} else {
  // Refinamiento estratégico alrededor del mejor valor
  std::vector<size_t> refinementPoints;
  if (bestArity > 2) refinementPoints.push_back(bestArity - bestArity / 4);
  refinementPoints.push_back(bestArity + bestArity / 4);

  // Evaluar los puntos de refinamiento
}
```

Esta estrategia de dos fases permite:

    * Explorar eficientemente el espacio de soluciones
    * Evitar quedarse atrapado en óptimos locales
    * Reducir el número total de pruebas necesarias

4.4 Integración con el Sistema
El componente se integra con el resto del proyecto a través de:

```cpp
int main() {
  // Cargar datos de prueba
  FileManager& fileManager = FileManager::getInstance();
  std::string testFile = fileManager.findLargestArityTestFile();

  // Determinar la aridad óptima
  size_t optimalArity = smartBinarySearch(testData, testMergeSortArity);

  // Guardar el resultado para uso por otros componentes
  std::ofstream arityFile("data/optimal_arity.txt");
  arityFile << optimalArity << std::endl;

  return 0;
}
```

El valor de aridad óptimo se almacena en un archivo que posteriormente es utilizado por el `experiment_runner.cpp` para configurar los experimentos.

5.  Consideraciones Técnicas
    5.1 Balanceo de Memoria
    El finder considera el equilibrio entre:

        * Número de buffers: Aumenta con la aridad (a+1 buffers)
        * Tamaño de cada buffer: Disminuye con la aridad (M/(a+1))

```cpp
size_t totalBuffers = a + 1;
size_t bufferSize = (M * 0.9) / (totalBuffers * elementSize);
```

El factor 0.9 proporciona un margen de seguridad para evitar el uso excesivo de memoria.

5.2 Límites Prácticos
El sistema impone límites prácticos a la aridad:

```cpp
// En calculateOptimalArity:
const size_t MAX_REASONABLE_ARITY = 16;
arity = std::min(arity, MAX_REASONABLE_ARITY);

// En smartBinarySearch:
size_t maxArity = 512;
```

Estos límites reflejan que:

    * Una aridad muy pequeña (2) infrautiliza la memoria pero minimiza las comparaciones
    * Una aridad muy grande (>512) puede crear buffers demasiado pequeños y excesiva sobrecarga administrativa

6.  Ejecución e Integración
    6.1 Flujo de Trabajo
    El Optimal Arity Finder se ejecuta como un paso preliminar en el flujo de trabajo experimental:

        * Generación de datos: data_generator crea conjuntos de datos de prueba
        * Determinación de aridad: optimal_arity_finder encuentra el valor óptimo
        * Ejecución de experimentos: experiment_runner utiliza el valor óptimo

    Este flujo está automatizado a través del sistema de construcción:

```txt
add_custom_target(find_optimal_arity
    COMMAND ${CMAKE_BINARY_DIR}/bin/optimal_arity_finder
    DEPENDS optimal_arity_finder generate_data
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMENT "Finding optimal arity for external sorting"
)
```

6.2 Verificación de Disponibilidad
El `experiment_runner.cpp` verifica la disponibilidad del valor óptimo:

```cpp
size_t arity = 0;
std::ifstream arityFile("data/optimal_arity.txt");
if (arityFile) {
  arityFile >> arity;
}

if (arity == 0) {
  std::cerr << "Optimal arity not found. Please run optimal_arity_finder first."
            << std::endl;
  return 1;
}
```

7.  Resultados y Análisis
    Los resultados típicos muestran que:

        * Para conjuntos de datos pequeños (<100MB) con memoria limitada (≈50MB), la aridad óptima suele estar entre 4 y 8
        * Para conjuntos de datos muy grandes (>1GB) con la misma memoria, la aridad óptima tiende a ser mayor (8-16)
        * Valores de aridad mayores a 32 rara vez proporcionan beneficios adicionales debido al tamaño reducido de los buffers

    La determinación automática de este parámetro:

        * Elimina la necesidad de ajuste manual
        * Adapta el algoritmo a las características específicas del hardware
        * Optimiza el rendimiento para las condiciones particulares del sistema
