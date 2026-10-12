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


# Laboratorio 3.5: Machine Learning Pipeline with Amazon SageMaker - ¿Qué hice y por qué?

## 1. Ingreso al laboratorio

Lo primero que hice fue abrir la plataforma de AWS Academy para revisar la pantalla inicial y echarle un vistazo a los objetivos generales. Esto me sirvió para entender bien de qué trataba la práctica: básicamente, poner en marcha un modelo de machine learning y hacer transformaciones por lotes sobre un conjunto de datos de prueba.

## 2. Apertura del JupyterLab 3.5

Entré al entorno de trabajo en Amazon SageMaker Studio y me aseguré de que la instancia ml.m4.xlarge estuviera activa (InService). Hice esto para tener garantizados todos los recursos de cómputo y poder ejecutar las celdas y el código del laboratorio sin contratiempos.

## 3. Notebook

Abrí el cuaderno de notas del estudiante para meterme un poco en contexto con el escenario empresarial. Quería conocer bien las características de los datos biomédicos de los pacientes ortopédicos con los que íbamos a trabajar en todo el proceso de clasificación.

## 4. Inicialización del repositorio GitHub

Me pasé a la terminal de SageMaker para inicializar Git, sincronizar mi rama de trabajo (dev_dcharry55) y conectarla con el repositorio remoto de GitHub. De esta manera, me aseguré de mantener un control de versiones ordenado desde el primer minuto.

## 5.6. Importación de los datos y configuración del entrenamiento del modelo XGBoost

Instalé scipy y scikit-learn para dejar listo el entorno. Luego descargué los datos de la columna vertebral, convertí la clase a números (Abnormal 1, Normal 0), los dividí en entrenamiento, prueba y validación (80%, 10%, 10%) y los guardé como CSV en S3. Con eso entrené el modelo XGBoost de clasificación binaria, y el mensaje “ready for hosting!” confirmó que quedó listo para desplegarse.
## 7. Status e inicio del primer commit

Revisé cómo iban los archivos usando git status y armé el primer paquete de cambios con git ad` y git commit. Con esto dejé registrado formalmente en el historial que la fase inicial de preparación y carga de datos ya estaba completada.

## 8. Paso 1: Alojar el modelo

Desplegué el modelo entrenado corriendo la función xgb_model.deploy(). El propósito de esto fue levantar un punto de enlace (endpoint) en tiempo real dentro de SageMaker, permitiendo que el modelo quedara en línea y listo para recibir consultas externas de forma inmediata.

## 9. y 10. Paso 2: Realizar predicciones

Antes de hacer cualquier consulta, revisé la estructura del conjunto de datos de prueba apoyándome en .shape y .head() para entender bien las dimensiones y asegurarme de cómo venían organizadas las características. Una vez todo estuvo claro, aislé una sola fila como muestra, la transformé al formato CSV que requiere el modelo y la envié al endpoint usando xgb_predictor.predict(), lo que me permitió comprobar en tiempo real que el servicio estaba respondiendo correctamente y obtener su primera puntuación de probabilidad.

## 11. Paso 3: Terminar el modelo implementado

Como ya había terminado de hacer las pruebas individuales, ejecuté xgb_predictor.delete_endpoint(). Hice esto para borrar el punto de enlace y apagar la instancia activa, evitando así consumir recursos de más en la nube y generar costos innecesarios.

## 12. 13. y 14. Paso 4: Realizar una transformación por lotes

Primero separé las características del conjunto de prueba, guardé el archivo como batch-in.csv en Amazon S3 y armé un objeto transformer en SageMaker para correr todas las predicciones de un solo golpe y de forma masiva. Después de que terminó el proceso, descargué los resultados de vuelta a un DataFrame de Pandas y les aplicué la función de conversión binaria con un umbral de `0.65`, lo que me ayudó a transformar esas probabilidades continuas en ceros y unos definitivos para comprobar los resultados finales.

## 15. Tarea desafío

Para resolver el desafío, modifiqué la función de conversión y le subí el umbral a 0.99. Quería ver qué pasaba con las decisiones del modelo al ponerle un nivel de exigencia tan alto, obligándolo a que solo clasificara como positivos (`1`) a los casos de los que estuviera prácticamente 100% seguro.

¿Afecta los resultados?

Sí, cambiar el umbral afecta por completo los resultados. Al subir la valla a 0.99, el modelo se vuelve extremadamente exigente, ya que solo clasifica como positivo (1) aquellas instancias de las que está prácticamente seguro al 100%, mientras que cualquier probabilidad por debajo de ese límite drásticamente alto la convierte en cero (0). En contraste con un umbral más moderado (como 0.5 o 0.65), este ajuste altera significativamente la proporción de clasificaciones finales, demostrando que mover el límite modifica de forma directa el criterio con el que el modelo toma sus decisiones.

## 16. Commits realizados

Volví a la terminal para correr en orden los comandos git add, git commit y git push de cada una de las etapas que fui completando. Así logré mantener la rama dev_dcharry55 con un historial limpio, muy ordenado y fácil de rastrear.
Por último, hice el commit correspondiente a la prueba del cambio de umbral y subí todos los cambios al repositorio remoto en GitHub, cerrando y consolidando con éxito la entrega completa de la práctica.


## 17. Validación endpoint

Volví a desplegar el modelo y le envié la entrada de prueba para validar que responde bien con datos nuevos. El resultado fue 0.0169, que no es un 0 o un 1 directo sino una probabilidad, así que se interpreta con el umbral de 0.5: al ser menor, el modelo clasifica el caso como Normal (0), con alta confianza.

