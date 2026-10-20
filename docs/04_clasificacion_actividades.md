# 04. Clasificación de actividades humanas con Random Forest

**Taller: IA para sensores — IBERSENSOR 2026**  
**Universidad del Norte, Barranquilla — 21 de octubre de 2026**

## 1. Objetivo

Comprender cómo utilizar características extraídas de señales de acelerómetro para identificar automáticamente actividades humanas mediante técnicas de aprendizaje automático supervisado.

Al finalizar esta guía, el participante podrá:

1. Formular el reconocimiento de actividades humanas como un problema de clasificación multiclase.
2. Identificar las variables predictoras y la variable objetivo.
3. Preparar un conjunto de datos WISDM en formato ARFF.
4. Entrenar un clasificador Random Forest.
5. Evaluar su desempeño mediante distintas métricas.
6. Interpretar una matriz de confusión.
7. Reconocer las limitaciones de la evaluación con datos de un único sujeto.

## 2. Reconocimiento de actividades humanas (HAR)

El reconocimiento de actividades humanas, conocido como *Human Activity Recognition* (HAR), consiste en identificar la actividad realizada por una persona a partir de información obtenida mediante sensores.

En nuestro caso utilizaremos las mediciones de un acelerómetro integrado en un smartphone.

El problema puede expresarse como:

$$
f(\mathbf{x})=\hat{y}
$$

donde:

- $\mathbf{x}$ representa las características extraídas de una ventana de mediciones.
- $f$ es el modelo de clasificación.
- $\hat{y}$ es la actividad predicha.

Por ejemplo, a partir de características relacionadas con la aceleración, el modelo podría identificar si una persona está caminando, trotando, sentada o de pie.

### 2.1. Clasificación multiclase

WISDM contiene diferentes actividades identificadas mediante códigos.

| Código | Actividad |
|---|---|
| A | Caminar |
| B | Trotar |
| C | Escaleras |
| D | Sentado |
| E | De pie |

Estas son solamente cinco de las 18 actividades disponibles.

Cuando el clasificador debe elegir entre más de dos actividades, hablamos de un problema de **clasificación multiclase**.

## 3. Datos utilizados

Utilizaremos el archivo:

`data_1603_accel_phone.arff`

Este archivo corresponde a características procesadas del acelerómetro del smartphone del sujeto 1603.

Los datos están disponibles en:

https://github.com/eduardozurek/ia/tree/main/_005_clustering/WISDM

Cada fila contiene características calculadas sobre una ventana de mediciones, no una lectura instantánea del acelerómetro.

### 3.1. Variables predictoras y variable objetivo

El archivo contiene 93 columnas:

| Tipo | Columnas | Cantidad |
|---|---|---:|
| Variable objetivo | `ACTIVITY` | 1 |
| Identificador del sujeto | `class` | 1 |
| Características del movimiento | Todas las demás | 91 |

La variable objetivo será:

$$
y=\text{ACTIVITY}
$$

El vector de características será:

$$
\mathbf{x}=[x_1,x_2,\ldots,x_{91}]
$$

La columna `class` identifica a la persona y **no debe utilizarse como variable predictora**.

Tampoco debe incluirse `ACTIVITY` entre las características de entrada, porque contiene precisamente la información que queremos predecir.

### 3.2. Familias de características

Las 91 características están agrupadas en distintas familias:

| Familia | Ejemplos | Propósito |
|---|---|---|
| Histogramas | `X0`–`X9`, `Y0`–`Y9`, `Z0`–`Z9` | Representar la distribución de las aceleraciones |
| Estadísticas | `XAVG`, `XABSOLDEV`, `XSTANDDEV` | Describir valores centrales y dispersión |
| Picos y variabilidad | `XPEAK`, `XVAR` | Caracterizar patrones de movimiento |
| Coeficientes espectrales | `XMFCC0`–`XMFCC12` | Resumir información del dominio de la frecuencia |
| Relaciones entre ejes | `XYCOS`, `XYCOR` | Representar relaciones entre señales |
| Magnitud tridimensional | `RESULTANT` | Resumir el movimiento conjunto en los tres ejes |

Para conocer cada familia con mayor detalle, consulta [03. Dataset WISDM](03_dataset_wisdm.md).

## 4. Flujo de trabajo

El procedimiento que seguiremos será:

```text
Archivo ARFF de WISDM
          |
          v
Lectura en Pandas
          |
          v
Exploración y validación
          |
          v
Selección de X e y
          |
          v
Separación entrenamiento/prueba
          |
          v
Entrenamiento Random Forest
          |
          v
Predicción de actividades
          |
          v
Evaluación del desempeño
```

La separación entre entrenamiento y prueba es fundamental: necesitamos evaluar el clasificador utilizando ejemplos que no haya utilizado para aprender.

## 5. Lectura del archivo ARFF

Podemos cargar el archivo directamente desde GitHub.

```python
import pandas as pd

from scipy.io import arff
from io import StringIO
from urllib.request import urlopen


def leer_wisdm(sujeto, sensor="accel", dispositivo="phone"):

    base = (
        "https://raw.githubusercontent.com/"
        "eduardozurek/ia/main/_005_clustering/WISDM/"
    )

    archivo = f"data_{sujeto}_{sensor}_{dispositivo}.arff"

    with urlopen(base + archivo) as respuesta:
        contenido = respuesta.read().decode("utf-8")

    datos, metadata = arff.loadarff(StringIO(contenido))

    df = pd.DataFrame(datos)

    df.columns = df.columns.str.strip('"')

    for columna in df.select_dtypes(include=["object"]).columns:
        df[columna] = df[columna].map(
            lambda valor: (
                valor.decode("utf-8")
                if isinstance(valor, bytes)
                else valor
            )
        )

    return df
```

Cargamos los datos del sujeto 1603:

```python
SUJETO = 1603

df = leer_wisdm(SUJETO)

print("Dimensiones:", df.shape)
display(df.head())
```

## 6. Exploración inicial

Antes de entrenar el modelo, debemos comprender los datos.

### 6.1. Examinar los atributos

```python
print(df.columns.tolist())
print(df.dtypes)
```

### 6.2. Verificar el sujeto

```python
print(df["class"].unique())
```

Para este ejemplo esperamos encontrar únicamente el identificador 1603.

### 6.3. Identificar las actividades

```python
print(df["ACTIVITY"].unique())
```

### 6.4. Examinar la distribución de clases

```python
frecuencias = df["ACTIVITY"].value_counts().sort_index()

display(frecuencias)
```

Podemos representar gráficamente esta distribución:

```python
import matplotlib.pyplot as plt

frecuencias.plot(kind="bar")

plt.title("Distribución de actividades")
plt.xlabel("Actividad")
plt.ylabel("Número de ventanas")
plt.grid(axis="y", alpha=0.3)
plt.show()
```

Esta información es importante porque un conjunto de datos desbalanceado puede afectar el entrenamiento y la interpretación de las métricas.

## 7. Preparación de los datos

### 7.1. Selección de características

```python
X = df.drop(columns=["ACTIVITY", "class"])

y = df["ACTIVITY"]
```

Verificamos las dimensiones:

```python
print("X:", X.shape)
print("y:", y.shape)
print("Número de características:", X.shape[1])
```

Esperamos obtener 91 características predictoras.

### 7.2. Verificación de valores faltantes

```python
print("Valores faltantes:", X.isna().sum().sum())
```

Si existen valores faltantes, deben tratarse antes del entrenamiento. No debemos eliminar filas o imputar valores sin comprobar cómo afectará esto a las actividades disponibles.

### 7.3. Selección de actividades

Para una primera demostración podemos utilizar cinco actividades:

```python
actividades = ["A", "B", "C", "D", "E"]

mascara = y.isin(actividades)

X = X.loc[mascara].copy()
y = y.loc[mascara].copy()
```

Esta selección es opcional. También podemos experimentar con las 18 actividades.

Es importante utilizar el mismo conjunto de actividades cuando comparemos los resultados de diferentes grupos.

## 8. Separación entre entrenamiento y prueba

En aprendizaje supervisado se acostumbra dividir los ejemplos en dos conjuntos:

- **Entrenamiento:** se utiliza para construir el modelo.
- **Prueba:** se utiliza para estimar su desempeño sobre ejemplos no utilizados durante el entrenamiento.

Una división frecuente es:

$$
80\%\ \text{entrenamiento}
$$

$$
20\%\ \text{prueba}
$$

### 8.1. Ejemplo con scikit-learn

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X,
    y,
    test_size=0.20,
    random_state=42,
    stratify=y
)
```

El parámetro `stratify=y` ayuda a conservar aproximadamente la proporción de las actividades en ambos conjuntos.

**Advertencia metodológica:** esta división aleatoria se presenta como un ejemplo introductorio del funcionamiento de scikit-learn. No garantiza una evaluación independiente cuando los ejemplos proceden de ventanas temporalmente relacionadas.

En los archivos ARFF procesados, además, debemos comprobar si existen identificadores de ventana, marcas temporales o información suficiente para reconstruir el orden de adquisición.

Si no es posible verificar la independencia de las ventanas, los resultados deben describirse como una evaluación exploratoria, no como una estimación rigurosa de generalización.

## 9. Clasificador Random Forest

Random Forest es un algoritmo de aprendizaje supervisado basado en un conjunto de árboles de decisión.

En lugar de utilizar un único árbol, construye múltiples árboles y combina sus predicciones.

En clasificación, cada árbol emite una predicción y el bosque agrega las predicciones para obtener la clase final.

### 9.1. Ventajas

Random Forest presenta características convenientes para este taller:

- Permite trabajar con numerosas características.
- Puede representar relaciones no lineales.
- No requiere normalización de las variables para funcionar correctamente.
- Ofrece estimaciones de importancia de características.
- Es relativamente sencillo de utilizar con scikit-learn.

### 9.2. Entrenamiento

```python
from sklearn.ensemble import RandomForestClassifier

modelo = RandomForestClassifier(
    n_estimators=100,
    random_state=42,
    n_jobs=-1
)

modelo.fit(X_train, y_train)
```

El parámetro `n_estimators=100` indica que se construirán 100 árboles.

El parámetro `random_state=42` ayuda a reproducir los resultados.

El parámetro `n_jobs=-1` permite utilizar los procesadores disponibles para las operaciones que admiten paralelización.

### 9.3. Predicción

```python
y_pred = modelo.predict(X_test)
```

El resultado contiene la actividad predicha para cada ejemplo del conjunto de prueba.

## 10. Evaluación del clasificador

No es suficiente entrenar un modelo. También debemos determinar qué tan bien clasifica las actividades.

### 10.1. Accuracy

La exactitud mide la proporción de predicciones correctas:

$$
\text{Accuracy}=
\frac{\text{Número de predicciones correctas}}
{\text{Número total de predicciones}}
$$

En Python:

```python
from sklearn.metrics import accuracy_score

accuracy = accuracy_score(y_test, y_pred)

print(f"Accuracy: {accuracy:.4f}")
```

Una exactitud elevada no garantiza que el clasificador funcione igualmente bien para todas las actividades.

### 10.2. Precision

Para una clase determinada:

$$
\text{Precision}=\frac{TP}{TP+FP}
$$

Mide qué proporción de los ejemplos predichos como pertenecientes a esa clase realmente pertenecen a ella.

### 10.3. Recall

$$
\text{Recall}=\frac{TP}{TP+FN}
$$

Mide qué proporción de los ejemplos reales de una clase son identificados correctamente.

### 10.4. F1-score

El F1-score combina Precision y Recall mediante su media armónica:

$$
F_1=
2\frac{\text{Precision}\cdot\text{Recall}}
{\text{Precision}+\text{Recall}}
$$

### 10.5. F1-macro

En clasificación multiclase podemos calcular un F1-score para cada actividad y después obtener su promedio:

$$
F_{1,\text{macro}}=
\frac{1}{K}\sum_{k=1}^{K}F_{1,k}
$$

donde $K$ representa el número de actividades.

Esta métrica asigna el mismo peso a cada actividad, independientemente de su número de ejemplos.

```python
from sklearn.metrics import f1_score

f1_macro = f1_score(
    y_test,
    y_pred,
    average="macro",
    zero_division=0
)

print(f"F1-macro: {f1_macro:.4f}")
```

### 10.6. Reporte completo

```python
from sklearn.metrics import classification_report

print(
    classification_report(
        y_test,
        y_pred,
        zero_division=0
    )
)
```

El reporte incluye Precision, Recall, F1-score y soporte para cada actividad.

El *support* corresponde al número de ejemplos reales de cada clase en el conjunto evaluado.

## 11. Matriz de confusión

La matriz de confusión permite identificar qué actividades clasifica correctamente el modelo y cuáles confunde.

Para un problema con $K$ actividades, la matriz tiene dimensiones:

$$
K\times K
$$

Cada elemento $C_{ij}$ indica cuántos ejemplos de la clase real $i$ fueron clasificados como clase $j$.

Los valores de la diagonal principal representan predicciones correctas.

### 11.1. Visualización

```python
from sklearn.metrics import ConfusionMatrixDisplay

ConfusionMatrixDisplay.from_predictions(
    y_test,
    y_pred,
    cmap="Blues",
    xticks_rotation=45
)

plt.title("Matriz de confusión")
plt.show()
```

### 11.2. Interpretación

La matriz permite responder preguntas como:

- ¿Se confunden caminar y trotar?
- ¿Se identifican correctamente las actividades estáticas?
- ¿Qué actividad presenta más errores?
- ¿Existen clases que el modelo prácticamente nunca predice?

Es conveniente analizar estas preguntas junto con las métricas por clase.

## 12. Importancia de las características

Random Forest permite estimar la importancia relativa de los atributos utilizados en sus árboles.

```python
importancias = pd.Series(
    modelo.feature_importances_,
    index=X.columns
).sort_values(ascending=False)

display(importancias.head(15))
```

Podemos visualizar las 15 características más importantes:

```python
importancias.head(15).sort_values().plot(
    kind="barh",
    figsize=(8, 6)
)

plt.title("Características más importantes")
plt.xlabel("Importancia")
plt.tight_layout()
plt.show()
```

**Precaución:** `feature_importances_` utiliza una medida basada en la reducción de impureza. Puede favorecer ciertos atributos y repartir la importancia entre variables correlacionadas. Por ello, no debe interpretarse automáticamente como una medida de causalidad.

Una alternativa para un análisis posterior es la importancia por permutación (*permutation importance*).

## 13. Comparación de diferentes conjuntos de características

Una pregunta interesante es si necesitamos utilizar las 91 características.

Podemos comparar tres configuraciones:

| Experimento | Características |
|---|---|
| 1 | Estadísticas básicas |
| 2 | Estadísticas e histogramas |
| 3 | Todas las características |

Para realizar una comparación válida debemos conservar los mismos ejemplos, la misma partición y los mismos hiperparámetros del modelo.

Esto permite estudiar cómo cambia el desempeño cuando incorporamos más información de las señales.

No debe suponerse que un mayor número de características producirá necesariamente mejores resultados.

## 14. Comparación entre sujetos

En el taller, cada grupo utilizará un sujeto diferente.

Por ejemplo:

| Grupo | Sujeto | Accuracy | F1-macro |
|---|---|---|---|
| Instructor | 1603 | Por calcular | Por calcular |
| Grupo 1 | 1604 | Por calcular | Por calcular |
| Grupo 2 | 1605 | Por calcular | Por calcular |
| Grupo 3 | 1606 | Por calcular | Por calcular |

Cada grupo entrenará y evaluará su propio clasificador utilizando los datos de la persona asignada.

### 14.1. ¿Qué estamos comparando?

Los resultados pueden variar por razones como:

- Diferencias en la forma de caminar o realizar otras actividades.
- Diferencias en la orientación o ubicación del teléfono.
- Cantidad y distribución de ejemplos por actividad.
- Variabilidad de las señales registradas.
- Dificultad de distinguir determinadas actividades.

### 14.2. Limitación de la evaluación intra-sujeto

Como cada clasificador utiliza datos de una sola persona, los resultados corresponden a una evaluación **intra-sujeto**.

Esto significa que no estamos comprobando si un modelo entrenado con una persona funciona correctamente con otra.

Para evaluar esa capacidad sería necesario diseñar un experimento **inter-sujeto**, separando completamente las personas utilizadas para entrenamiento y prueba.

Asimismo, aunque todos los grupos utilicen la misma partición aleatoria, no puede asegurarse independencia temporal sin información suficiente sobre la construcción de las ventanas.

## 15. Actividad práctica para los participantes

Cada grupo deberá:

1. Seleccionar el sujeto asignado.
2. Cargar el archivo ARFF de su acelerómetro.
3. Verificar las dimensiones y las actividades disponibles.
4. Seleccionar las características y la variable objetivo.
5. Entrenar un clasificador Random Forest.
6. Obtener las predicciones.
7. Calcular Accuracy y F1-macro.
8. Construir la matriz de confusión.
9. Identificar las características más importantes.
10. Comparar los resultados con los demás grupos.

### Resultados que deben presentar

| Elemento | Resultado |
|---|---|
| Sujeto utilizado | |
| Número de ejemplos | |
| Número de actividades | |
| Número de características | |
| Accuracy | |
| F1-macro | |
| Actividad con mejor F1-score | |
| Actividad con menor F1-score | |
| Característica más importante | |

## 16. Preguntas de reflexión

1. ¿Por qué necesitamos separar los datos de entrenamiento y prueba?
2. ¿Cuál es la diferencia entre Accuracy y F1-macro?
3. ¿Por qué pueden confundirse dos actividades distintas?
4. ¿Qué ventajas ofrece Random Forest frente a un único árbol de decisión?
5. ¿Es necesario utilizar las 91 características para obtener buenos resultados?
6. ¿Por qué los resultados pueden cambiar entre sujetos?
7. ¿Qué significa que un modelo tenga un buen desempeño intra-sujeto?
8. ¿Podemos afirmar que nuestro modelo funcionará correctamente con personas que no participaron en el entrenamiento?
9. ¿Qué consecuencias puede tener la dependencia temporal entre ventanas?
10. ¿Cómo diseñaríamos una evaluación que permita medir la generalización a sujetos desconocidos?

## 17. Referencias

1. Weiss, G. M. (2019). *WISDM Smartphone and Smartwatch Activity and Biometrics Dataset*. UCI Machine Learning Repository. https://doi.org/10.24432/C5HK59

2. [Scikit-learn — RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html)

3. [Scikit-learn — Classification Metrics](https://scikit-learn.org/stable/modules/model_evaluation.html#classification-metrics)

4. [Scikit-learn — Train Test Split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)

5. [Scikit-learn — Permutation Importance](https://scikit-learn.org/stable/modules/permutation_importance.html)

---

**Taller: IA para sensores — IBERSENSOR 2026**  
Universidad del Norte, Barranquilla, Colombia  
21 de octubre de 2026

[← Dataset WISDM](03_dataset_wisdm.md) | [Volver al inicio del taller](../README.md)
