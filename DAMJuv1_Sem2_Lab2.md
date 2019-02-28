---
id: rysi2019-s2-l02
summary: En este laboratorio 
categories: android
status: draft
feedback_url: https://github.com/htapiagroup/htapiagroup.github.io/issues
tags: sl4a, python, adb, ports, forward
difficulty: 3
published: 2019-02-21
author: HTM <htapia@lania.edu.mx>

---

# Aplicaciones con los sensores embebidos

## Introduccion
Duration: 1:00

Los dispositivos modernos contienen arreglos de sensores embebidos. Una de las funcionalidades de acceder programaticamente al dispositivo usando Python es que podemos interactuar directamente con los sensores. En esta libreta vamos a aprender como determinar que sensores se tienen en el dispositivo y como tomar lecturas de ellos. Posterioremente usaremos estas lecturas para algunas aplicaciones.


## Lectura de sensores
Duration: 7:00
positive
: **Nota:** Siempre es buena idea revisar que la comunicacion con el dispositivo sige activa al comienzo de una nueva libreta. Aun cuando la conexion a traves de `tcp` deberia seguir activa mientras el servicio siga vigente en el dispositivo, no es necesario volver a establecerla. Sin embargo, en cada nueva libreta **sí** se tiene que importar el modulo `android`.

Vamos a seguir la recomendacion de la nota anterior y verificar que el dispositivo esta conectado a la computadora. 


```python
!adb forward tcp:9999 tcp:2222
```


```python
import android
droide=android.Android()
droide.makeToast("Hola desde Jupyter!")
```

Usaremos dos de las funciones para capturar eventos que construimos en el laboratorio anterior. La primera de ellas `event_loop` es una funcion auxiliar  para captar eventos vaciando el buffer de eventos y capturando el evento mas reciente. Hemos modificado la version presentada antes para que regrese los valores resultantes de la ejecucion.


```python
import time

def event_loop():
  for i in range(10):
    time.sleep(1)
    droide.eventClearBuffer()
    time.sleep(1)
    e = droide.eventPoll(1)
    if e.result is not None:
      return e.result
  return False
```

La segunda funcion retorna lectura de los sensores de aceleracion con una frecuencia de una lectura por segundo. Recordemos que de acuerdo [a la documentacion](http://www.mithril.com.au/android/doc/SensorManagerFacade.html), el primer valor dentro del llamado a `startSensingTimed` corresponde al sensor y el valor 2 representa el acelerometro del dispositivo.


```python
def prueba_sensor():
  # 2: acelerometro; 1000 milisegundos entre lectura
  droide.startSensingTimed(2, 1000)
  try:
    return event_loop()
  finally:
    droide.stopSensing()

prueba_sensor()
```

El programa anterior es util cuando necesitamos abrir una sola conexion con el sensor para obtener una lectura de su estado. Este modelo forza la utilizacion de un controlador central. Antes de continuar vamos a determinar que sensores tenemos disponibles en nuestro dispositivo.

positive
: Cada modelo de dispositivo movil contienen arreglos de sensores diferentes. Tradicionalmente los dispositivos Android tienen un acelerometro, un giroscopo y un sensor de campo magnetico, y por esto existe una funcion en la API de Python especifica para estos sensores.



```python
def prueba_acelerometro():
    # 2: acelerometro; 1000 milisegundos entre lectura
    droide.makeToast("Prueba de acelerometro...")
    droide.startSensingTimed(2, 1000)
    try:
        return droide.sensorsReadAccelerometer().result
    finally:
        droide.stopSensing()

prueba_acelerometro()
```

<i></i> | <i></i>
--------| ----
positive
:El resultado de la ejecucion anterior es una lista con tres valores que corresponden a las lecturas de la aceleracion en cada direccion del espacio tridimensional usando un sistema de coordenadas situadas en el dispositivo como se muestra en el esquema. | <img src="https://www.researchgate.net/profile/Oliver_Keller3/publication/309762259/figure/fig21/AS:426169960013828@1478618244213/Coordinate-system-for-multi-axis-accelerometer-and-gyroscope-sensor-data-of-an-iOS.png" alt="CoordSys" style="width: 350px;"/> 

Vamos a modificar las funciones de la API para leer los datos del sensor de campo magnetico (magnetometro)


```python
def prueba_magnetometro():
    # 3: magnetometro; 1000 milisegundos entre lectura
    droide.makeToast("Prueba de magnetometro...")
    droide.startSensingTimed(3, 1000)
    try:
        return droide.sensorsReadMagnetometer().result
    finally:
        droide.stopSensing()

prueba_magnetometro()
```

y del sensor de orientacion (giroscopo):


```python
def prueba_orientacion():
    # 1: todos los sensores; 1000 milisegundos entre lectura
    droide.makeToast("Prueba de orientacion...")
    droide.startSensingTimed(1, 1000)
    try:
        return droide.sensorsReadOrientation().result
    finally:
        droide.stopSensing()

prueba_orientacion()
```

En todos los casos obtnemos una lista con los tres valores correspondientes a cada una de las direcciones de acuerdo al diagrama anterior. La interpretacion fisica de cada lectura se deja para mas adelante y es importante saber como interpretar las lecturas ya que los valores dan informacion sobre el contexto del dispositivo: movimiento (aceleracion), orientacion (giroscopo) y posicion respecto al norte magnetico de la Tierra (magnetometro).

Algunos dispositivos incluyen mas sensores, tipicamente todos tienen ademas de los tres ya indicados un sensor de luminosidad. La API proporciona una forma de saber que otros sensores se tienen y veremo ademas la ventaja de usar nuestra funcion `event_poll`.

Evaluen la siguiente funcion una vez, registren el numero que resulta y despues  cubran la pantalla de su dispositivo y vuelvan a evaluar. Que ocurre?


```python
def prueba_iluminacion():
    # 4: sensor luminosidad; 1000 milisegundos entre lectura
    droide.makeToast("Prueba de luminosidad...")
    droide.startSensingTimed(4, 1000)
    try:
        return droide.sensorsGetLight().result
    finally:
        droide.stopSensing()

prueba_iluminacion()
```

Evaluen nuevamente la funcion pero con la pantalla cubierta...


```python
prueba_iluminacion()
```

## Lectura de sensores usando `readSensors`

La API incluye una funcion nombrada `readSensors` que regresa los valores mas recientes que se han registrado por los sensores. Esta funcion se invoca primero indicando que los sensores deben estar "atentos" a censar:


```python
droide.startSensingTimed(1, 1000)
```

el llamado de la funcion es como sigue:


```python
lecturas = droide.readSensors().result
```

y finalmente no debemos olvidar indicar al dispositivo que deje de tomar lecturas de los sensores, de otro modo puede interferir con otras actividades:


```python
droide.stopSensing()
```

## Usando controlador central via `event_loop`

La funcion `event_loop` que construimos previamente nos permite acceder a las lecturas de los sensores disponibles pero ya controla los pasos anteriores:


```python
def prueba_sensores():
  # 1: todos los sensores; 1000 milisegundos entre lectura
  droide.startSensingTimed(1, 1000)
  try:
    return event_loop()
  finally:
    droide.stopSensing()

lecturas2 = prueba_sensores()
```

Comparemos los sensores disponibles entre ambos metodos:


```python
print(lecturas.keys())
```


```python
print(lecturas2[0]['data'].keys())
```

positive
: **Nota:** Como mencionamos, cada dispositivo contiene un arreglo diferente de sensores, asi que los resultados que observen dependeran de su modelo. En el fondo del alma la funcion `readSensors` utiliza `eventPoll` para tomar las lecturas, sin embargo `eventPoll` permite un control mas fino de los eventos que se estan registrando.

## Visualizando las lecturas del acelerometro

Vamos a realizar un programa que registre los datos del acelerometro. Este sera util para conocer el estado de movimiento del dispositivo (y por lo tanto del usuario).

La estructura del programa es como sigue: primero definimos el tiempo entre lecturas del sensor y lo nombramos `dt`.


```python
dt = 100 # 100ms entre lecturas
```

positive
: Mientras mas pequeno es el intervalo entre lecturas, es mas probable que puedan detectarse eventos importantes o de interes. El tiempo entre lecturas lo deben determinar de acuerdo a lo que quieren detectar. Pero recuerden que mientras mas corto este tiempo, mas gasto de bateria pues el sensor esta tomando lecturas con mas frecuencia.

Ahora vamos a definir el tiempo total que el dispositivo estara registrando valores, en este caso por segundos:


```python
tfin = 3000 # toma lecturas por 3 segundos
```

El dispositivo va a tomar lecturas del estado del sensor en el siguiente segmento de codigo, 


```python
tiempo = 0

droide.startSensingTimed(2, dt)

while tiempo <= tfin:
    print droide.sensorsReadAccelerometer().result
    time.sleep(dt/1000.0)
    tiempo += dt
droide.stopSensing()
```

## Visualizando los valores

Como tenemos acceso a las lecturas, podemos visualizar los tres valores de las aceleraciones captadas por el sensor en el sistema local. Para eso vamos a usar una libreria especializada para hacer graficass y que deben tener instalada. Primero modificamos el programa como sigue:


```python
acel = []
tiempo = 0

droide.startSensingTimed(2, dt)

while tiempo <= tfin:
    acel.append(droide.sensorsReadAccelerometer().result)
    tiempo += dt
droide.stopSensing()
```

Donde hemos definido la lista `acel` inicialmente vacia, y a la que iremos agregando los valores del sensor dentro del `while`. Ahora evaluen lo siguiente


```python
import pandas as pd
%matplotlib inline
```

La libreria `pandas` contiene metodos y clases para un tipo de estructuras de datos llamadas `DataFrames` que permiten la manipulacion y el analisis de datos de manera eficiente. La segunda linea nos permite visualizar las graficas en la libreta. Ahora evaluen lo que sigue


```python
a = pd.DataFrame(acel, columns=["ax","ay","az"])
```

La estructura `a` contiene los datos del sensor obtenidos de la lista `acel` en un formato similar a las tablas de columnas y renglones tradicionalmente usadas en software de oficina. Pero ademas contiene metodos heradados que permiten la manipulacion eficiente de los datos. Por ejemplo podemos graficarlos evaluando


```python
a.plot()
```

## Entrega

Van a modificar los programas necesarios para graficar los valores de orientacion, campo magnetico y luminosidad de sus dispositivos.

La libreta de entrega de este laboratorio debe mostrar los programas y las tres graficas correspondientes a los sensores.


Survey
: La funcion `sensorsReadAccelerometer` regresa
 - Un diccionario larousse
 - Una matrix aleatoria
 - Una lista de tres numeros
 - Nada
: Mi dispositivo cuenta con al menos los siguientes sensores 
 - Luminosidad, Aceleracion, Campo magnetico y orientacion
 - Luminosidad y orientacion
 - Luminosidad, Aceleracion, Campo magnetico, Orientacion, Presion barometrica y bioxido de carbono
 - No tiene sensores