# Segmentación de canciones de Spotify mediante técnicas de minería de datos

Proyecto académico de ciencia de datos que explora patrones en las características acústicas de canciones y construye segmentos musicales asociados con perfiles emocionales. Se combinan reglas de asociación, aprendizaje no supervisado y clasificación multiclase.

**Curso:** Minería de Datos 2026-I · Universidad Nacional de Colombia, Facultad de Ingeniería  
**Fecha del proyecto:** junio de 2026

## Objetivo

Analizar características acústicas de canciones para:

- Encontrar relaciones frecuentes entre atributos musicales mediante reglas de asociación.
- Agrupar canciones con perfiles acústicos similares e interpretar los grupos desde una perspectiva emocional.
- Evaluar hasta qué punto las características acústicas ayudan a predecir el género musical.

## Fuente de datos

El proyecto utiliza el [Spotify Tracks Dataset publicado en Kaggle por Maharshi Pandya](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset), cuyo archivo original contiene aproximadamente 114.000 registros y 21 columnas.

El CSV original **no se incluye en este repositorio**. El notebook lo descarga mediante KaggleHub, de modo que cada persona pueda obtener el archivo desde su fuente. Revisa los términos y la licencia indicados en la página del conjunto de datos antes de reutilizar los datos.

## Metodología

1. **Exploración y limpieza:** revisión de tipos, valores faltantes, duplicados y valores inconsistentes. Se eliminan duplicados por `track_id` y se aplican filtros a duración, compás, volumen, `speechiness` y tempo.
2. **Preprocesamiento:** transformaciones logarítmicas para variables sesgadas, codificación cíclica de variables musicales, estandarización y análisis de componentes principales (PCA).
3. **Reglas de asociación:** comparación de Apriori y FP-Growth sobre atributos acústicos discretizados.
4. **Clustering:** comparación de K-Means, Gaussian Mixture Models (GMM), clustering jerárquico aglomerativo y HDBSCAN. Se emplean métricas internas y visualizaciones UMAP; la interpretación emocional se apoya en el modelo circunflejo de Russell.
5. **Clasificación de género:** comparación de Random Forest, Histogram Gradient Boosting y regresión logística multinomial, con evaluación sobre una partición estratificada de entrenamiento y prueba.
6. **Experimento adicional:** evaluación exploratoria de si añadir etiquetas de clustering al clasificador mejora la predicción del género.

## Resultados destacados de la ejecución registrada

### Reglas de asociación

Una de las reglas con mayor lift relaciona **acústica alta y volumen bajo** con **energía baja**. En los resultados guardados se reportan soporte de aproximadamente `0,1897`, confianza de `0,9087` y lift cercano a `2,72`.

### Comparación de algoritmos de clustering

Métricas calculadas en una submuestra del notebook. Un silhouette más alto y un Davies–Bouldin más bajo suelen indicar grupos mejor separados según esas métricas.

| Algoritmo | N.º de grupos | Silhouette | Davies–Bouldin | Observación |
|---|---:|---:|---:|---|
| K-Means | 5 | 0,103 | 2,040 | Asigna cada canción a un grupo |
| GMM | 5 | 0,063 | 2,698 | Modela pertenencias probabilísticas |
| Jerárquico aglomerativo | 5 | 0,096 | 2,197 | Agrupamiento por similitud jerárquica |
| HDBSCAN | 3 | 0,249 | 1,426 | Identifica puntos de ruido; 74 en la submuestra evaluada |

### Clasificación de género

Resultados de la comparación de modelos registrada en el notebook, antes de considerar el ajuste adicional de hiperparámetros.

| Modelo | Accuracy | F1 macro | F1 ponderado |
|---|---:|---:|---:|
| Random Forest | **0,303** | **0,286** | **0,287** |
| Histogram Gradient Boosting | 0,201 | 0,192 | 0,198 |
| Regresión logística multinomial | 0,143 | 0,109 | 0,116 |

La clasificación es un problema multiclase difícil: los descriptores acústicos no siempre distinguen géneros cercanos y el conjunto de datos contiene muchas etiquetas. En el experimento adicional, incorporar etiquetas de clustering no mejoró el resultado del Random Forest en la partición evaluada.

> **Nota de reproducibilidad:** las métricas anteriores reflejan las salidas guardadas en el notebook original. Vuelve a ejecutar las celdas para recalcularlas con la versión actual del dataset y el entorno instalado; los resultados pueden variar.

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

- `notebooks/`: análisis reproducible, gráficos y modelos.
- `docs/`: informe técnico del proyecto.
- `outputs/`: directorio local para CSV derivados; los resultados generados no se versionan por defecto.
- `requirements.txt`: dependencias Python necesarias para ejecutar el notebook.

## Cómo ejecutar el proyecto

Se recomienda Python 3.11 o superior.

1. Clona o descarga este repositorio.
2. Crea y activa un entorno virtual:

   ```bash
   python -m venv .venv
   ```

   En Windows:

   ```powershell
   .venv\Scripts\Activate.ps1
   ```

   En macOS/Linux:

   ```bash
   source .venv/bin/activate
   ```

3. Instala las dependencias:

   ```bash
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. Inicia Jupyter y abre el notebook:

   ```bash
   jupyter lab
   ```

5. Ejecuta las celdas en orden. La primera sección de carga obtiene `dataset.csv` mediante KaggleHub y los CSV derivados se guardan en `outputs/`.

Si KaggleHub solicita autenticación, sigue el procedimiento documentado en la [guía oficial de KaggleHub](https://github.com/Kaggle/kagglehub#authenticate). No incluyas tokens, `kaggle.json` ni credenciales en el repositorio.

## Limitaciones y trabajo futuro

- El clasificador confunde géneros musicalmente similares; las características acústicas, por sí solas, no capturan toda la información semántica de una canción.
- El desbalance entre clases y la cantidad de géneros afectan la evaluación multiclase.
- Incorporar texto de letras, metadatos adicionales o representaciones de audio podría aportar información complementaria.
- El experimento que añade etiquetas de clustering advierte que estas se calcularon sobre el conjunto completo antes de evaluar el clasificador. Para una evaluación estricta sin fuga de información, las etiquetas deben generarse dentro de cada partición de entrenamiento/validación.

## Equipo

- Fernando Novoa Salazar
- Eduar Mauricio Méndez Méndez
- Leticia Martínez Sánchez
- Saidy Julieth Bonilla Cachaya

## Referencias

- Pandya, M. [Spotify Tracks Dataset — Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset).
- [KaggleHub: documentación oficial](https://github.com/Kaggle/kagglehub).
- Russell, J. A. (1980). *A circumplex model of affect*.

## Alcance académico

Este repositorio documenta un proyecto académico colaborativo. Antes de publicarlo, confirma que el equipo está de acuerdo con publicar el notebook, el informe y los nombres de sus integrantes, y conserva las atribuciones de la fuente de datos.
