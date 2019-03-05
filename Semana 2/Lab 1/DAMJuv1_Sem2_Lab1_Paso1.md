
## Estableciendo contacto con el droide
Duration: 7:00

Revisemos primero los requerimentos para conectarse al dispositivo desde su sistema local. Cabe recordar que necesitaremos
* adb
* variable de entorno AP_PORT con el valor 9999 definida en el sistema
* el servicio de Python corriendo en el dispositivo Android
* el modulo `android.py` accesible al interprete


|Video: Iniciar y configurar servicio de Python en Android | |
|-----|-----|
| 1. Antes de comenzar, conectar el dispositivo movil a su computadora con un cable USB y asegurarse que el servicio de Python esta ejecutandose \\ 2. Abre una libreta de Jupyter en tu sistema local y en un celda de codigo ejecuta las instrucciones que siguen | ![check_config.gif](attachment:check_config.gif)  |


1. Revisar que el dispositivo esta conectado a la computadora


```python
!adb devices
```

2. Una vez iniciado el servicio de Python en el dispositivo, ejecuta lo siguiente


```python
!adb forward tcp:9999 tcp:2222
```

3. Ahora invocamos el modulo `android.py`


```python
import android
```

4. Creamos un objeto de la clase android que llamaremos droide


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
