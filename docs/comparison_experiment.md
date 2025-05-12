# Comparison Experiment: Análisis Comparativo de Algoritmos de Ordenamiento Externo

1.  Introducción
    El archivo comparison_experiment.cpp implementa un marco de experimentación diseñado para realizar un análisis comparativo directo entre los algoritmos MergeSort y QuickSort en su versión externa. A diferencia de los experimentos individuales, este componente ejecuta ambos algoritmos sobre los mismos conjuntos de datos, permitiendo una comparación justa y directa de su rendimiento bajo idénticas condiciones.

2.  Objetivo del Experimento
    El objetivo principal de comparison_experiment.cpp es proporcionar evidencia empírica sobre el rendimiento relativo de MergeSort y QuickSort externos cuando se enfrentan a:

        * Diferentes tipos de datos (aleatorios, ordenados, inversamente ordenados, parcialmente ordenados)
        * Diferentes tamaños de conjuntos de datos
        * Diferentes limitaciones de memoria
        * Diferentes configuraciones (auto-tuning vs parámetros manuales)

3.  Implementación
    3.1 Estructura Principal
    El experimento está estructurado alrededor de la función runComparisonExperiment que toma un objeto ExperimentParameters que encapsula toda la configuración necesaria:

```cpp
void runComparisonExperiment(const ExperimentParameters& params) {
  // Generar y cargar datos
  // Ejecutar MergeSort y medir rendimiento
  // Ejecutar QuickSort y medir rendimiento
  // Comparar y reportar resultados
}
```

3.2 Proceso Experimental
El proceso experimental sigue estos pasos:

- Generación de datos: Crea conjuntos de datos según el tipo especificado (random, sorted, reverse, partial)
- Ejecución de MergeSort: Aplica el algoritmo MergeSort y registra:
  - Tiempo de ejecución
  - Número de lecturas de disco
  - Número de escrituras de disco
  - Verificación de correctitud
- Ejecución de QuickSort: Repite el mismo proceso con QuickSort sobre una copia idéntica de los datos
- Análisis comparativo: Calcula métricas como el speedup entre algoritmos
- Registro de resultados: Almacena todos los resultados en archivos CSV para análisis posterior
  3.3 Configuración Paramétrica
  El experimento utiliza la clase ExperimentParameters para encapsular todos los parámetros necesarios:

```cpp
ExperimentParameters params;
params.dataSize = size;
params.dataType = type;
params.memoryLimit = M;
params.useAutoTuning = useAutoTuning;
```

Esta aproximación permite una configuración flexible y la extensión sencilla del experimento con nuevos parámetros.

4.  Parámetros Experimentales
    4.1 Tipos de Datos
    El experimento evalúa cuatro tipos de datos:

        	* Random: Datos generados aleatoriamente
        	* Sorted: Datos pre-ordenados
        	* Reverse: Datos ordenados en orden inverso
        	* Partial: Datos parcialmente ordenados (70% ordenados)

    4.2 Tamaños de Entrada
    Se utilizan diferentes tamaños de datos para evaluar la escalabilidad:

```cpp
std::vector<std::size_t> sizes = {1000, 10000, 100000};
if (runLarge) {
    sizes.push_back(1000000);
}
```

4.3 Limitaciones de Memoria
El experimento utiliza diferentes limitaciones de memoria para simular escenarios reales:

```cpp
std::vector<std::size_t> memorySizes = {1000, 10000};
```

4.4 Modos de Auto-tuning
Se puede configurar el experimento para utilizar parámetros auto-ajustados o manuales:

```cpp
bool useAutoTuning = true;
if (argc > 2) {
    std::string tuningParam = argv[2];
    if (tuningParam == "manual") useAutoTuning = false;
}
```

5. Salida y Análisis
   5.1 Formato de Salida en Consola
   El experimento proporciona una salida detallada en la consola:

```bash
Results for random data of size 10000:
  MergeSort time: 0.235 seconds (sorted: Yes)
  MergeSort disk reads: 120
  MergeSort disk writes: 80
  QuickSort time: 0.189 seconds (sorted: Yes)
  QuickSort disk reads: 90
  QuickSort disk writes: 60
  Speedup: 1.24x
```

5.2Archivos CSV de Resultados
Los resultados se almacenan en archivos CSV para análisis posterior:

```cvs
DataType,Size,MemorySize,MergeSort_Time,MergeSort_DiskReads,MergeSort_DiskWrites,
QuickSort_Time,QuickSort_DiskReads,QuickSort_DiskWrites,Speedup
```

Este formato permite el análisis con herramientas como Python (Pandas, Matplotlib) o cualquier hoja de cálculo.

6.  Integración con el Flujo de Trabajo
    comparison_experiment.cpp se integra en el flujo de trabajo general del proyecto:

        	* Se puede ejecutar independientemente o como parte de los experimentos generales
        	* Utiliza las mismas clases de ordenamiento y utilidades que el resto del sistema
        	* Sus resultados alimentan la visualización creada por visualize_results.py 7. Ejecución del Experimento

    Para ejecutar el experimento:

```bash
# Modo básico
./comparison_experiment

# Con conjuntos de datos grandes
./comparison_experiment large

# Con configuración manual de parámetros
./comparison_experiment large manual
```

8.  Análisis de Resultados
    Los resultados del experimento permiten:

        * Comparación directa: Identificar qué algoritmo es más rápido en cada escenario
        * Análisis de escalabilidad: Examinar cómo cambia el rendimiento relativo con diferentes tamaños de datos
        * Impacto del tipo de datos: Determinar qué algoritmo es más sensible a la distribución inicial de datos
        * Eficiencia de E/S: Comparar qué algoritmo realiza menos operaciones de disco
        * Efectividad del auto-tuning: Evaluar si los parámetros automáticos son mejores que los manuales
