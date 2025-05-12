# Experiment Runner: Marco de Experimentación para Algoritmos de Ordenamiento Externo

1.  Introducción
    El componente experiment_runner.cpp implementa un marco experimental automatizado diseñado para ejecutar y evaluar sistemáticamente el rendimiento de los algoritmos de ordenamiento externo MergeSort y QuickSort bajo diferentes condiciones. Este informe detalla su arquitectura, funcionamiento y rol dentro del flujo de trabajo experimental del proyecto.

2.  Propósito y Objetivos
    El Experiment Runner tiene como objetivos principales:

        * Proporcionar un marco unificado para ejecutar experimentos sobre diferentes conjuntos de datos
        * Garantizar consistencia metodológica en la evaluación de los algoritmos
        * Facilitar la comparación directa entre MergeSort y QuickSort externos utilizando métricas estandarizadas
        * Generar resultados estructurados para análisis posterior y visualización

3.  Arquitectura del Sistema
    3.1 Estructura Principal
    El Experiment Runner se estructura alrededor de dos componentes principales:

        * Función runExperiment: Ejecuta el experimento sobre un archivo de entrada específico
        * Función main: Coordina la ejecución de múltiples experimentos bajo diferentes configuraciones

```cpp
void runExperiment(const std::string& inputFile, size_t arity) {
  // Ejecutar experimentos para MergeSort y QuickSort
  // Registrar y guardar resultados
}

int main(int argc, char* argv[]) {
  // Configurar experimentos
  // Detectar archivos de entrada disponibles
  // Iterar ejecutando experimentos individuales
}
```

3.2 Integración con el Sistema
El Experiment Runner se integra con otros componentes del proyecto:

    * Algoritmos de ordenamiento: Utiliza las clases MergeSort y QuickSort para realizar los experimentos
    * Sistema de archivos: Lee datos de secuencias generadas por data_generator y escribe resultados para análisis
    * Visualizador: Genera archivos CSV consumidos por visualize_results.py para crear gráficos

4.  Flujo de Trabajo Experimental
    4.1 Proceso de Ejecución
    El flujo de trabajo de experimentos sigue estos pasos:

        * Preparación: Creación de directorios para resultados y verificación de la aridad óptima
        * Selección de datos: Identificación de archivos de secuencias para procesamiento
        * Ejecución de experimentos:
        	* Lectura de cada secuencia de datos
        	* Aplicación de MergeSort con monitoreo de rendimiento
        	* Aplicación de QuickSort con monitoreo de rendimiento
        * Registro de resultados: Almacenamiento de métricas en archivos CSV estructurados

    4.2 Parametrización
    El sistema permite configura parámetros experimentales:

        * Aridad óptima (arity): Leída desde data/optimal_arity.txt, determinada previamente por optimal_arity_finder
        * Tamaño de datos (multiplier): Definido en los nombres de archivos de secuencia (p.ej., seq_32M_1.bin para 32M)
        * Memoria disponible (M_SIZE): Constante definida en sort_parameters.h (50MB según requisitos)

    4.3 Selección de Conjuntos de Datos
    El sistema identifica automáticamente los conjuntos de datos disponibles:

```cpp
std::vector<std::string> seqFiles;
for (const auto& entry : std::filesystem::directory_iterator("data/sequences")) {
  std::string filename = entry.path().filename().string();
  if (filename.find("seq_") == 0 && filename.size() >= 4 &&
      filename.substr(filename.size() - 4) == ".bin") {
    seqFiles.push_back(entry.path().string());
  }
}
```

También permite filtrar por tamaño específico mediante argumentos de línea de comandos:

```cpp
if (argc > 1) {
  std::string sizeArg = argv[1];
  size_t targetSize = std::stoul(sizeArg);
  // Filtrado de archivos por tamaño...
}
```

5.  Métricas de Rendimiento
    El Experiment Runner recolecta las siguientes métricas para cada algoritmo:

        * Tiempo de ejecución: Medido con la clase Timer en segundos
        * Lecturas de disco: Contador incremental de operaciones de lectura
        * Escrituras de disco: Contador incremental de operaciones de escritura
        * Correctitud: Verificación de que el resultado está correctamente ordenado

    5.1 Formato de Resultados
    Los resultados se almacenan en archivos CSV con la siguiente estructura:

```bash
Multiplier,Size,Time,DiskReads,DiskWrites,Sequence,Sorted
```

Donde:

    * Multiplier: Tamaño del conjunto de datos como múltiplo de M (memoria disponible)
    * Size: Número total de elementos en el conjunto de datos
    * Time: Tiempo de ejecución en segundos
    * DiskReads: Número de operaciones de lectura a disco
    * DiskWrites: Número de operaciones de escritura a disco
    * Sequence: Identificador de la secuencia (1-5)
    * Sorted: Indicador booleano de correctitud (1 = ordenado correctamente)

6. Verificación de Resultados
   El Experiment Runner verifica la correctitud de cada ejecución:

```cpp
bool sorted = true;
for (size_t i = 1; i < testData.size(); i++) {
  if (testData[i] < testData[i - 1]) {
    sorted = false;
    break;
  }
}
```

Esta verificación asegura la integridad de los experimentos y proporciona confiabilidad en los resultados reportados.

7. Integración con el Flujo de Trabajo General
   El Experiment Runner se integra con el flujo de trabajo automatizado del proyecto a través de CMake:

```txt
# Run all experiments
add_custom_target(run_experiments_all
    COMMAND ${CMAKE_BINARY_DIR}/bin/experiment_runner
    DEPENDS experiment_runner find_optimal_arity generate_data
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMENT "Running experiments on all data sizes"
)
```

También es posible ejecutar experimentos para tamaños específicos:

```bash
# Para ejecutar experimentos solo con datos de 32M
./experiment_runner 32
```
