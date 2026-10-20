# 01. Introducción a Smart Sensors

**Taller: IA para sensores**  
**IBERSENSOR 2026 — Universidad del Norte**

> **Propósito:** presentar los conceptos fundamentales de los sensores inteligentes y su relación con el procesamiento de datos y la inteligencia artificial. Este documento es material de referencia para los ejercicios prácticos del taller.

## 1. ¿Qué es un sensor?

Un **sensor** es un dispositivo capaz de detectar una magnitud física, química o ambiental y transformarla en una señal que pueda ser medida, procesada o interpretada por un sistema electrónico.

Algunas magnitudes que pueden medirse mediante sensores son:

| Magnitud | Sensor | Aplicación |
|---|---|---|
| Temperatura | Termistor | Monitoreo ambiental |
| Presión | Sensor piezorresistivo | Control industrial |
| Aceleración | Acelerómetro | Detección de movimiento |
| Velocidad angular | Giroscopio | Seguimiento de rotaciones |
| Iluminación | Fotodiodo | Control automático de iluminación |
| Campo magnético | Magnetómetro | Orientación y navegación |

Un sensor convencional puede entregar una señal analógica o digital que representa la magnitud medida.

Sin embargo, la interpretación de esa información generalmente requiere procesamiento adicional.

## 2. ¿Qué es un Smart Sensor?

Un **Smart Sensor** o sensor inteligente integra capacidades adicionales a la medición, como procesamiento, calibración, almacenamiento, diagnóstico o comunicación digital.

En términos generales:

**Sensor convencional:** mide una magnitud y proporciona una señal.

**Smart Sensor:** mide una magnitud e incorpora funcionalidades que facilitan el procesamiento, la interpretación o la integración de sus datos.

Un sensor inteligente puede incorporar:

- Elemento sensor.
- Acondicionamiento de señales.
- Conversión analógica-digital (ADC), cuando es necesaria.
- Microcontrolador o procesador digital.
- Memoria y parámetros de calibración.
- Interfaces de comunicación.
- Algoritmos de procesamiento.

No todos los Smart Sensors incorporan todas estas funciones. Su arquitectura depende de la aplicación.

### 2.1. Diferencias principales

| Característica | Sensor convencional | Smart Sensor |
|---|---|---|
| Medición física | Sí | Sí |
| Procesamiento integrado | Limitado o ausente | Puede incluirlo |
| Calibración automática | No necesariamente | Puede incorporarla |
| Comunicación digital | Opcional | Habitual |
| Diagnóstico interno | Limitado | Puede incorporarlo |
| Interpretación local de datos | Generalmente externa | Posible |

**Idea fundamental:** la inteligencia de un sensor no depende exclusivamente de utilizar inteligencia artificial. También puede provenir de mecanismos de calibración, procesamiento digital, autodiagnóstico y comunicación.

## 3. Arquitectura general de un Smart Sensor

Una arquitectura representativa comprende las siguientes etapas:

```text
     Magnitud física
           |
           v
   +------------------+
   | Elemento sensor  |
   +------------------+
           |
           v
   +------------------+
   | Acondicionamiento|
   | de señal         |
   +------------------+
           |
           v
   +------------------+
   | Conversión ADC   |
   | (si se requiere) |
   +------------------+
           |
           v
   +------------------+
   | Procesamiento    |
   | digital          |
   +------------------+
           |
           v
   +------------------+
   | Comunicación     |
   | / Interfaz       |
   +------------------+
           |
           v
     Aplicación
```

### Función de los componentes

**Elemento sensor:** transforma una magnitud física en una señal medible.

**Acondicionamiento:** puede amplificar, filtrar o adaptar la señal.

**Conversión ADC:** transforma una señal analógica en valores digitales.

**Procesamiento:** realiza operaciones como filtrado, calibración, compensación o extracción de información.

**Comunicación:** permite transferir datos a otros dispositivos o aplicaciones.

En dispositivos modernos, varios de estos componentes pueden estar integrados en un mismo circuito.

## 4. Adquisición de datos de sensores

Los sensores utilizados para medir movimientos producen secuencias de valores que cambian con el tiempo.

Una señal digital puede representarse mediante:

$$
x[n] = x(nT_s)
$$

Donde:

- $x[n]$: valor de la señal en la muestra $n$.
- $T_s$: período de muestreo.
- $n$: índice de la muestra.

La frecuencia de muestreo se define como:

$$
f_s = \frac{1}{T_s}
$$

Por ejemplo, si un sensor registra 20 muestras por segundo:

$$
f_s = 20\ \text{Hz}
$$

$$
T_s = 0.05\ \text{s}
$$

Esto significa que, idealmente, se obtiene una medición cada 50 milisegundos.

### 4.1. ¿Por qué es importante la frecuencia de muestreo?

La frecuencia de muestreo determina la resolución temporal con la que se registran los cambios de la señal.

Una frecuencia demasiado baja puede impedir la identificación de movimientos rápidos.

Una frecuencia más alta proporciona mayor detalle temporal, pero también incrementa la cantidad de datos y los recursos necesarios para procesarlos.

Para evitar aliasing, la frecuencia de muestreo debe ser superior al doble de la frecuencia máxima de interés de una señal adecuadamente limitada en banda.

## 5. Smart Sensors en dispositivos móviles

Los smartphones y smartwatches incorporan diversos sensores, entre ellos:

- Acelerómetros.
- Giroscopios.
- Magnetómetros.
- Sensores de proximidad.
- Sensores de iluminación.
- Sensores de presión atmosférica, según el dispositivo.

Muchos sensores de movimiento se fabrican utilizando tecnología **MEMS (Micro-Electro-Mechanical Systems)**.

Esta tecnología permite integrar estructuras mecánicas microscópicas y circuitos electrónicos en dispositivos compactos y de bajo consumo energético.

### 5.1. Acelerómetro

Un acelerómetro mide aceleración específica sobre uno o varios ejes. En aplicaciones móviles, sus lecturas incluyen normalmente el efecto de la gravedad.

Un acelerómetro triaxial proporciona:

$$
a_x,\quad a_y,\quad a_z
$$

Estas mediciones permiten analizar patrones de movimiento.

### 5.2. Giroscopio

Un giroscopio mide la velocidad angular alrededor de sus ejes:

$$
\omega_x,\quad \omega_y,\quad \omega_z
$$

Las mediciones del acelerómetro y el giroscopio proporcionan información complementaria sobre el movimiento de un dispositivo.

En el siguiente documento estudiaremos ambos sensores con mayor detalle.

## 6. Smart Sensors e inteligencia artificial

Los sensores generan datos, pero estos datos no siempre permiten identificar directamente lo que está ocurriendo.

Por ejemplo, un acelerómetro puede registrar una secuencia de aceleraciones, pero no necesariamente determinar por sí solo si una persona está caminando o corriendo.

Para interpretar estas señales podemos utilizar técnicas de **Machine Learning**.

Un sistema de reconocimiento de actividades humanas puede seguir el siguiente procedimiento:

```text
   Acelerómetro + Giroscopio
              |
              v
       Adquisición de datos
              |
              v
      Preprocesamiento
              |
              v
     Ventanas temporales
              |
              v
  Extracción de características
              |
              v
    Modelo de clasificación
              |
              v
       Actividad estimada
```

Por ejemplo:

- Caminar.
- Correr.
- Permanecer sentado.
- Subir escaleras.
- Bajar escaleras.

La clasificación se realiza a partir de patrones identificados en las señales.

**Importante:** el procesamiento mediante inteligencia artificial puede ejecutarse dentro del sensor, en el dispositivo que lo contiene o en un computador externo. Por tanto, utilizar IA para analizar datos de sensores no significa necesariamente que la IA esté integrada en el sensor.

## 7. Aplicaciones de los Smart Sensors

Los sensores inteligentes tienen aplicaciones en múltiples sectores:

| Sector | Ejemplos |
|---|---|
| Salud | Monitoreo de movimiento y actividad física |
| Industria | Mantenimiento predictivo y monitoreo de maquinaria |
| Transporte | Detección de movimientos y seguimiento de vehículos |
| Agricultura | Monitoreo de humedad y condiciones ambientales |
| Hogares inteligentes | Automatización y detección de presencia |
| Deportes | Análisis de movimientos y entrenamiento |

## 8. Limitaciones y desafíos

Los sistemas basados en sensores inteligentes presentan varios desafíos:

**Ruido:** las mediciones pueden contener perturbaciones que dificultan su interpretación.

**Calibración:** los sensores pueden presentar desviaciones y errores sistemáticos.

**Consumo energético:** la adquisición continua y el procesamiento requieren energía.

**Variabilidad:** distintas personas pueden ejecutar una misma actividad de maneras diferentes.

**Orientación del dispositivo:** los valores medidos dependen de la posición y orientación del sensor.

**Privacidad:** los datos de movimiento pueden revelar información sobre las actividades y rutinas de una persona.

Estas consideraciones son especialmente importantes cuando se diseñan aplicaciones de reconocimiento de actividades humanas.

## 9. Relación con el taller práctico

En el taller **IA para sensores** utilizaremos datos reales del conjunto WISDM.

Los participantes trabajarán con mediciones de acelerómetros y giroscopios para:

1. Explorar señales de movimiento.
2. Visualizar diferencias entre actividades.
3. Extraer características de las señales.
4. Entrenar un clasificador de actividades.
5. Evaluar los resultados.

Cada grupo utilizará los datos de una persona diferente.

El objetivo será comprender cómo las mediciones físicas pueden transformarse en información útil mediante técnicas de inteligencia artificial.

## 10. Preguntas de reflexión

1. ¿Qué diferencia existe entre un sensor convencional y un Smart Sensor?
2. ¿Por qué es importante la frecuencia de muestreo?
3. ¿Qué información adicional aporta un giroscopio respecto a un acelerómetro?
4. ¿Por qué dos personas pueden generar señales diferentes al realizar una misma actividad?
5. ¿Qué ventajas y limitaciones tendría ejecutar un clasificador directamente en un dispositivo móvil?

Estas preguntas sirven como referencia para interpretar los resultados de los ejercicios prácticos.

## 11. Referencias

1. [NIST — Smart Sensors and Standard-based Interoperability in Smart Grids](https://www.nist.gov/publications/smart-sensors-and-standard-based-interoperability-smart-grids)
2. [Bosch Sensortec — Motion Sensors](https://www.bosch-sensortec.com/en/products/motion-sensors/)
3. [Bosch Sensortec — MEMS Sensors](https://www.bosch-sensortec.com/en/about-us/our-company/mems)
4. [UCI Machine Learning Repository — WISDM Dataset](https://archive.ics.uci.edu/dataset/507/wisdm+smartphone+and+smartwatch+activity+and+biometrics+dataset)

---

**Taller: IA para sensores — IBERSENSOR 2026**  
Universidad del Norte, Barranquilla, Colombia  
21 de octubre de 2026

[Volver al inicio del taller](../README.md)
