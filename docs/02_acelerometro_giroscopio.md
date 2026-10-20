# 02. Acelerómetros y giroscopios

**Taller: IA para sensores**  
**IBERSENSOR 2026 — Universidad del Norte**

> **Propósito:** comprender qué miden los acelerómetros y giroscopios, cómo se representan sus señales y qué información proporcionan para el reconocimiento de actividades humanas (*Human Activity Recognition — HAR*).

## 1. Introducción

Los smartphones y smartwatches incorporan sensores capaces de medir el movimiento de los dispositivos.

Dos de los sensores más importantes son:

- **Acelerómetro:** mide la aceleración específica sobre uno o varios ejes.
- **Giroscopio:** mide la velocidad angular alrededor de uno o varios ejes.

La combinación de sus mediciones permite caracterizar movimientos complejos y proporciona información útil para identificar actividades humanas.

En el taller utilizaremos datos de ambos sensores provenientes del dataset WISDM.

## 2. Sistemas de coordenadas

Los acelerómetros y giroscopios triaxiales realizan mediciones respecto a tres ejes, generalmente denominados X, Y y Z.

Estos ejes suelen estar definidos respecto al dispositivo y no necesariamente respecto a un sistema de coordenadas fijo en el espacio.

En consecuencia, las lecturas pueden cambiar cuando el dispositivo modifica su orientación, incluso si la actividad física realizada es la misma.

### 2.1. Representación vectorial

Una medición triaxial puede representarse mediante un vector:

$$
\mathbf{v} =
\begin{bmatrix}
v_x\\
v_y\\
v_z
\end{bmatrix}
$$

La magnitud del vector se calcula como:

$$
\|\mathbf{v}\| =
\sqrt{v_x^2+v_y^2+v_z^2}
$$

La magnitud permite resumir la intensidad de una medición triaxial mediante un único valor.

Aunque reduce la dependencia de la orientación del dispositivo, también elimina información direccional.

## 3. Acelerómetro

Un acelerómetro mide la **aceleración específica**, es decir, la aceleración no gravitacional expresada en el sistema de coordenadas del sensor.

En un acelerómetro convencional, la lectura incluye el efecto de la gravedad.

### 3.1. Unidades

Las mediciones pueden expresarse en:

- Metros por segundo al cuadrado: m/s².
- Unidades de gravedad: g.

La equivalencia aproximada es:

$$
1g = 9.80665\ \text{m/s}^2
$$

Es importante distinguir la unidad g de la variable que representa la aceleración gravitacional.

### 3.2. Mediciones triaxiales

Un acelerómetro proporciona tres componentes:

$$
\mathbf{a} =
\begin{bmatrix}
a_x\\
a_y\\
a_z
\end{bmatrix}
$$

Cada componente representa la aceleración específica medida sobre un eje del sensor.

Por ejemplo, una lectura hipotética en m/s² podría ser:

$$
\mathbf{a} =
\begin{bmatrix}
0.5\\
1.2\\
9.7
\end{bmatrix}
$$

La magnitud correspondiente es:

$$
\|\mathbf{a}\| =
\sqrt{0.5^2+1.2^2+9.7^2}
$$

$$
\|\mathbf{a}\| \approx 9.79\ \text{m/s}^2
$$

Este valor es cercano a la aceleración gravitacional terrestre.

### 3.3. Acelerómetro en reposo

Cuando un acelerómetro permanece inmóvil respecto a la superficie terrestre, su magnitud suele aproximarse a:

$$
\|\mathbf{a}\| \approx 9.81\ \text{m/s}^2
$$

La distribución de las componentes entre los ejes depende de la orientación del dispositivo.

Por ejemplo, una lectura idealizada podría ser:

$$
\mathbf{a} =
\begin{bmatrix}
0\\
0\\
9.81
\end{bmatrix}
$$

Si el dispositivo cambia de orientación, los valores de los ejes también cambian, aunque permanezca inmóvil.

**Observación:** el signo de cada componente depende de la convención de ejes del dispositivo.

### 3.4. Aceleración estática y dinámica

La señal de un acelerómetro puede analizarse considerando dos contribuciones:

- **Componente estática:** asociada principalmente a la gravedad.
- **Componente dinámica:** asociada al movimiento del dispositivo.

Una aproximación común consiste en estimar la componente gravitacional mediante un filtro pasa-bajas y restarla de la señal original.

Esta separación es aproximada y depende de las características del movimiento y del filtro utilizado.

## 4. Giroscopio

Un giroscopio mide la **velocidad angular**, es decir, la rapidez con la que cambia la orientación de un dispositivo.

A diferencia del acelerómetro, el giroscopio no mide directamente la aceleración lineal.

### 4.1. Unidades

Las mediciones de velocidad angular pueden expresarse en:

- Radianes por segundo: rad/s.
- Grados por segundo: °/s.

La conversión se realiza mediante:

$$
\omega_{\text{rad/s}} =
\omega_{\text{°/s}}\frac{\pi}{180}
$$

Por ejemplo:

$$
90\ \text{°/s} = \frac{\pi}{2}\ \text{rad/s}
$$

### 4.2. Mediciones triaxiales

Un giroscopio proporciona tres componentes:

$$
\boldsymbol{\omega} =
\begin{bmatrix}
\omega_x\\
\omega_y\\
\omega_z
\end{bmatrix}
$$

Estas componentes representan las velocidades angulares alrededor de los ejes del dispositivo.

La magnitud de la velocidad angular es:

$$
\|\boldsymbol{\omega}\| =
\sqrt{\omega_x^2+\omega_y^2+\omega_z^2}
$$

Por ejemplo, para una lectura hipotética:

$$
\boldsymbol{\omega} =
\begin{bmatrix}
0.1\\
0.2\\
1.0
\end{bmatrix}
\ \text{rad/s}
$$

La magnitud es:

$$
\|\boldsymbol{\omega}\| =
\sqrt{0.1^2+0.2^2+1.0^2}
\approx 1.025\ \text{rad/s}
$$

### 4.3. Giroscopio en reposo

Si un dispositivo no está rotando, las mediciones ideales del giroscopio son:

$$
\omega_x=\omega_y=\omega_z=0
$$

En la práctica, pueden observarse pequeñas desviaciones debido al ruido, al sesgo (*bias*) y a otras imperfecciones del sensor.

### 4.4. Velocidad angular y orientación

En una rotación alrededor de un eje fijo, la velocidad angular puede relacionarse con el cambio de ángulo:

$$
\omega = \frac{d\theta}{dt}
$$

Si la velocidad angular es constante:

$$
\Delta\theta = \omega\Delta t
$$

Por ejemplo, una velocidad angular de 1 rad/s mantenida durante 2 segundos produce un cambio angular de 2 radianes alrededor del eje considerado.

En movimientos tridimensionales generales, la orientación requiere una representación y un procedimiento de integración más elaborados.

## 5. Comparación entre acelerómetro y giroscopio

| Característica | Acelerómetro | Giroscopio |
|---|---|---|
| Magnitud | Aceleración específica | Velocidad angular |
| Unidades habituales | m/s² o g | rad/s o °/s |
| Componentes | X, Y, Z | X, Y, Z |
| Movimiento lineal | Proporciona información relevante | No lo mide directamente |
| Rotación | Puede reflejar cambios de orientación respecto a la gravedad | Mide directamente la velocidad angular |
| Lectura ideal en reposo terrestre | Magnitud cercana a 1 g | Cercana a cero |
| Aplicación HAR | Patrones de movimiento | Patrones de rotación |

**¿Por qué utilizar ambos sensores?**

Porque proporcionan información complementaria.

Por ejemplo, durante una actividad física, el acelerómetro puede registrar variaciones periódicas de aceleración, mientras que el giroscopio puede registrar oscilaciones y rotaciones del dispositivo.

La combinación de ambas señales puede facilitar la identificación de actividades.

## 6. Señales temporales

Los sensores producen secuencias de mediciones.

Una muestra del acelerómetro puede representarse como:

$$
\mathbf{a}[n] =
[a_x[n],a_y[n],a_z[n]]
$$

De manera similar, una muestra del giroscopio es:

$$
\boldsymbol{\omega}[n] =
[\omega_x[n],\omega_y[n],\omega_z[n]]
$$

Si se utilizan ambos sensores triaxiales, cada instante de medición sincronizado puede describirse mediante seis valores:

$$
\mathbf{x}[n] =
[a_x,a_y,a_z,\omega_x,\omega_y,\omega_z]
$$

Las mediciones deben estar alineadas temporalmente para que esta representación sea válida.

### 6.1. Frecuencia de muestreo

La frecuencia de muestreo determina cuántas mediciones se registran por segundo.

$$
f_s = \frac{1}{T_s}
$$

Por ejemplo, con una frecuencia de 20 Hz se obtienen nominalmente 20 muestras por segundo.

En 10 segundos:

$$
N = 20\times10 = 200\ \text{muestras}
$$

En el taller verificaremos las marcas de tiempo y las frecuencias de muestreo correspondientes a los archivos utilizados.

## 7. Interpretación de las señales

Las diferentes actividades humanas producen patrones de movimiento que pueden reflejarse en las señales.

### 7.1. Persona sentada

En condiciones relativamente estables:

- El acelerómetro registra principalmente la componente asociada a la gravedad.
- El giroscopio registra velocidades angulares pequeñas.
- Las variaciones temporales suelen ser reducidas.

### 7.2. Persona caminando

Al caminar:

- El acelerómetro puede mostrar oscilaciones periódicas.
- El giroscopio puede registrar cambios de orientación.
- La amplitud y periodicidad dependen de la persona y de la ubicación del dispositivo.

### 7.3. Persona corriendo

Al correr:

- Pueden observarse variaciones de aceleración más intensas.
- La frecuencia de las oscilaciones puede aumentar.
- Los patrones dependen de la velocidad, el estilo de movimiento y la ubicación del sensor.

Estas descripciones son tendencias generales y no reglas universales de clasificación.

## 8. Características estadísticas de las señales

Los algoritmos de aprendizaje automático pueden trabajar con características extraídas de segmentos temporales.

Una técnica habitual consiste en dividir las señales en ventanas de duración fija.

Para una ventana de \(N\) muestras, algunas características son:

### 8.1. Media

$$
\mu = \frac{1}{N}\sum_{i=1}^{N}x_i
$$

La media representa el valor promedio de la señal.

### 8.2. Desviación estándar

$$
\sigma =
\sqrt{\frac{1}{N}\sum_{i=1}^{N}(x_i-\mu)^2}
$$

La desviación estándar cuantifica la variabilidad de las mediciones dentro de la ventana.

### 8.3. Valor mínimo y máximo

$$
x_{\min}=\min(x_1,\ldots,x_N)
$$

$$
x_{\max}=\max(x_1,\ldots,x_N)
$$

Permiten identificar los extremos de las mediciones.

### 8.4. Energía media cuadrática

$$
E = \frac{1}{N}\sum_{i=1}^{N}x_i^2
$$

Esta característica resume la intensidad cuadrática de la señal.

### 8.5. Magnitud vectorial

Para el acelerómetro:

$$
a_{\text{mag}}[n] =
\sqrt{a_x[n]^2+a_y[n]^2+a_z[n]^2}
$$

Para el giroscopio:

$$
\omega_{\text{mag}}[n] =
\sqrt{\omega_x[n]^2+\omega_y[n]^2+\omega_z[n]^2}
$$

Las magnitudes pueden utilizarse como señales adicionales para extraer características.

## 9. Relación con el dataset WISDM

El dataset **WISDM Smartphone and Smartwatch Activity and Biometrics Dataset** contiene registros de sensores de movimiento obtenidos durante diferentes actividades humanas.

En el taller trabajaremos con datos de acelerómetro y giroscopio de sujetos seleccionados.

Las señales se utilizarán para:

1. Identificar los ejes de medición.
2. Visualizar las variaciones temporales.
3. Comparar señales correspondientes a distintas actividades.
4. Construir ventanas temporales.
5. Extraer características estadísticas.
6. Entrenar modelos de clasificación.

**Importante:** las unidades, la frecuencia de muestreo, la organización de archivos y la disponibilidad de registros se verificarán en la documentación y en los datos originales antes de realizar los cálculos.

## 10. Aspectos que deben considerarse al analizar datos reales

### Ruido

Las señales pueden contener variaciones producidas por las características del sensor y del entorno.

### Orientación

Los ejes del sensor están asociados al dispositivo. Por tanto, su orientación influye en los valores registrados.

### Ubicación

Un sensor ubicado en la muñeca puede registrar movimientos diferentes de uno ubicado en un bolsillo.

### Variabilidad entre personas

La intensidad, frecuencia y forma de los movimientos pueden cambiar entre individuos.

### Sincronización

Cuando se combinan señales de varios sensores, es necesario verificar la correspondencia temporal entre las mediciones.

### Segmentación

La duración y el solapamiento de las ventanas temporales pueden afectar las características extraídas y el desempeño del clasificador.

## 11. Preguntas de reflexión

1. ¿Por qué un acelerómetro puede registrar valores diferentes de cero cuando está inmóvil?
2. ¿Cuál es la diferencia entre aceleración lineal y velocidad angular?
3. ¿Qué información se pierde al calcular la magnitud de un vector triaxial?
4. ¿Por qué un acelerómetro y un giroscopio pueden proporcionar información complementaria?
5. ¿Cómo puede afectar la orientación del dispositivo al reconocimiento de actividades?
6. ¿Por qué es importante utilizar ventanas temporales para analizar señales de movimiento?
7. ¿Qué diferencias esperarías observar entre las señales de una persona caminando y corriendo?

## 12. Referencias

1. [Bosch Sensortec — Motion Sensors](https://www.bosch-sensortec.com/en/products/motion-sensors/)
2. [STMicroelectronics — MEMS and Sensors](https://www.st.com/en/mems-and-sensors.html)
3. [Analog Devices — Accelerometers](https://www.analog.com/en/product-category/accelerometers.html)
4. [UCI Machine Learning Repository — WISDM Dataset](https://archive.ics.uci.edu/dataset/507/wisdm+smartphone+and+smartwatch+activity+and+biometrics+dataset)
5. [SciPy — Signal Processing](https://docs.scipy.org/doc/scipy/reference/signal.html)

---

**Taller: IA para sensores — IBERSENSOR 2026**  
Universidad del Norte, Barranquilla, Colombia  
21 de octubre de 2026

[← Introducción a Smart Sensors](01_smart_sensors.md) | [Volver al inicio del taller](../README.md)
