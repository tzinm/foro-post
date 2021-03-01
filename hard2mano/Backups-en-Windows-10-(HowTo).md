## Sincronización Windows - Disco duro externo

He comentado en varias ocasiones que en mi domicilio tengo un NAS con  OMV como sistema operativo. Este NAS lo utilizo también como destino de  los backups de diferentes dispositivos, además de para otras tareas. 

Un amigo que no dispone más que de un disco duro externo para copias de seguridad, me ha preguntado a ver que opciones tenía para hacer backups de varios directorios de su ordenador con Windows 10. Además de esto,  quería que estas copias fuesen lo más automatizadas posible. 

Hay bastantes herramientas para realizar copias de seguridad en Windows 10, incluso el sistema operativo cuenta con algunas de forma nativa.  Estas herramientas habitualmente tienen sistemas automatizados para que  se realicen cada "x" tiempo. Esto implica que, o bien mantienes el disco duro externo conectado en todo momento, o estás pendiente de conectar  el disco en el momento que la copia se va a lanzar de forma  automatizada.	 

Debido a esto, pensé en automatizar la copia de forma diferente, que se lanzara al momento de conectar el disco duro externo al equipo. La  aplicación nativa y otras que estuve mirando no disponían de esta  función, pero sí que encontré algún método para generar una tarea  autónoma con el **programador de tareas** de Windows 10  (creo que sólo se encuentra disponible en la versión Pro de Windows),  que permite programar tareas cuando se produce un evento. El evento en  cuestión, sería la conexión del disco identificado por su ID a través  del [visor de eventos](https://answers.microsoft.com/en-us/windows/forum/windows_vista-windows_programs/task-scheduler-how-to-automatically-synchronize-my/45a49d83-b1d8-4d37-8896-3d2696cf9795). 

Para que esto funcione se supone que hay que habilitar una opción en el visor de eventos, pero una vez habilitada no he conseguido que registré ningún evento relacionado con la conexión de dispositivos USB. Es por  esto que he buscado [otra solución](https://answers.microsoft.com/en-us/windows/forum/all/using-win10-backup-for-a-single-folder-to-my/1676b9b5-d513-4fbd-93c9-3804c30fa71d) que después de varias pruebas se ajusta a lo que estaba buscando. A  continuación cuales son las herramientas que he utilizado para ello.	 

### Herramienta de Backup - Synctoy

Por un lado, para los backups, que realmente lo que haré será una  sincronización en una única dirección (del pc al disco duro) he decidido utilizar la herramienta [SyncToy](https://en.wikipedia.org/wiki/SyncToy). Es una herramienta de las PowerToys que ya mencioné en [este hilo](https://www.hard2mano.com/topic/216593-powertoys-conjunto-de-herramientas-recicladas-de-windows-95/), hace mucho que no es actualizada, pero la he probado en varios equipos con Windows 10 y funciona fenomenal.

- [Enlace a la web de descarga](https://www.microsoft.com/en-us/download/details.aspx?id=15155) (Dispone de versión para sistemas de 64 o 32 bits). 

Es necesario contar con el **framework .NET** en su versión 2.0.50727 para poder realizar la instalación de esta aplicación. Para ello, [habilitaremos](https://social.microsoft.com/Forums/en-US/5cf4ff4b-96c6-4000-95e0-d5b275fd7284/unable-to-install-on-windows-10?forum=synctoy) este framework en el panel de **Características de Windows**.	 

¿Cómo habilitarlo? - Panel de control **>** Programas **>** Programas y características **>** Activar o desactivar las características de Windows (en el panel de la izquierda). Debemos **habilitar .NET Framework 3.5** (incluye .NET 2.0 y 3.0).	 

Una vez que hayamos activado el framework .NET, procederemos con la  instalación. Nos encontraremos con una pantalla inicial (ver siguiente  captura) en la que debemos seleccionar **Create New Folder Pair** para crear una nueva tarea de sincronización.	 

[![captura-synctoy-inicial.png?dl=0](https://dl.dropboxusercontent.com/s/davnk1lexpjqdhp/captura-synctoy-inicial.png?dl=0)](https://dl.dropboxusercontent.com/s/davnk1lexpjqdhp/captura-synctoy-inicial.png?dl=0)

En la siguiente ventana deberemos elegir cual será el directorio **origen** (Left folder) y cual el **destino** (Right folder).	 

![captura-synctoy-origen-destino.png?dl=0](https://dl.dropboxusercontent.com/s/bb2hizds4pwtccf/captura-synctoy-origen-destino.png?dl=0)	 

Después de seleccionar los directorios, debemos elegir que **tipo de sincronización** queremos realizar para esta tarea (cada tarea puede tener diferentes  tipos de sincronización). Los diferentes tipos que nos podemos  encontrar:

- **Synchronize**: es una sincronización total, del  dispositivo origen al destino y viceversa. Además de los archivos  nuevos, se sincronizarán también los ficheros eliminados y los  modificados. 
- **Echo**: Igual que el anterior pero sólo de origen a destino. Es decir, las modificaciones en el destino no se replican en origen. 
- **Contribute**: todo se sincroniza excepto los eliminados. Al igual que **echo**, únicamente de origen a destino. 

![captura-synctoy-tipo-sincro.png?dl=0](https://dl.dropboxusercontent.com/s/if9pwj2pw0wpmle/captura-synctoy-tipo-sincro.png?dl=0)

Por último, nos queda **asignarle un nombre** a esta tarea.

![captura-synctoy-nombre.png?dl=0](https://dl.dropboxusercontent.com/s/gvxeh72lx5zdbsz/captura-synctoy-nombre.png?dl=0)

​	 

Como resultado final podemos ver la tarea que hemos configurado. Además podemos realizar ciertas configuraciones para excluir o incluir algún  tipo de fichero específico. En el panel de la izquierda **All Folder Pairs** podremos ver los trabajos que tenemos configurado. Otra característica interesante es **Preview**, similar al parámetro **dry-run** que tienen algunas herramientas en Linux.	 

[![captura-synctoy-resultado.png?dl=0](https://dl.dropboxusercontent.com/s/wwaf2uvhcket380/captura-synctoy-resultado.png?dl=0)](https://dl.dropboxusercontent.com/s/wwaf2uvhcket380/captura-synctoy-resultado.png?dl=0)	 

*He visto otra herramienta que parece bastante interesante, que  además tiene alguna funcionalidad más pero que no la he probado, SyncToy es sencilla y hace lo que necesito. La herramienta en cuestión es [freefilesync](https://freefilesync.org)*.	 

### Gestión de eventos - Eventghost

Como comentaba al principio, he buscado otra alternativa al visor de  eventos que sea capaz de monitorizar las conexiones de dispositivos USB, la herramienta elegida ha sido [eventghost](http://www.eventghost.net). Esta herramienta permite automatizar diferentes tareas en función de diferentes sucesos, es muy potente, en su [foro](http://eventghost.net/forum/) encontraréis multitud de plugins que añaden más funcionalidades de las que ya tiene.

- [Enlace a la web de descarga](http://www.eventghost.net) (Para Windows 10 se recomienda versión **0.5 RC6**) 

* Una vez instalado el programa (tan sencillo como cualquier aplicación en Windows) nos encontraremos la siguiente pantalla:

[![Pantalla-incial-eventghost.png?dl=0](https://dl.dropboxusercontent.com/s/mfvq7v295v0eknt/Pantalla-incial-eventghost.png?dl=0)](https://dl.dropboxusercontent.com/s/mfvq7v295v0eknt/Pantalla-incial-eventghost.png?dl=0)

La aplicación se divide por varias secciones, entre ellas se encuentran los **plugins**, **macros**, **eventos**, etc., además la aplicación por defecto se inicia con el sistema y queda oculta con el resto de iconos que se encuentran en la barra de  notificaciones.	 

Veremos los pasos que se pueden seguir para conseguir la sincronización una vez que conectemos el dispositivo USB elegido como destino de los  backups.

- En primer lugar añadimos una macro que consiste en un comando de  python, encargado de extraer la información sobre el dispositivo  externo. 

[![eventghost-macro.png?dl=0](https://dl.dropboxusercontent.com/s/ehn7n016cebmcj1/eventghost-macro.png?dl=0)](https://dl.dropboxusercontent.com/s/ehn7n016cebmcj1/eventghost-macro.png?dl=0)

Una vez seleccionado el comando en python debemos introducir la siguiente línea:

```
eg.TriggerEvent(eg.event.payload[0][8:25], prefix = eg.event.suffix)
```

[![eventghost-extraer.png?dl=0](https://dl.dropboxusercontent.com/s/78qonbxqt9x3j59/eventghost-extraer.png?dl=0)](https://dl.dropboxusercontent.com/s/78qonbxqt9x3j59/eventghost-extraer.png?dl=0)

- En segundo lugar añadiremos el **evento** llamado **DeviceAttached**. Este evento corresponde a la inserción de un dispositivo a nuestro equipo. 
  [![eventghost-evento.png?dl=0](https://dl.dropboxusercontent.com/s/0g66qttfg5qctxr/eventghost-evento.png?dl=0)](https://dl.dropboxusercontent.com/s/0g66qttfg5qctxr/eventghost-evento.png?dl=0)

​	  * *Será necesario modificar el nombre del evento, añadiendo el ID correspondiente al dispositivo USB.*

- A continuación añadiremos otra macro más, en este caso se trata de una macro para iniciar una aplicación. 
  [![eventghost-iniciar-app.png?dl=0](https://dl.dropboxusercontent.com/s/u1kov53pn17wj8p/eventghost-iniciar-app.png?dl=0)](https://dl.dropboxusercontent.com/s/u1kov53pn17wj8p/eventghost-iniciar-app.png?dl=0)

- Esta macro se encargará de iniciar **SyncToy**. Una vez que lo añadamos a la pantalla inicial de eventghost podremos configurar el comando.

  - Ruta del ejecutable --> elegir **SyncToyCmd.exe**. 		

  - Opciones de la línea de comandos que pasamos --> pasamos el parámetro **-R**, que ejecutará todas las tareas guardadas en SyncToy. Si queremos pasar  una tarea específica debemos indicarle el nombre de dicha tarea. 		

    ![eventghost-evento-synctoy.png?dl=0](https://dl.dropboxusercontent.com/s/k8ybuzn13ojrrq4/eventghost-evento-synctoy.png?dl=0)

- Por último, y sin que sea necesario pero a modo informativo, he añadido otra macro más (se denomina **Run Command**) la cual se encarga de ejecutar un comando en la consola de Windows. Además del comando, añadiremos **exit** en la pestaña de **Trigger event when command is terminated**. 

```
cmd
PowerShell -Command "Add-Type -AssemblyName PresentationFramework;[System.Windows.MessageBox]::Show('La sincronización ha finalizado')"
```

![eventghost-evento-mensaje-finalizar.png?](https://dl.dropboxusercontent.com/s/7rtt7bf15uob8hc/eventghost-evento-mensaje-finalizar.png?dl=0)

​	 

- Os muestro como queda la configuración final una vez que hemos añadido todo lo que necesitamos. La parte izquierda que se muestra vacía en la  captura de pantalla, es donde se registran los diferentes eventos, donde aparece el **DeviceAttached** con su id. 
  [![eventghost-final2.png?dl=0](https://dl.dropboxusercontent.com/s/optvtmwo78diw1g/eventghost-final2.png?dl=0)](https://dl.dropboxusercontent.com/s/optvtmwo78diw1g/eventghost-final2.png?dl=0)

Tal y como se ve en la anterior captura es como lo tengo configurado,  pero tengo que realizar varias modificaciones (actualizaré cuando las  realice) para "corregir" lo siguiente (quizá sea el momento de comenzar a conocer en profundidad Powershell):

- Cuando se inicia SyncToy, [se abre un cmd](https://social.microsoft.com/Forums/en-US/596c60d9-e941-48c1-9667-2c0975fd96ac/synctoy-to-run-silently-without-command-prompt-window-visible?forum=synctoy) donde aparece la tarea de sincronización que está realizando. Estaría  bien que la ventana estuviese minimizada (modo silent) o incluso que  desapareciese. 
- El comando que lanza el mensaje de finalización no se muestra por  encima de las ventanas que se encuentren abiertas en el escritorio. 

He trasteado poco con la herramienta pero creo que se le puede sacar  mucho partido. Tiene muchísimos plugins que añaden funcionalidad extra  como por ejemplo el envío de **notificaciones** mediante **pushover**

**Y... ¿vosotros como tenéis configurados vuestros backups/sincronización en Windows?**

------

​	Algunas fuentes consultadas:

- ​		[EventGhost USB trigger - to launch apps or tasks](https://notenoughtech.com/home-automation/eventghost-usb-trigger/) 
- ​		[Customize USB Device Attached Event](http://www.eventghost.net/forum/viewtopic.php?t=2883) 
- ​		[Starting scheduled task by detecting connection of USB device](https://superuser.com/questions/219401/starting-scheduled-task-by-detecting-connection-of-usb-device) 
- ​		[Automatic Sync a USB Drive using SyncToy](https://social.microsoft.com/Forums/en-US/ecc3d462-f428-497b-aa22-1c9a87117c02/automatically-sync-a-usb-drive-using-synctoy?forum=synctoy) 