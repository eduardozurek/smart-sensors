# 03. Dataset WISDM: datos procesados en formato ARFF

**Taller: IA para sensores — IBERSENSOR 2026**  
**Universidad del Norte, Barranquilla — 21 de octubre de 2026**

## 1. Objetivo

Conocer y utilizar las características extraídas de señales de acelerómetro del dataset **WISDM Smartphone and Smartwatch Activity and Biometrics Dataset** para desarrollar un clasificador de actividades humanas (*Human Activity Recognition*, HAR).

El ejemplo guiado utiliza **`data_1603_accel_phone.arff`**, correspondiente al acelerómetro del smartphone del sujeto **1603**. Cada grupo podrá repetir el análisis con un sujeto diferente.

**Fuente del dataset:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/507/wisdm+smartphone+and+smartwatch+activity+and+biometrics+dataset).  
**Copia utilizada en el taller:** [Repositorio de GitHub](https://github.com/eduardozurek/ia/tree/main/_005_clustering/WISDM).

## 2. ¿Qué información contiene WISDM?

WISDM reúne mediciones de movimiento de smartphones y smartwatches mientras distintas personas realizan actividades. Incluye acelerómetros y giroscopios de tres ejes (X, Y, Z). La colección contempla **51 sujetos** y **18 actividades**; su frecuencia nominal de adquisición es de **20 Hz**.

Existen dos representaciones que conviene distinguir:

| Representación | Contenido | Uso habitual |
|---|---|---|
| Datos originales (`.txt`) | Mediciones sucesivas con marcas temporales y valores X, Y, Z | Visualización de señales, sincronización y extracción de características |
| Datos procesados (`.arff`) | Una fila por ventana, con características ya calculadas | Exploración, entrenamiento y evaluación de clasificadores |

**En este taller trabajaremos directamente con los archivos `.arff`.** Por ello, no es necesario reconstruir las señales originales ni implementar el cálculo de todas las características.

## 3. Archivos que utilizaremos

La copia del repositorio contiene archivos cuyo nombre identifica el sujeto, el sensor y el dispositivo. Por ejemplo:

```text
data_1603_accel_phone.arff
data_1603_gyro_phone.arff
data_1603_accel_watch.arff
data_1603_gyro_watch.arff
```

| Elemento del nombre | Significado |
|---|---|
| `1603` | Identificador del sujeto |
| `accel` / `gyro` | Acelerómetro / giroscopio |
| `phone` / `watch` | Smartphone / smartwatch |
| `.arff` | Formato Attribute-Relation File Format |

**Archivo principal:** `data_1603_accel_phone.arff`. No combinaremos inicialmente archivos de diferentes sensores: para hacerlo correctamente sería necesario comprobar la correspondencia de sus ventanas.

## 4. ¿Qué representa una fila?

Cada fila del ARFF representa **una ventana de mediciones del acelerómetro**, resumida mediante atributos numéricos. No es una lectura instantánea del sensor.

Las características incluyen histogramas, estadísticos descriptivos, coeficientes espectrales y relaciones entre ejes. Para este archivo se han identificado **93 columnas**:

- **91 características predictoras** del movimiento.
- **`ACTIVITY`**: actividad realizada, que queremos predecir.
- **`class`**: identificador de la persona que produjo los datos.

**Advertencia:** `class` no es la actividad objetivo en este ejercicio. En los archivos de un solo sujeto suele contener el mismo identificador en todas las filas. Debe excluirse de las variables predictoras, al igual que `ACTIVITY`.

### 4.1. Variable objetivo: `ACTIVITY`

WISDM utiliza códigos de actividad:

| Código | Actividad | Código | Actividad |
|---|---|---|---|
| A | Caminar | J | Comer pasta |
| B | Trotar | K | Beber de una taza |
| C | Escaleras | L | Comer un sándwich |
| D | Estar sentado | M | Patear un balón |
| E | Estar de pie | O | Lanzar/atrapar una pelota |
| F | Escribir en teclado | P | Driblar un balón |
| G | Cepillarse los dientes | Q | Escribir a mano |
| H | Comer sopa | R | Aplaudir |
| I | Comer papas fritas | S | Doblar ropa |

No existe un código N en esta lista. La disponibilidad efectiva de cada actividad debe comprobarse en el archivo elegido.

### 4.2. Identificador de sujeto: `class`

`class` contiene el código de la persona (por ejemplo, `1603`). Su papel depende del problema:

| Problema | Variable objetivo | Variables predictoras |
|---|---|---|
| **Reconocimiento de actividades (este taller)** | `ACTIVITY` | Las 91 características del acelerómetro |
| Identificación de personas (otro problema) | `class` | Características del movimiento de múltiples sujetos |

Para reconocer actividades **no se debe utilizar `class` como predictor**. Si se reunieran datos de varios sujetos, esa columna podría permitir al modelo memorizar identidades y distorsionar la evaluación.

## 5. Diccionario de características

Las 91 columnas predictoras se distribuyen así:

| Grupo | Columnas | Cantidad | Interpretación |
|---|---|---:|---|
| Histogramas | `X0`–`X9`, `Y0`–`Y9`, `Z0`–`Z9` | 30 | Distribución de valores por eje en diez intervalos |
| Medias | `XAVG`, `YAVG`, `ZAVG` | 3 | Promedio de cada eje |
| Picos | `XPEAK`, `YPEAK`, `ZPEAK` | 3 | Características relacionadas con la periodicidad o picos de la señal |
| Desviación absoluta | `XABSOLDEV`, `YABSOLDEV`, `ZABSOLDEV` | 3 | Dispersión absoluta respecto a la media |
| Desviación estándar | `XSTANDDEV`, `YSTANDDEV`, `ZSTANDDEV` | 3 | Variabilidad de cada eje |
| `VAR` | `XVAR`, `YVAR`, `ZVAR` | 3 | Características de variabilidad; verificar definición exacta del extractor |
| MFCC | `XMFCC0`–`XMFCC12`, `YMFCC0`–`YMFCC12`, `ZMFCC0`–`ZMFCC12` | 39 | Coeficientes cepstrales que resumen contenido espectral |
| Similitud coseno | `XYCOS`, `XZCOS`, `YZCOS` | 3 | Similitud entre pares de ejes |
| Correlaciones | `XYCOR`, `XZCOR`, `YZCOR` | 3 | Asociación lineal entre pares de ejes |
| Resultante | `RESULTANT` | 1 | Resumen de la magnitud tridimensional del movimiento |
| **Total** | | **91** | |

**Nota de precisión:** los nombres permiten explicar la finalidad general de cada familia, pero no garantizan por sí solos la fórmula exacta implementada por WISDM. En particular, `PEAK`, `VAR` y `RESULTANT` deben contrastarse con la documentación o el código de extracción antes de atribuirles una fórmula específica.

### 5.1. Histogramas: `X0`–`X9`, `Y0`–`Y9`, `Z0`–`Z9`

Representan diez intervalos de distribución por eje. **No son diez muestras temporales consecutivas.** Ayudan a caracterizar qué valores de aceleración son frecuentes dentro de una ventana.

### 5.2. Medias y dispersión

Para las muestras de un eje en una ventana, $x_1,\ldots,x_N$, se pueden definir:

- Media: $\bar{x}=\frac{1}{N}\sum_{i=1}^N x_i$.
- Desviación absoluta media: $\frac{1}{N}\sum_{i=1}^N |x_i-\bar{x}|$.
- Desviación estándar poblacional: $\sqrt{\frac{1}{N}\sum_{i=1}^N(x_i-\bar{x})^2}$.

Estas expresiones ilustran los conceptos; la implementación del extractor determina los detalles numéricos.

### 5.3. Coeficientes MFCC

Los *Mel-Frequency Cepstral Coefficients* (MFCC) resumen información espectral. El archivo contiene **13 coeficientes por eje** (39 en total). En el taller los trataremos como características calculadas previamente, sin reproducir su algoritmo de extracción.

### 5.4. Relaciones entre ejes

Las columnas `XYCOS`, `XZCOS`, `YZCOS` resumen similitud coseno, mientras que `XYCOR`, `XZCOR`, `YZCOR` resumen correlaciones. Estas medidas ayudan a representar cómo se relacionan los movimientos en los distintos ejes.

### 5.5. Resultante

La magnitud instantánea de una aceleración tridimensional es:

$$
\|\mathbf{a}_i\|=\sqrt{x_i^2+y_i^2+z_i^2}
$$

`RESULTANT` resume información relacionada con la magnitud conjunta; la fórmula precisa depende del extractor utilizado.

## 6. Lectura directa del ARFF desde GitHub

La dirección `github.com/.../tree/...` muestra una carpeta en el navegador. Para descargar un archivo mediante Python se utiliza una dirección **raw**.

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
    url = base + archivo

    with urlopen(url) as respuesta:
        texto = respuesta.read().decode("utf-8-sig")

    datos, metadata = arff.loadarff(StringIO(texto))
    df = pd.DataFrame(datos)

    # En este ARFF algunos nombres incluyen comillas literales.
    df.columns = df.columns.str.strip('"')

    # Decodificar categorías que SciPy devuelve como bytes.
    for columna in df.select_dtypes(include=["object"]).columns:
        df[columna] = df[columna].map(
            lambda v: v.decode("utf-8") if isinstance(v, bytes) else v
        )

    return df


SUJETO = 1603
df = leer_wisdm(SUJETO, "accel", "phone")

print("Dimensiones:", df.shape)
print("Columnas:", df.columns.tolist())
display(df.head())
```

**Nota técnica:** `arff.loadarff()` necesita recibir el contenido como texto en este procedimiento. Por ello usamos `StringIO` después de decodificar los bytes, **no `BytesIO`**.

Si la descarga produce `HTTP 404`, hay que comprobar el nombre y la ubicación exactos del archivo en el repositorio. Si SciPy informa de un atributo ARFF no soportado, será necesario utilizar un lector compatible con ese tipo de atributo.

## 7. Exploración inicial

```python
print("Sujeto(s):", df["class"].unique())
print("Actividades:", df["ACTIVITY"].unique())
print("Número de filas:", len(df))
print("Número de columnas:", len(df.columns))

display(df["ACTIVITY"].value_counts().sort_index())
display(df.isna().sum().sort_values(ascending=False).head(10))
```

Conviene verificar que `class` identifique al sujeto esperado:

```python
print(df["class"].value_counts(dropna=False))
```

Si existen valores faltantes o clases con muy pocos ejemplos, deben considerarse antes de entrenar el clasificador.

## 8. Preparación de `X` e `y`

El problema del taller es **predecir la actividad**:

```python
# Variable objetivo: actividad realizada
# No usar ni ACTIVITY ni class como variables predictoras.
X = df.drop(columns=["ACTIVITY", "class"])
y = df["ACTIVITY"]

print("Matriz de características:", X.shape)
print("Etiquetas:", y.shape)
print("Número de características:", X.shape[1])

assert X.shape[1] == 91, "Revisar columnas del archivo ARFF"
```

El algoritmo recibe una fila de 91 características y debe estimar una de las actividades presentes en el archivo.

## 9. Modelo de referencia: Random Forest

El siguiente ejemplo muestra un **modelo inicial de demostración**. La partición aleatoria es útil para verificar que el código funciona, pero **no basta para asegurar independencia temporal** entre entrenamiento y prueba.

```python
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, f1_score, classification_report

X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.25,
    random_state=42,
    stratify=y
)

modelo = RandomForestClassifier(
    n_estimators=200,
    random_state=42,
    n_jobs=-1
)
modelo.fit(X_train, y_train)

pred = modelo.predict(X_test)

print("Accuracy:", accuracy_score(y_test, pred))
print("F1-macro:", f1_score(y_test, pred, average="macro"))
print(classification_report(y_test, pred, zero_division=0))
```

El ejemplo requiere que cada clase tenga suficientes filas para la partición estratificada. Si no se cumple, es necesario revisar la distribución de actividades.

### Precaución sobre la evaluación

Los ARFF procesados no necesariamente conservan marcas temporales o identificadores de ventana que permitan reconstruir sesiones y separar bloques temporales de manera fiable. Por ello:

- Una división aleatoria de filas **no demuestra generalización a nuevas sesiones**.
- Aunque no existan ventanas solapadas, pueden persistir dependencias temporales entre ejemplos cercanos.
- Comparar sujetos distintos entrenando y evaluando un modelo separado para cada uno produce una **evaluación intra-sujeto**, no una prueba de generalización a personas desconocidas.
- Para una evaluación rigurosa por sesión, tiempo o sujeto se necesita información de agrupación suficiente, o recurrir a los datos originales.

## 10. Ejercicio en grupos

Cada grupo de tres participantes utilizará un sujeto distinto y repetirá el mismo procedimiento:

1. Cambiar `SUJETO` por el identificador asignado.
2. Cargar `data_<sujeto>_accel_phone.arff`.
3. Inspeccionar el número de ventanas y la distribución de `ACTIVITY`.
4. Separar `X` (91 características) e `y` (`ACTIVITY`), excluyendo `class`.
5. Entrenar el mismo Random Forest y evaluar accuracy y F1-macro.
6. Identificar actividades que se confunden con mayor frecuencia.
7. Comparar resultados y discutir posibles causas de las diferencias.

**Mantener constantes** las características, el modelo, los hiperparámetros y el procedimiento de partición para facilitar la comparación entre grupos.

### Tabla para socializar resultados

| Grupo | Sujeto | Ventanas | Actividades | Accuracy | F1-macro |
|---|---|---:|---:|---:|---:|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |

## 11. Preguntas de reflexión

1. ¿Por qué una fila del ARFF no equivale a una muestra instantánea de aceleración?
2. ¿Qué información capturan los histogramas que no aparece en las medias?
3. ¿Para qué pueden servir las características espectrales MFCC?
4. ¿Por qué `ACTIVITY` es la variable objetivo y `class` no lo es?
5. ¿Qué ocurriría si incluyéramos `ACTIVITY` entre los predictores?
6. ¿Por qué pueden cambiar los resultados al seleccionar otro sujeto?
7. ¿Qué limitaciones tiene una evaluación basada en una partición aleatoria de las ventanas?

## 12. Referencias y atribución

- Weiss, G. M. (2019). *WISDM Smartphone and Smartwatch Activity and Biometrics Dataset*. UCI Machine Learning Repository. https://doi.org/10.24432/C5HK59
- [Página oficial del dataset en UCI](https://archive.ics.uci.edu/dataset/507/wisdm+smartphone+and+smartwatch+activity+and+biometrics+dataset)
- [Documentación de `scipy.io.arff`](https://docs.scipy.org/doc/scipy/reference/io.arff.html)
- [Documentación de Random Forest en scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html)

El dataset se distribuye bajo **CC BY 4.0**; al reutilizarlo deben mantenerse la atribución, el enlace a la licencia y la indicación de modificaciones, cuando corresponda.

---

[← 02. Acelerómetros y giroscopios](02_acelerometro_giroscopio.md) · [Volver al README](../README.md) · [04. Clasificación de actividades →](04_clasificacion_actividades.md)
