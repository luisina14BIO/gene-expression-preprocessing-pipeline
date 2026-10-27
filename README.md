# Pipeline de Preprocesamiento y Clasificación de Datos de Expresión Génica

## Descripción del proyecto

Proyecto de aprendizaje automático desarrollado con Python y Scikit-Learn para construir un pipeline de preprocesamiento y clasificación binaria a partir de un dataset sintético de expresión génica y características demográficas.

El objetivo es integrar la preparación de los datos, el entrenamiento, la evaluación y la persistencia del modelo en un flujo reproducible.

## Objetivos

* Auditar la calidad de los datos, incluyendo valores faltantes y registros duplicados.
* Separar las variables numéricas y categóricas mediante `ColumnTransformer`.
* Integrar el preprocesamiento y el estimador en un `Pipeline`.
* Evitar la fuga de información mediante una división previa entre entrenamiento y prueba.
* Evaluar el modelo con métricas adecuadas para un problema de clasificación desbalanceado.
* Guardar el pipeline entrenado para reutilizarlo posteriormente.

## Dataset

Se utilizaron 1.000 registros sintéticos con ocho variables originales:

* Identificador del paciente.
* Edad y género.
* Expresión de dos genes.
* Estado de tabaquismo.
* Estado de enfermedad.
* Respuesta al tratamiento.

La variable objetivo se transformó en una clasificación binaria:

* `0`: paciente sano.
* `1`: paciente con enfermedad.

La distribución original de la variable objetivo estaba desbalanceada: 96,2 % de pacientes sanos y 3,8 % de pacientes con enfermedad.

Durante la auditoría se detectó una asociación determinista entre `SmokingStatus` y `DiseaseStatus`. Por este motivo, se excluyó `SmokingStatus` de los predictores para evitar que esa relación artificial dominara el entrenamiento. También se excluyeron `PatientID` y `TreatmentResponse`.

**Limitación:** al tratarse de datos sintéticos, los patrones encontrados no deben interpretarse como evidencia clínica ni como relaciones biológicas reales.

## Metodología

1. **Exploración y auditoría:** revisión de dimensiones, tipos de datos, valores faltantes, duplicados y distribución de clases.
2. **Preparación:** definición de las variables predictoras y de la variable objetivo.
3. **División de datos:** separación en entrenamiento (80 %) y prueba (20 %), utilizando `stratify=y` para conservar la proporción de clases.
4. **Preprocesamiento numérico:** imputación por mediana y estandarización con `StandardScaler`.
5. **Preprocesamiento categórico:** imputación por la categoría más frecuente y codificación con `OneHotEncoder(handle_unknown="ignore")`.
6. **Modelado:** regresión logística con `class_weight="balanced"` para asignar mayor peso a la clase minoritaria.
7. **Evaluación:** análisis de la matriz de confusión, precision, recall, F1-score, balanced accuracy, ROC-AUC y Average Precision.
8. **Persistencia:** exportación del pipeline completo mediante `joblib`.

Las transformaciones se ajustaron exclusivamente con el conjunto de entrenamiento para reducir el riesgo de fuga de información.

## Resultados y limitaciones

En la partición de prueba se obtuvo:

| Métrica                        | Resultado |
| ------------------------------ | --------: |
| Accuracy                       |      0,99 |
| Precision de la clase positiva |      0,80 |
| Recall de la clase positiva    |      1,00 |
| F1-score de la clase positiva  |      0,89 |
| Balanced accuracy              |     0,995 |
| ROC-AUC                        |      1,00 |
| Average Precision              |      1,00 |

El conjunto de prueba contiene 200 registros, incluidos únicamente ocho casos positivos. Aunque las métricas fueron elevadas, el tamaño reducido de la clase minoritaria y las características artificiales del dataset limitan la capacidad de generalizar estos resultados.

Por lo tanto, los resultados se presentan como una evaluación metodológica del pipeline y no como evidencia de capacidad predictiva clínica.

## Estructura del repositorio

```text
gene-expression-preprocessing-pipeline/
├── data/
│   └── Gene Expression Analysis and Disease Relationship.csv
├── notebooks/
│   └── 01_preprocessing_pipeline.ipynb
├── models/
│   └── pipeline_clasificacion.joblib
├── README.md
├── requirements.txt
└── .gitignore
```

## Tecnologías utilizadas

* Python
* pandas y NumPy
* Scikit-Learn
* Matplotlib y Seaborn
* Joblib
* Jupyter Notebook

## Reproducibilidad

1. Clonar el repositorio.
2. Instalar las dependencias indicadas en `requirements.txt`.
3. Ejecutar el notebook ubicado en `notebooks/`.
4. Entrenar y evaluar el pipeline.
5. Guardar el modelo entrenado en `models/`.

El archivo `pipeline_clasificacion.joblib` contiene el preprocesamiento y el estimador entrenado, por lo que puede cargarse posteriormente con `joblib.load()` para realizar predicciones sobre datos nuevos que tengan las mismas columnas de entrada.

**Nota:** los archivos serializados con Joblib deben cargarse únicamente desde fuentes confiables y utilizando versiones compatibles de las dependencias.
