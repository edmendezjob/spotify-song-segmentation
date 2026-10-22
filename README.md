# Segmentación de canciones de Spotify mediante técnicas de minería de datos

Proyecto académico colaborativo de ciencia de datos que analiza características acústicas de canciones para descubrir asociaciones, identificar grupos musicales e intentar predecir sus géneros.

**Curso:** Minería de Datos 2026-I  
**Institución:** Universidad Nacional de Colombia, Facultad de Ingeniería  
**Fecha del proyecto:** junio de 2026

## Objetivo

Explorar patrones musicales mediante tres líneas de trabajo:

- **Reglas de asociación:** encontrar relaciones frecuentes entre características acústicas discretizadas.
- **Clustering:** agrupar canciones según sus características y explorar la interpretación musical y emocional de los grupos.
- **Clasificación:** evaluar cuánto permiten predecir el género musical los descriptores acústicos.

## Datos

Se utiliza el [Spotify Tracks Dataset publicado por Maharshi Pandya en Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset). El conjunto original contiene aproximadamente **114.000 registros y 21 columnas**.

El CSV no está incluido en este repositorio. Para ejecutar el notebook:

1. Descarga manualmente el conjunto de datos desde [Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset).
2. Descomprime el archivo descargado si viene dentro de un ZIP.
3. Localiza `dataset.csv` en tu equipo.
4. Abre la celda **“Carga del dataset (descarga manual desde Kaggle)”** en `notebooks/01_segmentacion_spotify.ipynb` y actualiza `RUTA_DATASET` para que apunte a la ubicación real del archivo.

La ruta que aparece en la versión actual del notebook es un ejemplo de Windows:

```python
RUTA_DATASET = r"C:\Users\USUARIO\Downloads\archive\dataset.csv"
```

Reemplaza `USUARIO` por el nombre de tu cuenta de Windows y ajusta las carpetas si el CSV está en otra ubicación. No copies esa ruta literalmente sin verificarla. En macOS, Linux o Google Colab debes indicar la ruta correspondiente a tu entorno.

El CSV original y los CSV generados por el análisis están excluidos del control de versiones mediante `.gitignore`; no es necesario subir datos pesados al repositorio. Consulta las condiciones de uso indicadas en la página de Kaggle antes de reutilizar el conjunto de datos.

## Metodología

1. **Exploración y limpieza:** revisión de valores faltantes, duplicados y valores atípicos. Se elimina duplicación por `track_id` y se aplican filtros a características como duración, `loudness`, `speechiness` y `tempo`.
2. **Preprocesamiento:** transformaciones logarítmicas de variables sesgadas, codificación circular de variables musicales, estandarización y reducción de dimensionalidad con PCA.
3. **Reglas de asociación:** comparación de Apriori y FP-Growth sobre transacciones construidas a partir de atributos discretizados.
4. **Clustering:** comparación de K-Means, Gaussian Mixture Models (GMM), clustering jerárquico aglomerativo y HDBSCAN. Se utilizan métricas internas y visualizaciones UMAP; la interpretación emocional se relaciona con el modelo circunflejo de Russell.
5. **Clasificación de género:** comparación de Random Forest, Histogram Gradient Boosting y regresión logística multinomial con una partición estratificada de entrenamiento y prueba.
6. **Experimento adicional:** exploración de si agregar etiquetas de clustering como atributos mejora la clasificación de género.

## Resultados de referencia

Los siguientes valores corresponden a las salidas guardadas en la versión del notebook revisada para este repositorio. Una nueva ejecución puede producir resultados distintos según el entorno, las versiones de bibliotecas y los datos usados.

### Reglas de asociación

En la ejecución registrada, Apriori generó **21 reglas** y FP-Growth generó **9 reglas**, usando soporte mínimo de `0.15` y confianza mínima de `0.65`.

Una de las reglas con mayor lift relaciona acústica alta y volumen bajo con energía baja:

| Regla | Soporte | Confianza | Lift |
|---|---:|---:|---:|
| `{acusticidad_alta, volumen_bajo}` → `{energy_baja}` | 0,1897 | 0,9087 | 2,7192 |

Esto indica una asociación en los datos, no una relación causal.

### Reducción de dimensionalidad

PCA seleccionó **9 componentes** para superar el umbral del 80 % de varianza acumulada: la ejecución registrada conserva aproximadamente **83,7 %**.

### Comparación de clustering

Métricas calculadas sobre una submuestra, según el notebook. Para silhouette es preferible un valor más alto; para Davies–Bouldin, un valor más bajo suele indicar mejor separación.

| Algoritmo | Clusters | Silhouette | Davies–Bouldin |
|---|---:|---:|---:|
| K-Means | 5 | 0,103 | 2,040 |
| GMM | 5 | 0,063 | 2,698 |
| Jerárquico aglomerativo | 5 | 0,096 | 2,197 |
| HDBSCAN | 3 | 0,249 | 1,426 |

En la evaluación registrada de HDBSCAN, los puntos marcados como ruido se excluyeron del cálculo de las métricas de calidad.

### Clasificación de género

Comparación registrada antes de considerar el ajuste adicional de hiperparámetros:

| Modelo | Accuracy | F1 macro | F1 ponderado |
|---|---:|---:|---:|
| Random Forest | **0,302** | **0,286** | **0,287** |
| Histogram Gradient Boosting | 0,198 | 0,187 | 0,195 |
| Regresión logística multinomial | 0,145 | 0,111 | 0,118 |

Random Forest obtuvo el mejor resultado de esta comparación. Las métricas muestran que predecir géneros a partir únicamente de características acústicas continúa siendo difícil, en especial cuando distintas etiquetas describen estilos cercanos o se solapan.

> **Reproducibilidad:** estos resultados son referencias de las salidas guardadas, no una garantía de rendimiento idéntico en cualquier entorno. Ejecuta el notebook para recalcular las métricas.

## Estructura del repositorio

```text
spotify-song-segmentation/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── 01_segmentacion_spotify.ipynb
├── docs/
│   └── informe_tecnico.pdf
└── outputs/
    └── .gitkeep
```

- `notebooks/`: análisis, visualizaciones, modelos y resultados.
- `docs/`: informe técnico complementario.
- `outputs/`: carpeta creada/usada por el notebook para guardar CSV derivados; sus archivos se excluyen de Git por defecto.
- `requirements.txt`: dependencias para preparar el entorno de Python.
- `.gitignore`: evita subir entornos virtuales, CSV, credenciales y archivos temporales.

## Instalación y ejecución

Se recomienda **Python 3.11 o superior** y ejecutar el análisis en un entorno virtual.

### 1. Clonar el repositorio

```bash
git clone https://github.com/TU-USUARIO/spotify-song-segmentation.git
cd spotify-song-segmentation
```

Reemplaza `TU-USUARIO` por tu usuario de GitHub.

### 2. Crear y activar un entorno virtual

Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3. Instalar dependencias

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

El notebook también contiene celdas que instalan `efficient-apriori`, `pyfpgrowth` y `umap-learn`, además de una actualización de `scikit-learn`. Si instalaste todo desde `requirements.txt`, puedes omitir esas celdas de instalación para evitar reinstalaciones o actualizaciones a mitad de la ejecución. Si necesitas usarlas, ejecútalas en un entorno limpio y reinicia el kernel cuando sea necesario.

### 4. Descargar el dataset y configurar la ruta

Descarga y descomprime `dataset.csv` desde Kaggle, actualiza `RUTA_DATASET` en la celda de carga y confirma que la ruta exista antes de ejecutar la celda.

### 5. Abrir y ejecutar el notebook

```bash
jupyter lab
```

Abre `notebooks/01_segmentacion_spotify.ipynb` y ejecuta las celdas en orden. Las salidas tabulares se guardan en `outputs/` cuando se ejecutan las celdas de exportación. La ejecución completa de algunos modelos y visualizaciones puede requerir tiempo y memoria.

## Limitaciones y trabajo futuro

- Las características acústicas por sí solas no representan toda la información semántica, cultural o contextual de una canción.
- Géneros musicalmente cercanos pueden compartir características y resultar difíciles de distinguir.
- El número y la distribución de las clases afectan la clasificación multiclase.
- Incorporar letras, metadatos adicionales o representaciones de audio podría aportar información complementaria.
- El experimento adicional que agrega etiquetas de clustering debe interpretarse con cautela: para una evaluación estricta, las transformaciones y el ajuste de modelos deben aprenderse dentro de cada partición de entrenamiento, evitando fuga de información.

## Equipo

- Fernando Novoa Salazar
- Eduar Mauricio Méndez Méndez
- Leticia Martínez Sánchez
- Saidy Julieth Bonilla Cachaya

## Referencias

- Pandya, M. [Spotify Tracks Dataset — Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset).
- Russell, J. A. (1980). *A circumplex model of affect*.

## Alcance académico

Este repositorio documenta un proyecto académico colaborativo. 

## Licencia y uso

Este repositorio **no incluye por ahora una licencia abierta**. No se concede mediante este README permiso general para redistribuir, modificar o reutilizar el material.
