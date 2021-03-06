---
id: rysi2019-s2-l01
summary: En este laboratorio vamos a conectar el dispositivo movil con el sistema local a traves del servicio de Python que se ejecuta en el dispotivo por medio de SL4A y explorar la API de Python en Android.
categories: android
status: draft
feedback_url: https://github.com/htapiagroup/htapiagroup.github.io/issues
tags: sl4a, python, adb, ports, forward
difficulty: 2
published: 2019-02-20
author: HTM <htapia@lania.edu.mx>
analytics account: UA-134300173-1

---

# Explorando la API de Python en Android

## Introduccion
Duration: 1:00

¡Llegó el momento de ponernos en acción! Ahora que cuentan con un sistema configurado con los elementos para programar en Python, vamos a usarlo para ejecutar acciones en el dispositivo móvil desde su sistema local, generalmente una laptop con el software de Python y las configuraciones necesarias realizadas la semana anterior.

### Que haremos
En el este laboratorio vamos a realizar un recorrido por la API de Python disponible a través de SL4A para interactuar con el dispositivo. 

### What you’ll learn
  - Como ejecutar comandos de Python en el dispositivo movil de manera remota.
  - Como acceder a las diferentes funciones de Android a traves de la API de Python.
  - Como acceder a las aplicaciones del sistema, incluyendo el portapapeles, intenciones, vibraciones, estado de red, etc.
  - Como interactuar con los componentes del sistema y el dispositivo para realizar lecturas de los diferentes sensores embebidos, señales, antenas, interfaces de usuario, etc.
  - Como crear una aplicacion simple basada en comandos de Python que integran funcionalidades del sistema operativo

Aunque esta no es la manera tradicional en que desarrollarían una aplicación móvil, al final del laboratorio verán la flexibilidad que proporciona un lenguaje interpretado para el prototipado de ideas, además de permitirles un control mucho más avanzado de las funciones de su dispositivo móvil.

### Que vamos a necesitar
  - Dispositivo [móvil con las configuraciones]() realizadas la semana anterior.
  - "[Computadora con las configuraciones]" realizadas la semana anterior.
  - Cable USB de datos
  - Conexión a internet

Esta laboratorio detalla los pasos para establecer conexion entre el dispositivo movil y su sistema local, permitiendoejecutar codigo en el dispositivo. A partir de esto, vamos a explorar la API de Python en Android de forma interactiva.

Survey
: Indica el nivel de dificultad que encontraste en la configuracion del sistema
 - Sencillo
 - Intermedio
 - Muy dificil
 - Imposible
: Indica tu nivel de experiencia con lenguajes de programacion interpretados
 - Novato
 - Intermedio
 - Avanzado
: Indica tu nivel de experiencia con Android
 - Novato
 - Intermedio
 - Avanzado
 - Soy desarrollador

-------------------------------------------------------------------------------------------------------------------


## Estableciendo contacto con el droide
Duration: 5:00

Revisemos primero los requerimentos para conectarse al dispositivo desde su sistema local. Cabe recordar que necesitaremos
* adb
* variable de entorno AP_PORT con el valor 9999 definida en el sistema
* el servicio de Python corriendo en el dispositivo Android
* el modulo `android.py` accesible al interprete


|Video: Iniciar y configurar servicio de Python en Android | |
|-----|-----|
| 1. Antes de comenzar, conectar el dispositivo movil a su computadora con un cable USB y asegurarse que el servicio de Python esta ejecutandose 
<br>
2. Abre una libreta de Jupyter en tu sistema local y en un celda de codigo ejecuta las instrucciones que siguen | ![check_config.gif](check_config.gif)  |


Revisar que el dispositivo esta conectado a la computadora

positive
: **Importante:** El signo de admiración que precede a los siguientes comandos NO es error del instructor. Si los ejecutas en una celda de Jupyter, deben incluir el símbolo ! para que el intérprete pueda pasar la instrucción al SO.

```python
!adb devices
```

Una vez iniciado el servicio de Python en el dispositivo, ejecuta lo siguiente


```python
!adb forward tcp:9999 tcp:2222
```

Ahora invocamos el modulo `android.py`


```python
import android
```

Creamos un objeto de la clase android que llamaremos droide


```python
droide = android.Android()
```

positive
: **Si obtienes un error al crear el droide**
La causa mas comun por la que no se logra crear el objeto de la clase `android` es porque el dispositivo no puede encontrar el puente de comunicacion a traves de `tcp`. Confirma que la variable AP_PORT esta definida, que el puerto del servicio de Python en tu dispositivo es el 2222 y que ejecutaste correctamente el comando `adb forward tcp:9999 tcp:2222` antes de volver a invocar el metodo. Si continuas encontrando errores puedes buscar en el foro si alguien ha tenido problemas similares y si no puedes abrir una nueva conversacion sobre el tema.

5. Si el comando anterior ejecuta sin ningun mensaje de error, evalua lo siguiente y observa tu dispositivo movil.


```python
droide.makeToast("Saludos RySI")
```


-------------------------------------------------------------------------------------------------------------------


## Ejecutando comandos en el dispositivo desde Jupyter
Duration: 5:00

Si lograron establecer la conexion entre el dispositivo y su sistema, estan listos para comenzar a desarrollar en Android con Python. A continuacion escriban el siguiente codigo:


```python
import android
droide=android.Android()
droide.makeToast("Hola desde Jupyter!")
```

positive
: **Observacion:** Noten como no es necesario volver a establecer el puente `tcp` entre los sistemas. En principio, esto debe realizarse una sola vez y la conexion es permanente hasta que por alguna razon se pierda el enlace. Esto ocurre en algunas ocasiones e iremos notandolo cuando suceda y plantear posibles soluciones.

Ahora ejecute el siguiente comando:


```python
droide.vibrate(2500)
```

Survey
: Responde, cual es el resultado del comando anterior:
 - El dispositivo se eleva 2500 mm desde su posicion de reposo
 - El dispositivo vibra durante 2.5 segundos
 - El dispositivo emite un sonido agudo durante 25 segundos
 - El dispositivo no realiza ninguna accion

## Estructura de los comando de Python ejecutados por Android
Duration: 5:00

Ejecuten los siguientes comandos, observen lo que hace su dispositivo y vean la salida que se obtiene por el interprete:


```python
droide.makeToast("RySi coloca texto aqui")
```


```python
droide.vibrate(2500)
```


```python
droide.notify("RySI", "Esta es una notificacion")
```


```python
droide.wifiGetConnectionInfo()
```

Todos los comandos ejecutados por el interprete de Python en Android regresan con la misma estructura: un objeto identificado por `Result` con tres diccionarios, `id`, `result` y `error`. Si observan los diferentes valores del ientificador `id` en las salidas de los comandos anteriores, pueden notar que van incrementando en una unidad a partir de `id=0`. El objeto `result` consiste en un diccionario (pares llaves/valores) que contiene la informacion que la funcion regresa. En caso que la funcion no regresa nada, este diccionario tiene el valor `None`. Finalmente, `error` indica si se detecto algun error durante la ejecucion. 

## Paréntesis (...
Duration: 10:00

positive
: **Nota:** Vamos a hacer un pequeño paréntesis para describir algunas de las funcionalidades del lenguaje Python. Este paso no es esencial para el resto del laboratorio pero las ideas mostradas pueden ser útiles en algún momento.

Podemos acceder a los valores que resulta del llamado a Android si asignamos una variable a la funcion. Ejecuten el siguiente codigo


```python
wifiConnectionInfo = droide.wifiGetConnectionInfo()
```

La informacion desplegada anteriormente, ahora esta contenida en la variable `wifiConnectionInfo` y podemos extraerla de la siguiente forma:


```python
print("El identificador del comando ejecutado es: %s"%wifiConnectionInfo.id)
print("El resultado del comando anterior es: %s"%wifiConnectionInfo.result)
print("El error al ejecutar el comando anterior es: %s"%wifiConnectionInfo.error)
```

Podemos acceder de manera iterativa a cada uno de los objetos regresados si lo [incluimos en un `iterador`](https://www.w3schools.com/python/python_iterators.asp) (pueden encontrar mas informacion de iteradores en Python y funciones apropiadas para ello [en este enlace](https://realpython.com/python-itertools/)). 


```python
wci=iter(wifiConnectionInfo)
```


```python
print(next(wci))
```


```python
print(next(wci))
```


```python
print(next(wci))
```

Tambien podemos acceder a traves de una iteracion:


```python
wci=iter(wifiConnectionInfo)
for r in wci:
    print(r)
```

En muchas ocasiones nos interesa unicamente el resultado de la funcion. En ese caso lo asignamos a una variable, como en el ejemplo siguiente:


```python
toast = droide.makeToast("Hola desde Jupyter!")
result = toast.result
print(result)
```


```python
wifiConnectionInfo = droide.wifiGetConnectionInfo().result
```


```python
wifiConnectionInfo
```

Como mencionamos, la estructura de `result` es un diccionario, un tipo de variable que se caracteriza por siempre ocurrir en pares de llaves/valores. Podemos acceder a las llaves con el metodo `keys()`:


```python
wifiConnectionInfo.keys()
```

y a los valores con el metodo `values()`:


```python
wifiConnectionInfo.values()
```

Los diccionarios pueden iterarse usando el metodo` items()` del diccionario como sigue:


```python
for k,v in wifiConnectionInfo.items():
    print(v)
```

El metodo usado regresa una vista del conjunto de elementos del diccionario. Otra forma de acceder a los contenidos es usando `iteritems()`, `viewitems()`.


```python
wifiConnectionInfo.viewitems()
```

```python
for items in wifiConnectionInfo.iteritems():
    print items
```

### Fin del paréntesis...) 

## Explorando la API de Python: AndroidFacade
Duration: 5:00

Vamos a explorar varios de los comandos disponibles en la API y ejecutarlos de manera que puedan emplearlos en aplicaciones futuras. Vamos a utilizar la estructura de programacion modular que se emplea tradicionalmente con Python, de modo que encapsulamos cada llamada a la función de la API en una función que nos permite evaluar si se ejecuta correctamente. Todas las notas se obtuvieron de [la documentación de la API.](https://github.com/damonkohler/sl4a/blob/wiki/ApiReference.md)

Comenzaremos con algunos ejemplos de [rutinas generales de Android](http://www.mithril.com.au/android/doc/AndroidFacade.html):

### <sub>vibrate</sub> ###
```
vibrate(
 Integer duration[optional, default 300]: duration in milliseconds)

Vibrates the phone or a specified duration in milliseconds.
```

### <sub>Ejemplo</sub> ###
```python
def test_vibrate():
  result = droide.vibrate()
  return result.error is None

test_vibrate()

```
### <sub>makeToast</sub> ###
```
makeToast(
  String message)

Displays a short-duration Toast notification.
```

### <sub>Ejemplo</sub> ###

```python
def test_make_toast():
  result = droide.makeToast('Un brindis por ustedes!')
  return result.error is None

test_make_toast()
```

### <sub>notify</sub> ###
```
notify(
 String title: title,
 String message)

Displays a notification that will be canceled when the user clicks on it.
```

### <sub>Ejemplo</sub> ###

```python
def test_notify():
  result = droid.notify('Primero el Titulo', 'Despues el mensaje de notificacion!')
  return result.error is None

test_notify()
```

### <sub>getClipboard</sub> ###
```
getClipboard()

Read text from the clipboard.

Returns:
  The text in the clipboard.
```

### <sub>setClipboard</sub> ###
```
setClipboard(
  String text)

Put text in the clipboard.
```

### <sub>Ejemplo</sub> ###

```python
def test_clipboard():
  previous = droide.getClipboard().result
  msg = 'Hola, clase!'
  droide.setClipboard(msg)
  echo = droide.getClipboard().result
  droide.setClipboard(previous)
  return echo == msg

test_clipboard()
```

## Explorando la API de Python: TextToSpeechFacade
Duration: 2:00

### <sub>ttsSpeak</sub> ###
```
ttsSpeak(
  String message)

Speaks the provided message via TTS.

Requires API Level 4.
```

### <sub>Ejemplo</sub> ###

```python
def test_speak():
  result = droide.ttsSpeak('Hola, clase')
  return result.error is None

test_speak()
```

## Explorando la API de Python: UiFacade
Duration: 10:00

### <sub>dialogCreateAlert</sub> ###
```
dialogCreateAlert(
 String title[optional],
  String message[optional])

Create alert dialog.
```

### <sub>dialogSetPositiveButtonText</sub> ###
```
dialogSetPositiveButtonText(
 String text)

Set alert dialog positive button text.
```

### <sub>dialogShow</sub> ###
```
dialogShow()

Show dialog.
```

### <sub>dialogGetResponse</sub> ###
```
dialogGetResponse()

Returns dialog response.
```

### <sub>Ejemplo</sub> ###

```python
def test_alert_dialog():
  title = 'Interfaz de usuario'
  message = 'Bienvenidos a la prueba de integracion del curso.'
  droide.dialogCreateAlert(title, message)
  droide.dialogSetPositiveButtonText('Continuar')
  droide.dialogShow()
  response = droide.dialogGetResponse()
  return response

test_alert_dialog()
```

### <sub>dialogSetNegativeButtonText</sub> ###
```
dialogSetNegativeButtonText(
  String text)

Set alert dialog button text.
```

### <sub>dialogSetNeutralButtonText</sub> ###
```
dialogSetNeutralButtonText(
  String text)

Set alert dialog button text.
```

### <sub>Ejemplo</sub> ###

```python
def test_alert_dialog_with_buttons():
  title = 'Alerta!'
  message = ('Esta alerta tiene 3 botones y '
             'espera a que presiones alguno.')
  droide.dialogCreateAlert(title, message)
  droide.dialogSetPositiveButtonText('Positive')
  droide.dialogSetNegativeButtonText('Negative')
  droide.dialogSetNeutralButtonText('Neutral')
  droide.dialogShow()
  response = droide.dialogGetResponse().result
  return response['which'] in ('positive', 'negative', 'neutral')

test_alert_dialog_with_buttons()
```

### <sub>dialogSetItems</sub> ###
```
dialogSetItems(
  JSONArray items)

Set alert dialog list items.
```

### <sub>Ejemplo</sub> ###

```python
def test_alert_dialog_with_list():
  title = 'Alerta'
  droide.dialogCreateAlert(title)
  droide.dialogSetItems(['item 1', 'item 2', 'item 3'])
  droide.dialogShow()
  response = droide.dialogGetResponse().result
  return True

test_alert_dialog_with_list()
```

### <sub>dialogSetSingleChoiceItems</sub> ###
```
dialogSetSingleChoiceItems(
 JSONArray items,
 Integer selected[optional, default 0]: selected item index)

Set dialog single choice items and selected item.
```

### <sub>Ejemplo</sub> ###

```python
def test_alert_dialog_with_single_choice_list():
  title = 'Alert'
  droide.dialogCreateAlert(title)
  droide.dialogSetSingleChoiceItems(['item 1', 'item 2', 'item 3'])
  droide.dialogSetPositiveButtonText('Acepto')
  droide.dialogShow()
  response = droide.dialogGetResponse().result
  return True

test_alert_dialog_with_single_choice_list()
```

### <sub>dialogSetMultiChoiceItems</sub> ###
```
dialogSetMultiChoiceItems(
 JSONArray items,
 JSONArray selected[optional]: list of selected items)

Set dialog multiple choice items and selection.
```

### <sub>Ejemplo</sub> ###

```python
def test_alert_dialog_with_multi_choice_list():
  title = 'Alert'
  droide.dialogCreateAlert(title)
  droide.dialogSetMultiChoiceItems(['item 1', 'item 2', 'item 3'], [])
  droide.dialogSetPositiveButtonText('Acepto')
  droide.dialogShow()
  response = droide.dialogGetResponse().result
  return True

test_alert_dialog_with_multi_choice_list()
```

### <sub>dialogCreateSpinnerProgress</sub> ###
```
dialogCreateSpinnerProgress(
 String title[optional],
 String message[optional],
 Integer maximum progress[optional, default 100])

Create a spinner progress dialog.
```

### <sub>dialogDismiss</sub> ###
```
dialogDismiss()

Dismiss dialog.
```

### <sub>Ejemplo</sub> ###

```python
import time

def test_spinner_progress():
  title = 'Ruedo sin parar'
  message = 'Esta es una rueda de progreso simple.'
  droide.dialogCreateSpinnerProgress(title, message)
  droide.dialogShow()
  time.sleep(2)
  droide.dialogDismiss()
  return True

test_spinner_progress()
```

### <sub>dialogCreateHorizontalProgress</sub> ###
```
dialogCreateHorizontalProgress(
 String title[optional],
 String message[optional],
 Integer maximum progress[optional, default 100])

Create a horizontal progress dialog.
```

### <sub>dialogSetCurrentProgress</sub> ###
```
dialogSetCurrentProgress(
 Integer current)

Set progress dialog current value.
```

### <sub>Ejemplo</sub> ###

```python
def test_horizontal_progress():
  title = 'Horizontal'
  message = 'Esta es simplemente una barra de progreso horizontal simple.'
  droid.dialogCreateHorizontalProgress(title, message, 50)
  droid.dialogShow()
  for x in range(0, 50):
    time.sleep(0.1)
    droid.dialogSetCurrentProgress(x)
  droid.dialogDismiss()
  return True

test_horizontal_progress()
```

## Explorando la API de Python: ApplicationManagerFacade
Duration: 5:00

[Application Manager Facade](http://www.mithril.com.au/android/doc/ApplicationManagerFacade.html)

### <sub>getRunningPackages</sub> ###
```
getRunningPackages()

Returns a list of packages running activities or services.

Returns:
  List of packages running activities.
```

### <sub>Ejemplo</sub> ###

```python
def test_get_running_packages():
  result = droid.getRunningPackages()
  return result.error is None
```

## Explorando la API de Python: BatteryManagerFacade
Duration: 5:00

[Battery Manager Facade](http://www.mithril.com.au/android/doc/BatteryManagerFacade.html)

### <sub>batteryStartMonitoring</sub> ###
```
batteryStartMonitoring()

Starts tracking battery state.

Generates "battery" events.
```

### <sub>batteryGetStatus</sub> ###
```
batteryGetStatus()

Returns  the most recently received battery status data:
1 - unknown;
2 - charging;
3 - discharging;
4 - not charging;
5 - full;
```

### <sub>batteryStopMonitoring</sub> ###
```
batteryStopMonitoring()

Stops tracking battery state.
```

### <sub>Ejemplo</sub> ###

```python
def test_battery():
  droide.batteryStartMonitoring()
  time.sleep(1)
  try:
    return bool(droide.batteryGetStatus())
  finally:
    droide.batteryStopMonitoring()
    
test_battery()
```

## Explorando la API de Python: EventFacade y SensorManagerFacade
Duration: 10:00

[Event Facade](http://www.mithril.com.au/android/doc/EventFacade.html)
[Sensor Manager Facade](http://www.mithril.com.au/android/doc/SensorManagerFacade.html)

### <sub>eventClearBuffer</sub> ###
```
eventClearBuffer()

Clears all events from the event buffer.
```

### <sub>eventPoll</sub> ###
```
eventPoll(
 Integer number_of_events[optional, default 1])

Returns and removes the oldest n events (i.e. location or sensor update, etc.)  
from the event buffer.

Returns:
  A List of Maps of event properties.
```

### <sub>Ejemplo</sub> ###

```python
def event_loop():
  for i in range(10):
    time.sleep(1)
    droid.eventClearBuffer()
    time.sleep(1)
    e = droid.eventPoll(1)
    if e.result is not None:
      return True
  return False    
```

### <sub>startSensingTimed</sub> ###
```
startSensingTimed(
 Integer sensorNumber: 1 = All, 2 = Accelerometer, 3 = Magnetometer and 4 =     
Light,
 Integer delayTime: Minimum time between readings in milliseconds)

Starts recording sensor data to be available for polling.

Generates "sensors" events.
```

### <sub>stopSensing</sub> ###
```
stopSensing()

Stops collecting sensor data.
```

### <sub>Ejemplo</sub> ###

```python
def test_sensor():
  # acelerometro 1 lectura por segundo
  droid.startSensingTimed(2, 1000)
  try:
    return event_loop()
  finally:
    droid.stopSensing()
```

## Explorando la API de Python: EventFacade y LocationFacade
Duration: 10:00

[Event Facade](http://www.mithril.com.au/android/doc/EventFacade.html)
[Location Facade](http://www.mithril.com.au/android/doc/LocationFacade.html)

### <sub>startLocating</sub> ###
```
startLocating(
 Integer minDistance[optional, default 60000]: minimum time between updates in  
milliseconds,
 Integer minUpdateDistance[optional, default 30]: minimum distance between      
updates in meters)

Starts collecting location data.

Generates "location" events.
```

### <sub>stopLocating</sub> ###
```
stopLocating()

Stops collecting location data.
```

### <sub>Ejemplo</sub> ###

```python
def test_gps():
  droid.startLocating()
  try:
    return event_loop()
  finally:
    droid.stopLocating()
```

### <sub>getLastKnownLocation</sub> ###
```
getLastKnownLocation()

Returns the last known location of the device.

Returns:
  A map of location information by provider.
```

### <sub>Ejemplo</sub> ###

```python
def test_get_last_known_location():
  result = droid.getLastKnownLocation()
  return result.error is None
```


### <sub>geocode</sub> ###
```
geocode(
 Double latitude,
 Double longitude,
 Integer maxResults[optional, default 1]: maximum number of results)

Returns a list of addresses for the given latitude and longitude.

Returns:
  A list of addresses.
```

### <sub>Ejemplo</sub> ###

```python
def test_geocode():
  result = droid.geocode(0.0, 0.0, 1)
  return result.error is None
```

## Explorando la API de Python: EventFacade y PhoneFacade
Duration: 5:00

[Event Facade](http://www.mithril.com.au/android/doc/EventFacade.html)
[Phone Facade](http://www.mithril.com.au/android/doc/PhoneFacade.html)

### <sub>startTrackingPhoneState</sub> ###
```
startTrackingPhoneState()

Starts tracking phone state.

Generates "phone" events.
```

### <sub>stopTrackingPhoneState</sub> ###
```
stopTrackingPhoneState()

Stops tracking phone state.
```

### <sub>Ejemplo</sub> ###

```python
def test_phone_state():
  droid.startTrackingPhoneState()
  try:
    return event_loop()
  finally:
    droid.stopTrackingPhoneState()
```

## Explorando la API de Python: EventFacade y SettingsFacade
Duration: 5:00

[Event Facade](http://www.mithril.com.au/android/doc/EventFacade.html)
[Settings Facade](http://www.mithril.com.au/android/doc/SettingsFacade.html)

### <sub>toggleRingerSilentMode</sub> ###
```
toggleRingerSilentMode(
 Boolean enabled[optional])

Toggles ringer silent mode on and off.

Returns:
  True if ringer silent mode is enabled.
```

### <sub>Ejemplo</sub> ###

```python
def test_ringer_silent():
  result1 = droid.toggleRingerSilentMode()
  result2 = droid.toggleRingerSilentMode()
  return result1.error is None and result2.error is None
```

### <sub>getRingerVolume</sub> ###
```
getRingerVolume()

Returns the current ringer volume.
```

### <sub>setRingerVolume</sub> ###
```
setRingerVolume(
  Integer volume)

Sets the ringer volume.
```

### <sub>Ejemplo</sub> ###

```python
def test_ringer_volume():
  get_result = droid.getRingerVolume()
  if get_result.error is not None:
    return False
  droid.setRingerVolume(0)
  set_result = droid.setRingerVolume(get_result.result)
  if set_result.error is not None:
    return False
  return True
```

## Explorando la API de Python: EventFacade y WifiFacade
Duration: 5:00

[Event Facade](http://www.mithril.com.au/android/doc/EventFacade.html)
[Wifi Facade](http://www.mithril.com.au/android/doc/WifiFacade.html)

### <sub>toggleWifiState</sub> ###
```
toggleWifiState(
 Boolean enabled[optional])

Toggle Wifi on and off.

Returns:
  True if Wifi is enabled.
```

### <sub>Ejemplo</sub> ###

```python
def test_wifi():
  result1 = droid.toggleWifiState()
  result2 = droid.toggleWifiState()
  droid.toggleWifiState(True)  # Make sure wifi ends up ON, as it interferes with other tests
  return result1.error is None and result2.error is None
```

    
## Retroalimentación
Duration: 1:00

Survey
: La API de Python en Android proporciona una estructura de acceso a
 - Una radiografía de los sensores
 - Un mundo donde reina la oscuridad y el caos
 - Una serie de funciones para interactuar de forma interactiva con el sistema operativo Android
 - Nada
: El comando droide.startSensingTimed(2, 1000)
 - indica al dispositivo que comience a tomar datos del estado de red local usando un paquete de 1000 bytes
 - indica al dispositivo que tenga la sensitividad relativa de 2 en 1000
 - indica al dispositivo que una lectura del sensor del aceleromentro deberá realizarse cada segundo
 - india al dispositivo que una lectura del sensor del magentómetro deberá realizarse cada 1000 mili segundos
 - No indica nada ni genera ningun sentimiento en el dispositivo
 
## Entregas
Duration: 1:00

Las entregas se especifican empezando con el nombre del archivo y la descripción del contenido.

positive
: 
	1. Sem2Lab1_notify.gif: Captura de pantalla del dispositivo móvil mostrando una notificación.

	2. Sem2Lab1_ui.gif: Animacion que muestra una interacción entre el dispositivo móvil y el usuario donde haga lo siguiente:
	    * pregunte al usuario su nombre
	    * le pida ingresar su nombre
	    * le pida ingresar su fecha de nacimiento dialogCreateDatePicker()
	    * muestre una rueda de progreso mientras calcula la edad
	    * emita una notificación al terminar de calcular la edad
	    * convierta el siguiente texto a voz: "Hola $NOMBRE, tu fecha de nacimiento es $DOB y tu edad es $EDAD"

## Documentacion de todas las Facades
Duration: 1:00

¡Wow! ¡Eso fue mucho trabajo! Pero para ahora saben un poco mas como utilizar las diferentes llamadas a la API. Por supuesto no hemos revisado ni una cuarta parte de la funcionalidad disponible! hay mas de 20 fachadas que integran las diferentes funcionalidades de la API, listadas a continuacion. 


<html><head><title>SL4A API Help</title></head>
<body>
<table border=1>
<tr><td><a href="http://www.mithril.com.au/android/doc/ActivityResultFacade.html">ActivityResultFacade</a></td><td>Allows you to return results to a startActivityForResult call</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/AndroidFacade.html">AndroidFacade</a></td><td>Some general purpose Android routines</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/ApplicationManagerFacade.html">ApplicationManagerFacade</a></td><td>Facade for managing Applications</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/BatteryManagerFacade.html">BatteryManagerFacade</a></td><td>Exposes Batterymanager API</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/BluetoothFacade.html">BluetoothFacade</a></td><td>Bluetooth functions</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/CameraFacade.html">CameraFacade</a></td><td>Access Camera functions</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/CommonIntentsFacade.html">CommonIntentsFacade</a></td><td>A selection of commonly used intents</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/ContactsFacade.html">ContactsFacade</a></td><td>Provides access to contacts related functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/EventFacade.html">EventFacade</a></td><td>Manage the event queue</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/EyesFreeFacade.html">EyesFreeFacade</a></td><td>Provides Text To Speech services for API 3 or less</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/LocationFacade.html">LocationFacade</a></td><td>This facade exposes the LocationManager related functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/MediaPlayerFacade.html">MediaPlayerFacade</a></td><td>This facade exposes basic mediaPlayer functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/MediaRecorderFacade.html">MediaRecorderFacade</a></td><td>A facade for recording media</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/PhoneFacade.html">PhoneFacade</a></td><td>Exposes TelephonyManager functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/PreferencesFacade.html">PreferencesFacade</a></td><td>This facade allows access to the Preferences interface</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/SensorManagerFacade.html">SensorManagerFacade</a></td><td>Exposes the SensorManager related functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/SettingsFacade.html">SettingsFacade</a></td><td>Exposes phone settings functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/SignalStrengthFacade.html">SignalStrengthFacade</a></td><td>Exposes SignalStrength functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/SmsFacade.html">SmsFacade</a></td><td>Provides access to SMS related functionality</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/SpeechRecognitionFacade.html">SpeechRecognitionFacade</a></td><td>A facade containing RPC implementations related to the speech-to-text functionality of Android</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/TextToSpeechFacade.html">TextToSpeechFacade</a></td><td>Provides Text To Speech services for API 4 or more</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/ToneGeneratorFacade.html">ToneGeneratorFacade</a></td><td>Generate DTMF tones</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/UiFacade.html">UiFacade</a></td><td>User Interface Facade</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/WakeLockFacade.html">WakeLockFacade</a></td><td>A facade exposing some of the functionality of the PowerManager, in particular wake locks</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/WebCamFacade.html">WebCamFacade</a></td><td>Manages access to camera streaming</td></tr>
<tr><td><a href="http://www.mithril.com.au/android/doc/WifiFacade.html">WifiFacade</a></td><td>Wifi functions</td></tr>
</body></html>
