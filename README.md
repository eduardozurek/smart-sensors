# IA para sensores

### Reconocimiento de actividades humanas mediante aprendizaje automático

**14th Ibero-American Congress on Sensors — IBERSENSOR 2026**

**Taller 1**

- **Fecha:** Miércoles 21 de octubre de 2026
- **Horario:** 11:30 a. m. – 1:00 p. m.
- **Duración:** 90 minutos
- **Lugar:** laboratorio de Ciencias de la Computación, Piso 6, Bloque K, Universidad del Norte, Barranquilla, Colombia
- **Instructor:** Eduardo Zurek, Ph.D.
- **E-mail:** ezurek@uninorte.edu.co
- **Orcid:** https://orcid.org/0000-0002-9816-6863
- **Institución:** Departamento de Ingeniería de Sistemas, Universidad del Norte

[Programa oficial de IBERSENSOR 2026](https://ibersensor2026.org/#programa)

---

## Descripción

Este taller se desarrolla en el marco del **14th Ibero-American Congress on Sensors (IBERSENSOR 2026)**.

La actividad explora cómo las herramientas de inteligencia artificial están transformando el procesamiento de datos provenientes de sensores, particularmente mediante la extracción de patrones y la generación de información útil para la toma de decisiones.

Como caso de estudio, utilizaremos **características previamente extraídas de señales del acelerómetro de un smartphone**, disponibles en los archivos procesados del dataset WISDM (formato ARFF), para desarrollar un sistema de reconocimiento de actividades humanas (*Human Activity Recognition — HAR*) mediante aprendizaje automático supervisado.

Los conceptos de acelerómetros y giroscopios se abordarán en la introducción teórica. El ejercicio práctico se concentrará en el acelerómetro del smartphone.

El taller combinará una demostración guiada por el instructor con un ejercicio práctico colaborativo en grupos de tres participantes.

Cada grupo trabajará con datos de una persona diferente, entrenará su propio clasificador y comparará los resultados obtenidos con los de los demás equipos.

## Objetivos del taller

1. Comprender los fundamentos de los sensores de movimiento y su aplicación al reconocimiento de actividades humanas.
2. Explorar los datos procesados del acelerómetro de un smartphone utilizando Python y Pandas.
3. Interpretar las características estadísticas y espectrales extraídas de las señales de aceleración.
4. Identificar las variables predictoras y la variable objetivo de un problema de clasificación multiclase.
5. Entrenar y evaluar un clasificador Random Forest mediante Accuracy, F1-macro y matriz de confusión.
6. Comparar los resultados obtenidos con diferentes sujetos y discutir las limitaciones de la evaluación intra-sujeto.


## Agenda

| Actividad | Duración |
|---|---|
| Introducción y fundamentos teóricos | 15 minutos |
| Demostración guiada | 30 minutos |
| Trabajo práctico en grupos de tres | 35 minutos |
| Comparación de resultados y conclusiones | 10 minutos |
| **Total** | **90 minutos** |

## Material del taller

### Guía teórica

- [Introducción a Smart Sensors](docs/01_smart_sensors.md)
- [Acelerómetros y giroscopios](docs/02_acelerometro_giroscopio.md)
- [Dataset WISDM](docs/03_dataset_wisdm.md)
- [Clasificación de actividades humanas](docs/04_clasificacion_actividades.md)

### Ejercicios prácticos

- [Ejemplo guiado por el instructor](notebooks/01_ejemplo_guiado.ipynb)
- [Trabajo colaborativo](notebooks/02_trabajo_en_grupo.ipynb)

## Dataset

**WISDM Smartphone and Smartwatch Activity and Biometrics Dataset**

[Acceder al dataset en UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/507/wisdm+smartphone+and+smartwatch+activity+and+biometrics+dataset)

## Herramientas

Python, Jupyter Notebook, NumPy, Pandas, Matplotlib y Scikit-learn.

---

## Información del congreso

**14th Ibero-American Congress on Sensors — IBERSENSOR 2026**

21–23 de octubre de 2026  
Barranquilla y Cartagena de Indias, Colombia

[Sitio oficial del congreso](https://ibersensor2026.org/) | [Programa del evento](https://ibersensor2026.org/#programa)

