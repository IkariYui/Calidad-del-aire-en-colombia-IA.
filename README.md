# 🌎 Calidad del Aire en Colombia — Machine Learning

Proyecto de análisis y clasificación de la calidad del aire en Colombia utilizando técnicas de **Machine Learning**, análisis exploratorio de datos y visualización geográfica.

El proyecto utiliza información pública sobre calidad del aire disponible en **datos.gov.co** y desarrolla un modelo de **Random Forest Classifier** para clasificar los registros de calidad del aire en tres categorías: **Buena, Moderada y Mala**.

---

## 📌 Descripción del proyecto

El objetivo del proyecto es analizar datos históricos de diferentes estaciones de monitoreo de calidad del aire en Colombia y desarrollar un modelo de clasificación capaz de asignar una categoría de calidad del aire a cada registro.

El flujo de trabajo incluye:

* Carga y exploración del dataset.
* Análisis de variables y valores faltantes.
* Limpieza y preparación de los datos.
* Tratamiento de valores atípicos.
* Análisis estadístico y de correlaciones.
* Creación de una variable objetivo para la calidad del aire.
* Entrenamiento de un modelo **Random Forest**.
* Evaluación mediante métricas de clasificación.
* Visualización de la clasificación sobre un mapa interactivo de Colombia.

---

## 📊 Dataset

Los datos utilizados provienen del portal oficial de datos abiertos de Colombia:

**Fuente:** [Datos.gov.co — Calidad del Aire en Colombia: Promedio Anual](https://www.datos.gov.co/Ambiente-y-Desarrollo-Sostenible/Calidad-Del-Aire-En-Colombia-Promedio-Anual-/kekd-7v7h/about_data)

El dataset inicial contiene:

* **28.732 registros**
* **28 variables**
* Información correspondiente a los años **2011–2023**
* Estaciones de monitoreo ubicadas en diferentes departamentos y municipios de Colombia.

Entre las variables disponibles se encuentran:

* Estación
* Autoridad ambiental
* Latitud y longitud
* Variable ambiental
* Tiempo de exposición
* Año
* Promedio
* Mediana
* Percentil 98
* Máximo
* Mínimo
* Días de excedencias
* Porcentaje de excedencias
* Departamento
* Municipio
* Tipo de estación

---

## 🧹 Preparación y limpieza de datos

Antes de entrenar el modelo se realizó un proceso de preparación de los datos.

### Valores faltantes

Se identificaron valores faltantes principalmente en:

* `Representatividad Temporal`
* `Código del Municipio`
* `Tipo de Estación`

La representatividad temporal fue imputada utilizando la mediana, mientras que el tipo de estación fue completado utilizando el valor más frecuente.

Los registros sin código de municipio fueron eliminados debido a que representaban una cantidad muy pequeña del dataset.

### Variables categóricas

Las variables categóricas fueron convertidas al tipo `category` de Pandas para facilitar su procesamiento.

### Eliminación de variables

Se eliminaron algunas variables consideradas poco relevantes para el modelo, entre ellas:

* `Fechas/horas del máximo`
* `Fechas/horas del mínimo`
* `Suma`

### Filtrado de datos

Se utilizaron registros con una `Representatividad Temporal` de al menos 50%.

También se corrigieron valores inconsistentes:

* Valores negativos en `Mínimo`.
* Valores superiores a 100 en `Representatividad Temporal`.

Posteriormente se aplicó el método **IQR (Interquartile Range)** para restringir valores atípicos en variables estadísticas relevantes.

---

## 🎯 Clasificación de la calidad del aire

Se creó una nueva variable llamada `calidad_aire`, utilizada como variable objetivo del modelo.

La clasificación se realizó utilizando el valor de `Promedio`:

|    Promedio | Clasificación |
| ----------: | ------------- |
|        ≤ 25 | 🟢 Buena      |
| > 25 y ≤ 50 | 🟠 Moderada   |
|        > 50 | 🔴 Mala       |

Esto permitió convertir el problema en una tarea de **clasificación multiclase**.

---

## 🤖 Machine Learning

### Algoritmo

Se utilizó:

**Random Forest Classifier**

Las principales variables utilizadas como características (`features`) fueron:

* `Mediana`
* `Percentil 98`
* `Máximo`
* `Días de excedencias`
* `Porcentaje excedencias limite actual`
* `Latitud`
* `Longitud`
* `Tiempo de exposición (horas)`
* `Tipo de Estación`

La variable categórica `Tipo de Estación` fue transformada mediante **One-Hot Encoding**.

---

## 🔬 Entrenamiento

Los datos fueron divididos utilizando:

* **80%** → entrenamiento
* **20%** → prueba
* `random_state = 42`
* División estratificada según la variable objetivo.

Inicialmente se intentó realizar una búsqueda de hiperparámetros mediante `GridSearchCV`.

La búsqueda contemplaba diferentes combinaciones de:

* Número de árboles.
* Profundidad máxima.
* Muestras mínimas para dividir un nodo.
* Muestras mínimas por hoja.
* Número de características utilizadas.

Sin embargo, la búsqueda completa requería evaluar **1.620 entrenamientos**, por lo que el proceso fue interrumpido.

Posteriormente se entrenó un Random Forest con:

```text
n_estimators = 200
random_state = 42
```

---

## 📈 Resultados

El modelo obtuvo:

**Accuracy: 99.51%**

Sobre el conjunto de prueba:

| Clase    | Precision | Recall | F1-Score |
| -------- | --------: | -----: | -------: |
| Buena    |      1.00 |   1.00 |     1.00 |
| Moderada |      1.00 |   0.99 |     1.00 |
| Mala     |      0.99 |   0.99 |     0.99 |

El conjunto de prueba estuvo compuesto por **2.846 registros**.

Además del accuracy y el classification report, se generó una **matriz de confusión** para analizar el comportamiento del modelo en cada categoría.

> **Nota:** El alto desempeño obtenido debe interpretarse teniendo en cuenta que la variable objetivo se construye directamente a partir de `Promedio` y varias características utilizadas por el modelo están relacionadas estadísticamente con las mediciones utilizadas para dicha clasificación. Por este motivo, el resultado no debe interpretarse automáticamente como una capacidad de predicción independiente para nuevos escenarios.

---

## 🗺️ Visualización geográfica

Como parte del proyecto se desarrolló un mapa interactivo utilizando **Folium**.

Las estaciones son representadas utilizando sus coordenadas geográficas:

* 🟢 **Buena**
* 🟠 **Moderada**
* 🔴 **Mala**

Además, cada estación contiene información como:

* Nombre de la estación.
* Municipio.
* Clasificación de calidad del aire.

Para facilitar la visualización de múltiples estaciones se utilizó `MarkerCluster`.

El resultado se guarda como:

```text
mapa_calidad_aire.html
```

---

## 🛠️ Tecnologías utilizadas

### Lenguaje

* Python

### Data Science

* Pandas
* NumPy
* Scikit-learn

### Visualización

* Matplotlib
* Seaborn
* Folium

### Machine Learning

* Random Forest
* GridSearchCV
* Train/Test Split
* Classification Report
* Confusion Matrix

### Entorno

* Google Colab
* Jupyter Notebook

---

## 📁 Estructura del proyecto

```text
Calidad-del-aire-en-colombia-IA/
│
├── Calidad_del_aire_en_Colombia.ipynb
├── dataset_limpio.csv
├── dataset.csv
├── mapa_calidad_aire.html
└── README.md
```

> Los nombres de los archivos pueden variar dependiendo de la versión del proyecto almacenada en el repositorio.

---

## 🚀 Cómo ejecutar el proyecto

### 1. Clonar el repositorio

```bash
git clone https://github.com/IkariYui/Calidad-del-aire-en-colombia-IA.git
```

### 2. Instalar dependencias

```bash
pip install pandas numpy matplotlib seaborn scikit-learn folium
```

### 3. Abrir el notebook

El proyecto fue desarrollado originalmente utilizando **Google Colab**.

Puedes abrir el archivo:

```text
Calidad_del_aire_en_Colombia.ipynb
```

y ejecutar las celdas en orden.

---

## 📚 Flujo del proyecto

```text
Datos públicos
      │
      ▼
Exploración del dataset
      │
      ▼
Limpieza y preparación
      │
      ▼
Tratamiento de valores atípicos
      │
      ▼
Análisis estadístico
      │
      ▼
Creación de categorías
      │
      ▼
Random Forest
      │
      ▼
Evaluación del modelo
      │
      ▼
Clasificación de estaciones
      │
      ▼
Mapa interactivo de Colombia
```

---

## 🎓 Objetivo académico

Este proyecto fue desarrollado como ejercicio práctico de **Inteligencia Artificial y Machine Learning**, aplicando conceptos de:

* Análisis exploratorio de datos.
* Preprocesamiento.
* Ingeniería básica de características.
* Clasificación supervisada.
* Entrenamiento y evaluación de modelos.
* Interpretación de resultados.
* Visualización de datos.
* Análisis geográfico.

---

## 👨‍💻 Autor

**Juan Guillermo Cuartas Valderrama**

Desarrollador de software e interesado en **Python, Data Analysis, Machine Learning, Inteligencia Artificial e infraestructura tecnológica**.

---

## 📄 Fuente de datos

Datos abiertos del Gobierno de Colombia:

**Datos.gov.co — Calidad del Aire en Colombia: Promedio Anual**

https://www.datos.gov.co/Ambiente-y-Desarrollo-Sostenible/Calidad-Del-Aire-En-Colombia-Promedio-Anual-/kekd-7v7h/about_data
