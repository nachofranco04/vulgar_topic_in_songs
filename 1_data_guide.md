
# Instrucciones para obtener los datos y ejecutar los notebooks

Este documento explica **cómo descargar el dataset**, **preparar el entorno** y **ejecutar en orden** los notebooks de esta carpeta.

> Nota importante sobre rutas: los notebooks usan **rutas relativas** (por ejemplo `spanish_songs_wikidata.csv`, `topics_all_year.csv`, etc.).

---

## 1) Datos: descarga desde Hugging Face

El dataset se obtiene de:

https://huggingface.co/datasets/MarKos29/Music_Metadata_and_Lyrics_Dataset

En el repositorio de Hugging Face hay un CSV llamado `spanish_songs_wikidata_no_lyrics.csv`.

**Nota sobre derechos de autor:** en la versión del dataset publicada en Hugging Face se han eliminado las letras (`lyrics`) deliberadamente por motivos de copyright/derechos de autor. Por eso el archivo disponible allí es la variante **no_lyrics**.

### Opción A (recomendada): descarga manual del CSV

1. Entra en la pestaña **Files and versions**.
2. Descarga `spanish_songs_wikidata_no_lyrics.csv`.
3. Colócalo en la carpeta:
	- `3_mineria_de_datos_complejos/`
4. Renómbralo a:
	- `spanish_songs_wikidata.csv`

### Requisito de columnas (mínimo)

El notebook `entrega_2_nuevo_enfoque.ipynb` requiere que el CSV tenga, como mínimo, estas columnas:

- `year` (año, numérico)
- `views` (numérico; si falta, el notebook lo rellena con 0)
- `lyrics` (texto)

Si el CSV descargado de Hugging Face no incluye `lyrics` (como ocurre en la versión “no_lyrics” publicada), entonces:

- Para ejecutar el **Paso 1** (pipeline completo con limpieza/lematización + tópicos), necesitas un CSV local equivalente que **sí** contenga `lyrics` (no distribuible públicamente en nuestro caso).
- Si no dispones de letras, puedes ejecutar directamente el **Paso 2** (análisis temporal) usando los CSVs ya generados (`topics_all_year.csv`, `doc_topic_year.csv`, `sentiment_by_year.csv`).

---

## 2) Entorno: dependencias necesarias

Estos notebooks se han ejecutado con Python 3.x y requieren, como mínimo:

- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- `nltk`
- `gensim`
- `spacy` + modelo `es_core_news_sm`
- `pysentimiento` (usa modelos basados en `transformers`/`torch`)
- `wordcloud`
- `statsmodels`
- `jupyter` (si vas a ejecutar desde Jupyter fuera de VS Code)

### Instalación rápida (Windows PowerShell)

Desde la raíz del proyecto (carpeta `Practica/`):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -U pip
python -m pip install pandas numpy matplotlib seaborn nltk gensim spacy wordcloud statsmodels jupyter
python -m pip install pysentimiento
python -m spacy download es_core_news_sm
```

Notas:

- `nltk` descarga automáticamente `punkt` y `stopwords` desde el propio notebook.
- `pysentimiento` descargará modelos la primera vez (puede tardar y requiere conexión).

---

## 3) Ejecución (orden recomendado)

### Paso 0: asegúrate del directorio de trabajo

Para que las rutas relativas cuadren con los datos y los resultados ya incluidos en `3_mineria_de_datos_complejos/`, ejecuta los notebooks con **working directory**:

- `3_mineria_de_datos_complejos/`

Si usas Jupyter desde terminal, una forma sencilla es arrancarlo desde esa carpeta:

```powershell
cd .\3_mineria_de_datos_complejos
jupyter lab
```

Si ejecutas desde VS Code y tienes errores de “File not found”, comprueba el directorio actual con `import os; os.getcwd()` y ajusta el working dir (o mueve/copias los CSV al directorio donde esté corriendo el kernel).

### Paso 1: pipeline completo (tópicos + series por año)

Notebook: `Entrega_3_completa/notebooks/entrega_2_nuevo_enfoque.ipynb`

Qué hace:

- Lee `spanish_songs_wikidata.csv`.
- Limpia y lematiza letras (spaCy).
- Construye/carga diccionario y corpus de gensim.
- Entrena/carga `LdaSeqModel`.
- Calcula evolución de términos por tópico y métricas por año.
- Calcula sentimiento con `pysentimiento`.
- Exporta CSVs intermedios para el análisis temporal.

Archivos generados (en el directorio de trabajo):

- `diccionario_ldaseq.dict`
- `corpus_ladaseq.mm`
- `modelo_ldaseq.model`
- `topics_all_year.csv`
- `doc_topic_year.csv`
- `sentiment_by_year.csv`
- `dataset_entrega3_final.csv`

> Aviso: este notebook puede tardar bastante (especialmente el análisis de sentimiento) y consume memoria.

### Paso 2: análisis temporal y modelos de series

Notebook: `Entrega_3_completa/notebooks/estudio_serie_temporal_entrega3.ipynb`

Requiere que existan ya (del Paso 1):

- `topics_all_year.csv`
- `doc_topic_year.csv`
- `sentiment_by_year.csv`

Qué produce:

- Figuras guardadas en una carpeta `results/` dentro del directorio de trabajo.

---

## 4) Solución de problemas frecuentes

### Error: no encuentra `spanish_songs_wikidata.csv` / `topics_all_year.csv` / etc.

- Causa típica: estás ejecutando el notebook con un directorio de trabajo distinto.
- Solución: ejecuta con working directory `3_mineria_de_datos_complejos/` o copia los archivos al directorio donde se ejecuta el kernel.

### Error: `spaCy model es_core_news_sm not found`

Ejecuta:

```powershell
python -m spacy download es_core_news_sm
```

### Error: `wordcloud` no instalado

Instala:

```powershell
python -m pip install wordcloud
```

### `pysentimiento` tarda mucho / descarga modelos

Es normal en la primera ejecución. Si quieres acelerar pruebas, puedes saltarte temporalmente las celdas de sentimiento (pero entonces `sentiment_by_year.csv` no se generará).

