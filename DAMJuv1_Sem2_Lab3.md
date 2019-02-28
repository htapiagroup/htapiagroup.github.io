---
id: rysi2019-s2-l03
summary: En este laboratorio 
categories: android
status: draft
feedback_url: https://github.com/htapiagroup/htapiagroup.github.io/issues
tags: sl4a, python, adb, ports, forward
difficulty: 3
published: 2019-02-21
author: HTM <htapia@lania.edu.mx>

---

# Aplicaciones de geolocalizacion

## Introduccion
Duration: 1:00

Ademas de los sensores embebidos, los dispositivos modernos contienen antenas que se utilizan para la comunicacion. En este laboratorio vamos a enfocarnos particularmente en la antena de GPS, utilizada para determinar la ubicacion del dispositivo.


## Antenas y radios
Duration: 7:00
positive
: **Nota:** Siempre es buena idea revisar que la comunicacion con el dispositivo sige activa al comienzo de una nueva libreta. Aun cuando la conexion a traves de `tcp` deberia seguir activa mientras el servicio siga vigente en el dispositivo, no es necesario volver a establecerla. Sin embargo, en cada nueva libreta **sí** se tiene que importar el modulo `android`.

Vamos a seguir la recomendacion de la nota anterior y verificar que el dispositivo esta conectado a la computadora. 

### Estableciendo conexion con el dispositivo


```python
!adb forward tcp:9999 tcp:2222
```


```python
import android
droide=android.Android()
droide.makeToast("Hola desde Jupyter!")
```




    Result(id=0, result=None, error=None)



## Localizacion
En 1995 existen en operacion satelites que proporcionan a los civiles un [Sstema de Posicionamiento Global (en inglés, **GPS**; Global Positioning System)](https://es.wikipedia.org/wiki/GPS) que permite determinar la posicion de cualquier objeto sobre la Tierra.

Los dispositivos moviles modernos tienen una antena receptora de GPS y la API de Python ofrece algunas funciones para extraer valores de ella que permiten conocer la geoposicion actual del dispositivo. Una de ellas es

### <sub>getLastKnownLocation</sub> ###
```
getLastKnownLocation()

Returns the last known location of the device.

Returns:
  A map of location information by provider.
```


```python
droide.getLastKnownLocation()
```




    Result(id=1, result={u'passive': {u'bearing': 81, u'altitude': 1381, u'provider': u'fused', u'longitude': -96.9155753776431, u'time': 1551204503400L, u'latitude': 19.519595811143517, u'speed': 0, u'accuracy': 9367.2490234375}, u'network': {u'bearing': 0, u'altitude': 1348.0999755859375, u'provider': u'network', u'longitude': -96.915587, u'time': 1551195658939L, u'latitude': 19.5196228, u'speed': 0, u'accuracy': 22.746000289916992}, u'gps': {u'bearing': 6.123749256134033, u'altitude': 1330.932861328125, u'provider': u'gps', u'longitude': -96.9156464323333, u'time': 1551194104500L, u'latitude': 19.520619408385585, u'speed': 7, u'accuracy': 1681.982421875}}, error=None)



## Parentesis
Para desarrollar aplicaciones relacionadas a informacion de geoposicion es util contar con una herramienta para visualizar mapas. La libreria `matplotlib` cuenta con funcionalidad elemental para graficos simples, por ejemplo graficas de dispersion, barras, etc. Por supuesto los pares de latitud,longitud pueden visualizarse con funnciones disponibles en esta libreria sin embargo, no muestran capas de referencia a la geolocalizacion (mapas). Aunque existen varias librearias que proporcionan funciones utiles y flexibles para visualizar mapas, entre ellas `basemaps`, `ipyleaflet`, `bokeh`, `gmaps`, etc. vamos a utilizar nuestro dispositivo que ya contiene un a "libreria" de mapas instalada: la aplicacion de mapas de Google.

### Abrir una aplicacion en el dispositivo
Primero veamos como abrir una aplicacion en el dispositivo desde Python. Podemos ver la lista de aplicaciones en el dispositivo con la siguiente instruccion:


```python
droide.getLaunchableApplications().result
```




    {u'Actualizar sistema': u'com.tcl.fota.FotaClientActivity',
     u'Ajustes': u'com.android.settings.Settings',
     u'Ajustes de Google': u'com.google.android.gms.app.settings.GoogleSettingsActivity',
     u'Asistente': u'com.google.android.apps.gsa.staticplugins.opa.EnterOpaActivityFromLauncher',
     u'Book Cr...': u'com.appli.bookcr.WelcomeActivity',
     u'B\xfasqueda por voz': u'com.google.android.googlequicksearchbox.VoiceSearchActivity',
     u'Calculadora': u'com.tct.calculator.Calculator',
     u'Calendario': u'com.android.calendar.AllInOneActivity',
     u'Cars Racing': u'com.gameloft.android.GloftCRSM.Start',
     u'Chrome': u'com.google.android.apps.chrome.Main',
     u'Claro juegos': u'com.gameloft.android.gdc.GDCMainShop',
     u'Claro m\xfasica': u'com.telcel.imk.MainActivity',
     u'ClaroVideo': u'com.clarovideo.app.ui.activities.SplashActivity',
     u'Clarokids': u'com.movile.playkids.UnityPlayerActivity',
     u'Clarosalud': u'com.elunviersal.isalud.ClarosaludActivity',
     u'Clarosync': u'com.fsecure.mobile.sync.StartupActivity',
     u'Consumo': u'telcel.net.MainActivity',
     u'Contactos': u'com.android.contacts.activities.PeopleActivity',
     u'Correo electr\xf3nico': u'com.tct.email.activity.Welcome',
     u'C\xe1mara': u'com.android.camera.CameraLauncher',
     u'DCI': u'appinventor.ai_horaciotmc.DCI2.Screen1',
     u'DU Recorder': u'com.duapps.screen.recorder.SplashActivity',
     u'Descargas': u'com.android.providers.downloads.ui.DownloadList',
     u'Drive': u'com.google.android.apps.docs.app.NewMainProxyActivity',
     u'Duo': u'com.google.android.apps.tachyon.MainActivity',
     u'Facebook': u'com.facebook.katana.LoginActivity',
     u'Fotos': u'com.google.android.apps.photos.home.HomeActivity',
     u'Galer\xeda': u'com.tct.gallery3d.app.PermissionActivity',
     u'Gesti\xf3n de archivos': u'com.jrdcom.filemanager.activity.FileBrowserActivity',
     u'Gmail': u'com.google.android.gm.ConversationListActivityGmail',
     u'Google': u'com.google.android.googlequicksearchbox.SearchActivity',
     u'Google+': u'com.google.android.apps.plus.phone.HomeActivity',
     u'Grabador de sonido': u'com.tct.soundrecorder.activity.SoundRecorderMainActivity',
     u'Hangouts': u'com.google.android.talk.SigningInActivity',
     u'IMU+GPS-Stream': u'de.lorenz_fenster.sensorstreamgps.SensorStreamActivity',
     u'Keep (notas)': u'com.google.android.keep.activities.BrowseActivity',
     u'Kingdoms & Lords': u'com.gameloft.android.GloftKLMF.Start',
     u'Lithium': u'com.faultexception.reader.MainActivity',
     u'Little Big City': u'com.gameloft.android.GloftLCEF.Start',
     u'Maps': u'com.google.android.maps.MapsActivity',
     u'Mensajes': u'com.android.mms.ui.ConversationList',
     u'Mi Telcel': u'com.speedymovil.wire.Login',
     u'M\xfasica': u'com.alcatel.music5.activities.Music5WelcomeActivity',
     u'NFC': u'com.jrdcom.apps.tag.MainMenu',
     u'Navegaci\xf3n Segura': u'com.fsecure.browser.BrowserActivity',
     u'Navegador': u'com.android.browser.BrowserActivity',
     u'Notas': u'com.tct.note.ui.NotesListActivity',
     u'Noticias': u'com.google.apps.dots.android.app.activity.CurrentsStartActivity',
     u'Onetouch Care': u'com.alcatel.alcatelonetouch.SplashScreenActivity',
     u'Phonto': u'com.youthhr.phonto.PhontoActivity',
     u'Play Juegos': u'com.google.android.gms.games.ui.destination.main.MainActivity',
     u'Play Libros': u'com.google.android.apps.books.app.BooksActivity',
     u'Play M\xfasica': u'com.android.music.activitymanagement.TopLevelActivity',
     u'Play Pel\xedculas': u'com.google.android.youtube.videos.EntryPoint',
     u'Play Store': u'com.android.vending.AssetBrowserActivity',
     u'Portal Claroideas': u'com.portal.Portal',
     u'Puzzle Pets': u'com.gameloft.android.GloftDBMF.Start',
     u'Python for Android': u'com.googlecode.pythonforandroid.PythonMain',
     u'QPython': u'org.qpython.qpy.main.activity.SplashActivity',
     u'Radio': u'com.tct.fmradio.ui.FMActivity',
     u'Reloj': u'com.tct.timetool.DeskClock',
     u'SIM Telcel': u'com.android.stk.StkLauncherActivity',
     u'SL4A': u'com.googlecode.android_scripting.activity.ScriptManager',
     u'SPIDER-MAN: ULTIMATE POWER': u'com.gameloft.android.GloftSMIM.Start',
     u'Seguridad Total': u'com.fsecure.ms.ui.LauncherActivity',
     u'Smart Suite': u'com.tcl.smartsuite.MainActivity',
     u'Support': u'com.rsupport.rs.activity.edit.IntroActivity',
     u'Telcel': u'com.telcel.contenedor.gui.activities.AppsContainerActivity',
     u'Tel\xe9fono': u'com.android.dialer.DialtactsActivity',
     u'TfLiteCameraDemo': u'com.example.android.tflitecamerademo.CameraActivity',
     u'Video Comprimir': u'com.melgames.videocompress.AlbumListActivity',
     u'Vysor': u'com.koushikdutta.vysor.StartActivity',
     u'WA Business': u'com.whatsapp.Main',
     u'Walli': u'com.shanga.walli.mvp.splash.SplashActivity',
     u'Wi-Fi Display': u'com.android.settings.wfd.WifiDisplayEnableActivity',
     u'Wi-Fi Transfer': u'com.jrdcom.wifitransfer.welcome.LauncherActivity',
     u'YouTube': u'com.google.android.youtube.app.honeycomb.Shell$HomeActivity',
     u'wecourses': u'net.wecourses.mobile.wecourses'}



positive
: La lista de aplicaciones varia de acuerdo al modelo y version de sistema que tenga el dispositivo movil.

Podemos ver que la estructura del diccionario es un par de llave:valor de la forma `{u'AppName':u'ClassName'}`. En algunas ocasiones es posible iniciar las aplicaciones usando la instruccion `launch(u'ClassName')`, por ejemplo


```python
fb = droide.getLaunchableApplications().result['Facebook']
print(fb)
```

    com.facebook.katana.LoginActivity
    


```python
droide.launch(fb)
```




    Result(id=50, result=None, error=None)



Sin embargo no todos las clases que se obtienen del comando `getLaunchableApplications` se ejecutan directamente, por ejemplo no es posible iniciar la aplicacion de mapas si usamos la misma estrategia. Ejecuten el siguiente codigo y observen que no se abre la aplicacion esperada en el dispositivo. 


```python
mapas = droide.getLaunchableApplications().result['Maps']
print(mapas)
droide.launch(mapas)
```

    com.google.android.maps.MapsActivity
    




    Result(id=52, result=None, error=None)



La razon es que la clase a la que esta ligada la aplicacion `Maps` no es un programa sino una *actividad*. Para iniciar el programa de mapas podemos usar

### <sub>startActivity</sub> ###
```
startActivity(
 String action,
 String uri[optional],
 String type[optional]: MIME type/subtype of the URI,
 JSONObject extras[optional]: a Map of extras to add to the Intent,
 Boolean wait[optional]: block until the user exits the started activity,
 String packagename[optional]: name of package. If used, requires classname to  
be useful,
 String classname[optional]: name of class. If used, requires packagename to be 
useful)

Starts an activity.
```

como sigue:


```python
droide.startActivity(
                    'android.intent.action.MAIN',
                    None,
                    None,
                    None,
                    False,
                    'com.google.android.apps.maps',
                    'com.google.android.maps.MapsActivity'
                    )
```




    Result(id=60, result=None, error=None)



La aplicacion inicia desplegando el mapa mas reciente. Es posible especificar una localizacion para el mapa si se espcifica el segundo argumneto `uri` de la funcion. Por ejemplo:


```python
uri = "geo:19.024666568 -97.268665592"
droide.startActivity(
                    'android.intent.action.VIEW',
                    uri,
                    None,
                    None,
                    False,
                    'com.google.android.apps.maps',
                    'com.google.android.maps.MapsActivity'
                    )
```




    Result(id=4, result=None, error=None)



Para obtener la mas reciente geolocalizacion del dispositivo podemos usar la siguiente instruccion:


```python
droide.getLastKnownLocation().result
```




    {u'gps': {u'accuracy': 48,
      u'altitude': 1359,
      u'bearing': 0,
      u'latitude': 19.51968751,
      u'longitude': -96.91547261,
      u'provider': u'gps',
      u'speed': 0,
      u'time': 1551212647000L},
     u'network': {u'accuracy': 23.569000244140625,
      u'altitude': 0,
      u'bearing': 0,
      u'latitude': 19.5195932,
      u'longitude': -96.9155206,
      u'provider': u'network',
      u'speed': 0,
      u'time': 1551212669686L},
     u'passive': {u'accuracy': 23.569000244140625,
      u'altitude': 0,
      u'bearing': 0,
      u'latitude': 19.5195932,
      u'longitude': -96.9155206,
      u'provider': u'network',
      u'speed': 0,
      u'time': 1551212669686L}}



Observen que la funcion regresa un diccionario con tres llaves: `gps`, `network` y `passive`, cada una con valores que son nuevamente otro diccionario que contienen detalles de la localizacion. Por el momento nos interesan unicamente `latitude` y `longitude`de la informacion proporcionada por el GPS:


```python
lon,lat=[droide.getLastKnownLocation().result['gps'][l] for l in ('longitude','latitude')]
```


```python
uri = "geo:%s %s?z=15"%(lat,lon)
droide.startActivity(
                    'android.intent.action.VIEW',
                    uri,
                    None,
                    None,
                    False,
                    'com.google.android.apps.maps',
                    'com.google.android.maps.MapsActivity'
                    )
```




    Result(id=21, result=None, error=None)



### Invocando aplicaciones
la funcion `startActivity` puede usarse para iniciar aplicaciones usando las opciones de otras formas:


```python
droide.startActivity('android.intent.action.VIEW', 
      'vnd.youtube:1_u0NmrFM0c', None, None, False, None, None)
```




    Result(id=22, result=None, error=None)



### Ejercicio
Busca en internet las coordenadas de alguna localizacion de interes para ti (puedes encontrar idean [en esta pagina](https://latitude.to/articles-by-country/mx/mexico/)) y muestra en el mapa la posicion desde 

## Navegacion
En los ejemplos previos usamos la geolocalizacion mas reciente que "recuerda" el dispositivo. Podemos utilizar la informacion de GPS para conocer nuestra ubicacion actual y para darle seguimiento a cambios en la geolocalizacion.

positive
: El uso continuo del GPS puede reducir la bateria considerablemente. En el siguiente laboratorio vamos a monitorear la bateria y podran darse cuenta de esto con los datos de su dispositivo. 

Para conocer la posicion actual del dispositivo podemos usar

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


```python
!adb forward tcp:9999 tcp:2222
import android
droide=android.Android()
droide.makeToast("Hola desde Jupyter!")
```




    Result(id=0, result=None, error=None)




```python
droide.startLocating()
```




    Result(id=1, result=None, error=None)




```python
print "reading GPS ..." 
event = droide.eventWaitFor('location',10000).result
```

    reading GPS ...
    


```python
print event
```

    {u'data': {u'fused': {u'bearing': 81, u'altitude': 1381, u'provider': u'fused', u'longitude': -96.91554019999998, u'time': 1551215303000L, u'latitude': 19.519610199999995, u'speed': 0, u'accuracy': 5733.21240234375}}, u'name': u'location', u'time': 1551215299697000L}
    


```python
if event['name'] == "location": 
    try: 
        lat = str(event['data']['gps']['latitude']) 
        lng = str(event['data']['gps']['longitude']) 
    except KeyError: 
        lat = str(event['data']['fused']['latitude']) 
        lng = str(event['data']['fused']['longitude'])
    latlng = 'lat: ' + lat + ' lng: ' + lng 
    print droide.makeToast("Coordenadas: %s"%latlng)
```

    Result(id=3, result=None, error=None)
    


```python
droide.stopLocating()
```




    Result(id=4, result=None, error=None)




```python
droide.startLocating()

event = droide.eventWaitFor('location',10000).result

if event['name'] == "location": 
    try: 
        lat = str(event['data']['gps']['latitude']) 
        lng = str(event['data']['gps']['longitude']) 
    except KeyError: 
        lat = str(event['data']['fused']['latitude']) 
        lng = str(event['data']['fused']['longitude'])
    latlng = 'lat: ' + lat + '\nlng: ' + lng 
    print droide.makeToast("Coordenadas: \n %s"%latlng)

droide.stopLocating()
```

    Result(id=18, result=None, error=None)
    




    Result(id=19, result=None, error=None)




```python
droide.geocode(lat, lon).result
```




    [{u'admin_area': u'Veracruz',
      u'country_code': u'MX',
      u'country_name': u'M\xe9xico',
      u'feature_name': u'80',
      u'locality': u'Xalapa Enr\xedquez',
      u'postal_code': u'91000',
      u'sub_admin_area': u'Xalapa',
      u'thoroughfare': u'Avenida Enrique C. R\xe9bsamen'}]




```python
uri = "google.navigation:q=Los Pinos CDMX"
droide.startActivity(
                    'android.intent.action.VIEW',
                    uri,
                    None,
                    None,
                    False,
                    'com.google.android.apps.maps',
                    'com.google.android.maps.MapsActivity'
                    )
```




    Result(id=16, result=None, error=None)



## Enlaces y recursos externos

### Android
* https://stackoverflow.com/questions/48502915/get-list-of-all-apps-with-qpython-sl4a
* https://stackoverflow.com/questions/2662531/launching-google-maps-directions-via-an-intent-on-android
* https://stackoverflow.com/questions/48978716/open-html-file-on-android-with-python-androidhelper-sl4a
* https://stackoverflow.com/questions/6205827/how-to-open-standard-google-map-application-from-my-application
* https://developer.android.com/guide/appendix/g-app-intents.html
* https://android.stackexchange.com/questions/119144/android-tethering-shell-to-python-code-sl4a-api
 
### SL4A
* https://www.pythoncentral.io/series/python-sl4a-android-scripting-layer-tutorial/

