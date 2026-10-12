# Laboratorio 3.4: Entrenamiento de un modelo de Machine Learning (Amazon SageMaker) ¿Que hice y por que?

## 1. Ingreso al laboratorio

Entré al laboratorio 3.4 desde AWS Academy y le di Start Lab para activar el entorno. Lo hice porque ahí se crean los recursos de AWS (la instancia de SageMaker y el bucket de S3) que se usan en todo el ejercicio.

## 2. Apertura de JupyterLab

En la consola de AWS fui a Amazon SageMaker AI, Notebooks y abrí JupyterLab de la instancia MyNotebook para proceder con la ejecucion del laboratorio.

## 3. Apertura del notebook
Abrí el archivo 3_4-machinelearning.ipynb y escribí mi nombre en la primera celda para identificar el trabajo. Este notebook trae las instrucciones y el código de la práctica: dividir los datos en entrenamiento, validación y prueba, y entrenar un modelo XGBoost.

## 4. Inicialización del repositorio con Git
Desde la terminal de JupyterLab entré a la carpeta SageMaker/es_es y ejecuté:

-git init: crear el repositorio local.
-git remote add origin ... y git fetch origin --prune: conectarlo con mi repositorio de GitHub y traer sus ramas.
-git checkout -b dev_dcharry55: crear mi rama de trabajo.

Trabajé en una rama aparte para no tocar main y llevar el control de versiones de mi avance.

## 5. Importación de datos

En el notebook ejecuté las celdas base:

- Instalé scipy` y scikit-learn.
- Importé las librerías (pandas, arff, boto3, etc.).
- Descargué el conjunto de datos de columna vertebral (UCI) y lo cargué en un DataFrame.
- Convertí la clase a números: Abnormal = 1 y Normal = 0.

Esto deja los datos listos para analizarlos y para que el algoritmo los pueda procesar.

## 6. Status e inicio del primer commit

Se uso git status vi que los archivos estaban sin seguimiento. Luego hice git add . y git commit -m "Primer procedimiento del laboratorio 3.4". Git pidió identificarme, así que configuré mi nombre y correo con git config --global. Con eso quedó guardado el primer punto de control y el árbol de trabajo limpio.

## 7. Paso 1: Explorar los datos

Se uso df.shape y df.columns. El resultado fue 310 filas y 7 columnas: seis características biomecánicas y la columna objetivo class. Quise confirmar el tamaño del conjunto y qué variables tenía antes de prepararlo.

## 8. 9. 10. Paso 2: Preparación de Datos y Pipeline hacia Amazon S3

Para garantizar que el dataset cumpla con las especificaciones del framework y mantener la reproducibilidad de las pruebas, se realizaron las siguientes etapas:

**Formato del modelo (XGBoost):** Se reordenó el dataset colocando la columna objetivo class en la primera posición (index 0), cumpliendo así con el requerimiento de entrada que exige Amazon SageMaker para el algoritmo XGBoost.

**Estrategia de particionado:** Mediante train_test_split y fijando una semilla (random_state=42), dividí los datos en un 80% para entrenamiento (248 instancias) y el 20% restante se distribuyó equitativamente entre los conjuntos de prueba y validación (31 instancias cada uno).

**Control de sesgo (Estratificación):** Debido al tamaño reducido de la muestra, apliqué el parámetro stratify para preservar el balance entre clases. Posteriormente, verifiqué con shape y value_counts() que los tres subconjuntos mantuvieran exactamente la misma distribución proporcional.

**Persistencia en la nube (S3):** Se exportaron los conjuntos a formato CSV sin encabezados ni índices. Mediante una función personalizada, se automatizó la carga de los archivos train, test y validate hacia la ruta lab3 del bucket en S3, dejando el pipeline listo para ser consumido por el Job de entrenamiento.

## 11. Paso 3: Entrenar el modelo (configuración)

Para el entrenamiento del modelo, obtuve el URI del contenedor de XGBoost mediante retrieve y definí los hiperparámetros iniciales (num_round=42, eval_metric=auc y objective=binary:logistic) como valores de partida aptos para un problema de clasificación binaria; con esto configurado, instancié el estimador de SageMaker especificando el tipo de instancia ml.m4.xlarge junto con la ruta de salida en Amazon S3, y finalmente establecí los canales de entrada apuntando directamente a los archivos CSV de entrenamiento y validación almacenados en la nube para dejar listo el Job de entrenamiento.

## 12. Ejecución final del entrenamiento

Ejecuté el entrenamiento con xgb_model.fit(...). En los logs de la consola pude ver cómo SageMaker preparó la instancia, descargó los datos junto con la imagen del contenedor, entrenó el algoritmo y subió el modelo final a S3 antes de cerrar con el mensaje Training job completed. Con esto, el modelo quedó completamente entrenado, guardado en la nube y listo para ser evaluado en las siguientes prácticas.

## 13. Commits realizados
Fui guardando el avance en GitHub después de cada paso, con git add ., git commit -m "..." y git push origin dev_dcharry55:

- Paso 1: exploración de datos con shape y columns.
- Paso 2: preparación y división estratificada de datos.
- Paso 3: entrenamiento y configuración del modelo XGBoost.
