
# Proyecto Final — Comparación Pandas vs Polars en HPC (Kabre)

## Resumen
Este repositorio contiene el notebook finalizado para la entrega 3 del proyecto *Exploración de Herramientas Paralelas y Distribuidas para el Análisis de Datos en Python*. El objetivo es comparar Pandas vs Polars en lectura y procesamiento de un CSV de 10M filas (`product_10000000.csv`) en el cluster Kabre.

**Archivo principal:** Notebook con secciones: Setup HPC, Carga/Validación, Benchmarks, Strong & Weak Scaling, Análisis y Conclusiones.

## Requisitos del proyecto
- Python 3.9+ (recomendado 3.10)
- Bibliotecas (instaladas en el entorno de Kabre): `pandas`, `polars`, `numpy`, `matplotlib`, `psutil`
- Dataset: `product_10000000.csv` disponible en el FS local (path por defecto en notebook: `/mnt/data/product_10000000.csv`).
- No usar Dask, Ray, Spark, Modin, ni otras librerías paralelas externas.

> **Nota:** No se incluye `requirements.txt` por instrucción del usuario. Usa el entorno con estas librerías ya instaladas en Kabre.

## Estructura del notebook
1. Setup HPC y configuración de entorno (detección de núcleos, configuración Polars).
2. Carga y validación del dataset (lecturas optimizadas).
3. Benchmark de operaciones básicas (filtrado, groupby, sort, joins).
4. Strong scaling (variar threads).
5. Weak scaling (variar tamaño del dataset).
6. Análisis técnico y conclusiones.

## Cómo correr el notebook en Kabre (paso a paso)
1. **Conectarse** al nodo (ejecutar en la cola, ejemplo PBS/SLURM abajo).
2. Asegurarse de que `product_10000000.csv` esté disponible en el path indicado o actualizar `DATA_PATH` en la celda 1.
3. Abrir JupyterLab o ejecutar `jupyter nbconvert --to notebook --execute --inplace Proyecto_Avance_3.ipynb` desde la terminal (preferible dentro de un job).
4. Para experimentos largos (10M), correr desde un job interactivo o batch en la cola Kura (ver ejemplos abajo).
5. Reemplazar en los Markdown los placeholders con los valores medidos y guardar outputs (gráficos y `results_summary.json`).

## Ejemplos de envío a la cola Kura

> **Atención:** adapta `#PBS` o `#SBATCH` según la configuración de Kura en tu institución. A continuación dos ejemplos, uno PBS y otro SLURM.

### Ejemplo PBS (batch file `run_notebook.pbs`)
```bash
#!/bin/bash
#PBS -N pandas_polars_bench
#PBS -l select=1:ncpus=40:mem=256gb
#PBS -l walltime=04:00:00
#PBS -j oe
#PBS -o job.out

cd $PBS_O_WORKDIR
module load python/3.10  # ejemplo
# Activar entorno virtual si aplica
# source /path/to/venv/bin/activate

# Ejecutar notebook (recomendado para reproducibilidad)
jupyter nbconvert --ExecutePreprocessor.timeout=99999 --to notebook --execute Proyecto_Avance_3.ipynb --output Proyecto_Avance_3_out.ipynb
