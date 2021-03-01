## [Guía] Rclone en Windows (conexión con Google Drive)

Esta pequeña guía consistirá en asignar almacenamiento en la nube a una unidad (las conocidas letras de unidades Windows) para tener acceso directo desde el explorador de Windows a todo el contenido del servicio al que nos estemos conectando.

El servicio al que me conectaré será de Google Drive, pero la guía es aplicable a cualquier otro servicio, simplemente podrá haber algún cambio a la hora de configurar el **remoto**. Para llevar a cabo este proceso contamos con tres aplicaciones diferentes.	 

- [Rclone](https://rclone.org/) para gestionar el almacenamiento en la nube. Es compatible con una [gran cantidad](https://rclone.org/#providers) de servicios de almacenamiento en la nube, en este caso lo utilizaré con el archiconocido Google Drive. 
- [WinFsp](http://www.secfs.net/winfsp/) que nos permitirá hacer uso del comando *rclone mount* en Windows tal y como indican en su [web oficial](https://rclone.org/commands/rclone_mount/#installing-on-windows). 
- [Nssm](https://nssm.cc/) que será de utilidad para registrar el comando *rclone mount* como **servicio** en Windows. 

### Rclone

Hace algún tiempo realice una [pequeña guía](https://tzinm.github.io/mis-apuntes/OpenMediaVault/Rclone/) para poner en funcionamiento Rclone en OpenMediaVault, el proceso de instalación involucraba la ejecución de varios comandos. En Windows no hay ningún proceso de instalación, viene empaquetado en un único fichero **.exe** el cual viene incluido en un fichero **.zip** que se encuentra a nuestra disposición en la [sección de descargas](https://rclone.org/downloads/) de su web oficial.

![rclone-zip.png?dl=0](https://dl.dropboxusercontent.com/s/hq51krwuvja5u4u/rclone-zip.png?dl=0)

Esta aplicación no incluye ninguna interfaz gráfica (hay alternativas de terceros) con lo cual la única forma de utilizarla es a través de la **línea de comandos**. Para poder ejecutar la aplicación debemos hacerlo desde el directorio o en la línea de comandos indicando la ruta completa hasta el ejecutable. Esto no es muy práctico, veremos como resolverlo de una forma sencilla, que además es aplicable a cualquier aplicación.	 

#### Variable de entorno PATH

Modificar la variable de entorno **PATH** nos permitirá ejecutar la aplicación Rclone (realmente cualquier otra aplicación) en la línea de comandos sin indicar la ruta completa hasta el ejecutable.

En primer lugar, voy a explicar como tengo organizados los ficheros relacionados con aplicaciones que no necesitan ser instaladas en el equipo. Por un lado, tengo un directorio en el que almaceno todas las aplicaciones portable, es decir, todas aquellas aplicaciones consideradas como **portables**. Por otro lado, he creado un directorio para almacenar accesos directos al ejecutable de estas aplicaciones, que será el directorio que estableceré en la variable **PATH**.	 

Los pasos a seguir son los siguientes:

- Abriremos las **Propiedades del sistema** ejecutando *sysdm.cpl* desde el cuadro **ejecutar**. 
- De las diferentes pestañas disponibles debemos posicionarnos en la de **Opciones avanzadas**, veremos abajo del todo las **Variables de entorno...**. 

![opciones-avanzadas-variables-de-entorno.](https://dl.dropboxusercontent.com/s/qjncfd5d3dq43ub/opciones-avanzadas-variables-de-entorno.png?dl=0)

- En las **Variables de entorno** tenemos dos secciones importantes, una que hace referencia a las variables de usuario que ha iniciado sesión y otra que hace referencia a las variables del sistema. 

![variables-de-entorno.png?dl=0](https://dl.dropboxusercontent.com/s/8ncyi31v62er7t8/variables-de-entorno.png?dl=0)

​	 

* Modificaremos dos variables, una que hace referencia al usuario y otra al sistema. 

- PATH --> **variable de usuario** a la que añadiremos la ruta al directorio en el que se encuentran los accesos directos. 

![variable-path.png?dl=0](https://dl.dropboxusercontent.com/s/o4alpqarbiqboi4/variable-path.png?dl=0)

- PATHEXT --> **variable del sistema** a la que debemos [añadir la extensión](https://superuser.com/questions/569466/add-a-directory-and-all-subdirectories-to-the-path-variable-with-a-single-entry#comment2394805_841100) que tienen los accesos directos. Estos ficheros cuentan con la extensión **.lnk**. 

![variable-pathext.png?dl=0](https://dl.dropboxusercontent.com/s/eijbip6b82uecyk/variable-pathext.png?dl=0)

- Una vez que las variables necesarias hayan sido modificadas ejecutaremos en la línea de comando *set PATH=C* para evitar [reiniciar o cerrar la sesión](https://search4fan.github.io/post/win10_new_system_variable_without_restart.html) del usuario. 

#### Configurar remote

No entraré en detalle sobre como configurar un **remoto**, en [este enlace](https://tzinm.github.io/mis-apuntes/OpenMediaVault/Rclone/#configuracion) o en la [guía oficial]() hay suficiente documentación como para configurar cualquier servicio de almacenamiento en la nube compatible con Rclone.

La forma más sencilla de hacerlo es ejecutar *rclone config* para que se lance la configuración guiada en la línea de comandos, que nos irá indicando paso a paso los datos que debemos introducir. Como comentaba al principio, he configurado mi cuenta de [Google Drive](https://rclone.org/drive). En este tipo de almacenamiento hay un punto importante a destacar durante la configuración, que es la creación un **[id independiente](https://rclone.org/drive/#making-your-own-client-id)** para no utilizar el propio de Rclone el cual ofrece ciertas limitaciones.	 

Una vez que tenemos el remoto configurado podemos ejecutar el comando *rclone ls nombre-remoto:* para comprobar que nos lista el contenido que hay en el remoto. Después de finalizar la configuración ejecutando de nuevo *rclone config* mostrará los **remotos** que tenemos actualmente configurados, además de una lista de acciones que podemos realizar.	 

![rclone-config.png?dl=0](https://dl.dropboxusercontent.com/s/8mno4vrp1n25bf0/rclone-config.png?dl=0)

#### Montaje de la unidad


Ya tenemos configurado el acceso al almacenamiento en la nube, el siguiente paso es montar el almacenamiento en una letra de unidad de las que tengamos disponible.

Comando necesario para montar el remoto en la letra de unidad **Z:** (puede ser cualquier otra letra).

```html
rclone mount GDrive: Z: --vfs-cache-mode full
```

*Más información sobre rclone mount -->* *[enlace](https://rclone.org/commands/rclone_mount/)*

Después de haber llegado hasta aquí ya tenemos acceso a nuestro almacenamiento en la nube desde el explorador de archivos, como si estuviésemos navegando en un disco duro interno del ordenador. Pero hay dos pequeños inconvenientes:

1. La consola debe estar funcionando en segundo plano. 
2. Después de reiniciar deberemos abrir la consola y ejecutar el comando *rclone mount*. 

Para evitar estos dos puntos, la solución que mejor me ha funcionado de varias que he probado ha sido configurando el comando como un servicio de Windows. Es en este momento donde entra la aplicación **nssm** mencionada al principio.	 

#### Montaje permanente

Al igual que sucede con Rclone, esta aplicación no necesita ser instalada además de funcionar desde la línea de comandos. Por lo que los primeros pasos que debemos seguir son:	 

1. Guardar el ejecutable en el directorio de **aplicaciones portables**. 
2. Generar una acceso directo que se guardará en el directorio que corresponde. 

Como tenemos configuradas correctamente las variables de entorno para que reconozca los accesos directos almacenados en el directorio empleado para ello, podemos trabajar con esta aplicación directamente desde la consola.

El comando *nssm* nos permite entre otras cosas **instalar**, **editar**, **eliminar**, **iniciar**, **parar**, **reiniciar** o conocer el **estado** del servicio. En nuestro caso nos interesa instalar un servicio nuevo, para ejecutaremos el comando *nssm install rclone-mount*, donde **rclone-mount** puede ser cualquier otro nombre, que abrirá una pequeña interfaz gráfica para configurar el servicio.	 

En la interfaz gráfica disponemos de varias pestañas para personalizar el servicio, las más relevantes son las de **Application** y la **Log On**. El resto de [pestañas](https://nssm.cc/usage) se pueden personalizar o no, en función de las necesidades de cada uno.

- **Application** 
- **Path**: se establece la ruta al ejecutable, en este caso donde hemos almacenado el fichero **rclone.exe**. 
- **Startup directory**: automáticamente establece el directorio donde se encuentra el fichero **.exe**. Se puede modificar, por si fuese necesario ejecutar la aplicación en un directorio específico. 
- **Arguments**: los argumentos que pasamos a **rclone**, es decir, *mount GDrive: Z: --vfs-cache-mode full*. 

![nssm-aplicacion-gui.png?dl=0](https://dl.dropboxusercontent.com/s/b471o1wag4dv4ln/nssm-aplicacion-gui.png?dl=0)

- **Log On**: nos permite seleccionar si el servicio se inicia con cualquier cuenta local, o por el contrario se inicia únicamente con la cuenta que especifiquemos. En mi caso me interesa únicamente iniciar el servicio con mi cuenta ya que hay más personas que utilizan este equipo. 	

![nssm-logon-gui.png?dl=0](https://dl.dropboxusercontent.com/s/fmnywuzpn77wqs3/nssm-logon-gui.png?dl=0)

Después de haber configurado las pestañas deseadas, pulsaremos en **Edit service** para guardar la configuración. Si abrimos la ventana de servicios, veremos que se ha configurado e iniciado correctamente.

​	![servicio-rclone-mount.png?dl=0](https://dl.dropboxusercontent.com/s/zzbybft3ajzxj7k/servicio-rclone-mount.png?dl=0)



#### Personalizar icono

Por defecto el icono que se muestra es el de un fichero de texto vacío, lo cual no es demasiado cómodo para identificar la unidad rápidamente. Una forma sencilla de modificar el icono a mostrar en una letra de unidad en particular es modificando el registro de Windows.	 

Dejo el [enlace](https://winaero.com/change-drive-icon-in-windows-10-with-a-custom-ico-file/) a los pasos que hay que seguir para conocer cuales son las claves que hay que crear. 

![gdrive-unidad.png?dl=0](https://dl.dropboxusercontent.com/s/mm3snhk7dqgl87s/gdrive-unidad.png?dl=0)

Para terminar, una web ([icons-icons.com](https://icon-icons.com/es/)) en la que se pueden encontrar multitud de iconos (ficheros **.ico**) para descargar de forma gratuita.