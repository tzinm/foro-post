## Fedora 30

Después de estar utilizando Linux como sistema operativo en diferentes dispositivos, he decidido incluirlo también en mi equipo principal como sistema operativo de escritorio. Para ello he elegido la distribución Fedora, concretamente la versión 30 (hay varias versiones por delante). Esta distribución es de la familia Red Hat, distribuciones que utilizan **rpm** como gestor de paquetes.

## Establecer el nombre del equipo

Para establecer el nombre del equipo se puede hacer de dos formas:

1. Línea de comandos
   
   ```
    sudo hostnamectl set-hostname nombre_del_equipo    
   ```

2. Interfaz gráfico

## Añadir Repositorios

**¿Qué son los repositorios?**

Los repositorios son espacios en los que se aloja el software que podemos descargar para nuestra distribución. Existen dos tipos de repositorios, oficiales y no oficiales. Los repositorios oficiales son lo que son mantenidos por los desarrolladores de la distribución que estemos utilizando, sin embargo los no oficiales son mantenidos por personas ajenas al desarrollo oficial de la distribución.

En mi caso, por el momento he optado por añadir dos repositorios que considero imprescindibles.

1. [RPM Fusion](https://rpmfusion.org/): habilitar estos repositorios es muy sencillo, en este [enlace](https://rpmfusion.org/Configuration) sacado de su web está explicado para diferentes versiones de Fedora.

2. [FlatHub](https://flathub.org/home): este es el [enlace](https://flatpak.org/setup/Fedora/) directo donde explican como habilitar estos repositorios en Fedora. Básicamente es instalar un fichero.

**Información adicional:**

Con un único comando podemos ver los repositorios que tenemos habilitados.

```
sudo dnf repolist
```

Aunque hay un comando que me gusta más ya que muestra todos los repositorios, tanto los que se encuentra habilitados como los que no. Además una de las columnas que muestra identifica el estado, con texto (habilitado/inhabilitado) y con el texto coloreado (verde/rojo respectivamente).

```
sudo dnf repolist all
```

## Otras instalaciones

* **Drivers Nvidia:** para la instalación de los Drivers he seguido la guía de [esta web](https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/) en la que se detalla el proceso para la instalación de estos drivers. 
  ![Nvidia Drivers](https://dl.dropboxusercontent.com/s/1eemzv2liu9r8v1/Captura%20de%20pantalla%20de%202019-06-17%2021-22-22.png?dl=0 "Nvidia Drivers")

* **Codecs:** para la instalación de los códecs debemos abrir la "tienda" de software e ir a los complementos.
  ![Códecs](https://dl.dropboxusercontent.com/s/bhgp5rd048xgjc9/Captura%20de%20pantalla%20de%202019-06-17%2018-11-25.png?dl=0 "Instalación de codecs")

* **Tipográfias de Microsoft Windows:**
  [Enlace](http://mscorefonts2.sourceforge.net/) de descarga e instalación.

* **Gnome tweaks (Retoques de Gnome en castellano):**  se instala desde la propia tienda de software. Herramienta para modificar opciones avanzadas del aspecto de gnome.
  ![Gnome Tweaks](https://dl.dropboxusercontent.com/s/bkpk5v7ekv504al/Captura%20de%20pantalla%20de%202019-06-17%2018-22-52.png?dl=0 "Gnome Tweaks")

* [**Gnome-extensions:**](https://extensions.gnome.org/) pequeñas extensiones para modificar el comportamiento de gnome. Listado de las que me han resultado interesantes:
  
  * [Dash to dock](https://extensions.gnome.org/extension/307/dash-to-dock/)
  * [Activities Configurator](https://extensions.gnome.org/extension/358/activities-configurator/)
  * [Clipboard indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/)
  * [KStatus NotifierItem/AppIndicator Support](https://extensions.gnome.org/extension/615/appindicator-support/)
  * [Gtile](https://extensions.gnome.org/extension/28/gtile/): utilizada para ajustar las ventanas en el escritorio. La utilizo para ajustar las ventanas de forma diferente en un monitor horizontal y en otro vertical.
  * [Suspend button](https://extensions.gnome.org/extension/826/suspend-button/): para añadir un botón de suspensión. Se puede suspender el sistema desde la terminal ejecutando `systemctl suspend -i`. (**Con update a Fedora 32 el botón lo han incorpordado de forma nativa**)

![Botón suspender](https://dl.dropboxusercontent.com/s/j1q634fghcte32r/Captura%20de%20pantalla%20de%202019-06-17%2018-27-14.png?dl=0 "Botón suspender")

## Personalización

Para personalizar un poco Gnome he instalado un tema tanto de iconos como del entorno de escritorio. El tema instalado ha sido [Flat Remix](https://drasite.com/flat-remix-gtk)

La instalación es muy sencilla, viene explicado de manera sencilla en el enlace que he dejado en la línea anterior.

El escritorio por el momento me ha quedado así:
![Captura de escritorio](https://dl.dropboxusercontent.com/s/3d8ui2wz0rr2r13/Captura%20de%20pantalla%20de%202019-06-17%2018-36-13.png?dl=0)

## Configuración cliente OpenVPN

Las distribuciones basadas en Red Hat incluyen un modulo de seguridad en el kernel llamado **SELinux**  ([Security Enhanced Linux](https://es.wikipedia.org/wiki/SELinux)), el cual hay que tener en cuenta para realizar ciertas configuraciones.

Una parte de nuestra configuración de Fedora trata de poner en funcionamiento el cliente OpenVPN para nuestras conexiones a servidores que se encuentran en otro lugar.

Según he podido encontrar en google ([comentario reddit](https://www.reddit.com/r/Fedora/comments/a4aooi/setting_up_an_openvpn_connection_from_f29/ebcuc05?utm_source=share&utm_medium=web2x)) hay un directorio generado explícitamente en el **$home** del usuario llamado `.pki`, el cual nos permite alojar los ficheros relacionados con el cliente VPN.

Una vez que tenemos los ficheros en el directorio debemos ejecutar el comando `restorecon -R -v ~/.pki` para que SELInux aplique el contexto de seguridad correspondiente en dicho directorio, con lo que se modificarán los ficheros convenientemente.

Una vez hecho esto podremos ver como ya nos funciona correctamente las conexiones VPN con nuestros servidores.

SELinux es muy amplio, para conocer sobre ello es recomendable echar un vistazo al [manual](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/pdf/SELinux_Users_and_Administrators_Guide/Red_Hat_Enterprise_Linux-7-SELinux_Users_and_Administrators_Guide-en-US.pdf) en inglés de Red Hat.

Más información:

* [OpenVPN and SELinux](https://www.systutorials.com/docs/linux/man/8-openvpn_selinux/)
* [OpenVPN Fedora Forums](https://forums.fedoraforum.org/showthread.php?310521-Can-t-connect-OpenVPN-client-in-Fedora-24)

## Configuración cliente SSH

Si deseamos conectarnos a nuestros servidores SSH vía clave pública (sin contraseña) debemos seguir los siguiente pasos:

1. Generar la clave pública en nuestro equipo con Fedora, para ello ejecutamos:

```
ssh-keygen
```

2. Copiar la clave en el fichero `authorized_keys` donde se encuentra el servidor SSH. **Para esto es necesario que en el servidor esté habilitado la autenticación por contraseña.**

```
cat ~/.ssh/id_rsa.pub | ssh user@hostname 'cat >> .ssh/authorized_keys'
```

## Aplicaciones

Por el momento habiendo terminado recientemente la instalación de Fedora las aplicaciones que estoy utilizando son las siguientes:

* **Firefox:** navegador principal.
* **VLC:** reproductor multimedia.
* **Remmina:** cliente RDP.
* **Filezilla:** cliente FTP.
* **Telegram:** cliente de mensajería.
* **Simplenote:** almacenamiento de notas.
* **Spotify:** reproductor de música.
* **Visual Studio Code:** editor de código.
* **VirtualBox:** herramienta de virtualización.
* **[Marktext](https://marktext.app/):** editor de texto Markdown.
* **[TimeShift](https://github.com/teejee2008/timeshift)**: gestor de copias de seguridad.

**Apps orientadas a la productividad:**

* **[Ulauncher](https://ulauncher.io/):** lanzador de aplicaciones. Tiene extensiones que le añaden funcionalidades interesantes.
* **[Autokey](https://github.com/autokey/autokey#fedora):** expansor de texto, aunque tiene más herramientas interesantes que aún no he utilizado.
* **[Flameshot](https://flameshot.js.org/#/):** capturador de pantalla.
* **[Nitroshare](https://nitroshare.net/):** aplicación multiplataforma para transferencia de archivos en red. Se encuentra disponible para **Windows 7/8/10**, **MacOS** y **Linux**.
* **[Stacer](https://oguzhaninan.github.io/Stacer-Web/)**: optimizador y monitor del sistema. Código en [GitHub](https://github.com/oguzhaninan/Stacer).

**Otras apps interesantes**:

* **[HydraPaper](https://hydrapaper.gabmus.org/):** gestor de fondos de escritorio, permite establecer dos fondos de escritorio diferentes en setups con más de un monitor.
* **[Piper](https://github.com/libratbag/piper/)**: aplicación para configurar ratones *gaming*.

## Atajos de teclado intersantes:

* **Mover ventana entre monitores:** _Super_ (tecla Windows) + _Shift_ + _flecha_ (izquierda/derecha/arriba/abajo).

* **Grabar pantalla:** _Ctrl_ + _Alt_ + _Shift_ + _R_. El tiempo de grabación está configurado de forma predeterminada para que se pare después de 30 segundos. Para modificar este tiempo es necesario introducir el siguiente comando.
  
  ```
  sudo gsettings set org.gnome.settings-daemon.plugins.media-keys max-screencast-length X
  ```
  
  _La X es el tiempo en segundos. Si no queremos que se paré de forma automática en un tiempo determinado debemos poner **0**._

* **Mostrar el Launchpad:** _Super_ + _A_.

* **Bloqueo de pantalla:** _Super_ + _L_.

Fuente: [vitux.com](https://vitux.com/how-to-use-gnome-shells-secret-screen-recorder/)

## Anotaciones de interés:

* [**Reiniciar gnome-shell**](https://askubuntu.com/questions/455301/how-to-restart-gnome-shell-after-it-became-unresponsive-freeze/496999#496999) (entorno de escritorio): presionar `Alt` + `F2`, escribir `r` y pulsar `ENTER`.

* **Entorno de escritorio:** para conocer el entorno de escritorio que estamos utilizando únicamente es necesario hacer uso de la variable de entorno **$DESKTOP_SESSION**. Podemos mostrar el valor de esta variable del siguiente modo:
  
  ```bash
  [tzinm@tzinmpc ~]$ echo $DESKTOP_SESSION 
  gnome
  ```

* **Capturas de pantalla**: en Windows utilizaba Greenshot como herramienta para realizar capturas de pantalla. En Fedora por el momento estoy utilizando la herramienta que viene por defecto con atajos de teclado, que se configuran en **Configuración > Dispositivos > Teclado > Capturas de pantalla**
  
  ![Capturas de pantalla](https://dl.dropboxusercontent.com/s/qtaycouhhtyagwy/Captura%20de%20pantalla%20de%202019-06-17%2018-05-59.png?dl=0 "Atajos Capturas de pantalla")

* **Blog con información sobre Fedora:** [Fedora Magazine](https://fedoramagazine.org/)

* **Fondos de escritorio:** https://fedoramagazine.org/installing-extra-wallpaper-packs-to-fedora-workstation/

De momento hasta aquí es todo lo que he tocado en Fedora Workstation. Aún tengo algunas cosas pendientes por configurar, algunas son sencillas y otras tengo que investigar un poco más. 

**Listado de tareas pendientes:**

* Monitor del sistema en el escritorio.
* Configurar ratón Logitech Mx Master.

## DNF - Gestor de paquetes

**DNF** es un gestor de paquetes escrito en python que utiliza **curl**. Lleva en Fedora desde la versión 18 y desde la versión 22 es el gestor oficial que sustituye a **yum**.

Dentro de las mejoras que trae este nuevo gestor de paquetes es el rendimiento en las descargas e instalación de paquetes. Veamos un resumen de los comandos más relevantes o que más uso podemos dar:

* Comprobar versión instalada:

```bash
dnf --version
```

* Mostrar la lista de comandos disponibles

```bash
dnf --help
```

* Listar paquetes instalados

```bash
dnf list installed
```

\**Haciendo uso del **pipeline** (**|**) se puede hacer una búsqueda con grep de un paquete concreto.*

* Listar paquetes disponible para su instalación

```bash
dnf list available
```

* Búsqueda de un paquete

```bash
dnf search vsftpd
```

* Instalar un paquete

```bash
sudo dnf install vsftpd
```

* Ver los detalles de un paquete

```bash
dnf info vsftpd
```

* Listar repositorios

```bash
#Listar todos los repositorios habilitados en nuestro sistema
dnf repolist

#Listar todos los repositorios (habilitados/deshabilitados)
dnf repolist all
```

* Actualizaciones del sistema

```bash
#Comprobar que paquetes pueden ser actualizados
dnf check-update

#Actualizar los paquetes
sudo dnf update
```

* Eliminar paquetes instalados

```bash
sudo dnf remove vsftpd
```

* Purgar caché

```bash
dnf clean all

dnf makecache
```

Suponiendo que nos encontramos con un usuario que no es administrador, algunos de estos comandos serán necesarios ejecutarlos con permisos de superusuario.

##### Referencias:

* [DNF - Documento de referencia](https://dnf.readthedocs.io/en/latest/index.html)

* [DNF para no iniciados](https://www.evafedora.org/2015/02/05/dnf-para-no-iniciados/)

* [DNF en CentOS](https://nksistemas.com/dnf-en-centos-explicacion-y-uso/)

## AppImage

Hemos hablado de la herramienta **dnf** como gestor de paquetes en Sistemas Operativos basados en RedHat. Cuando utilizamos los comandos que nos proporciona **dnf** para la descarga de paquetes, lo que hacemos es conectarnos a los repositorios que tenemos definidos en `/etc/yum.repos.d`.

Con **dnf** podremos descargarnos cualquier paquete que se encuentre disponible en los diferentes repositorios que tengamos configurados. La instalación es sencilla, se descarga el programa/aplicación deseado y demás dependencias que necesita para poder funcionar correctamente. 

Todo ello lo realiza sin necesidad de interacción por parte del usuario, salvo para la confirmación de las descargas que podemos evitar pasando el parámetro `-y` a **dnf**. La **principal ventaja** que puede tener la descarga de este modo es que otros programas pueden aprovechar librerías y aplicaciones que ya se encuentran en nuestro equipo.

Hace algún tiempo surgieron otras alternativas para la instalación de aplicaciones, estas alternativas pretenden empaquetar dentro de un único fichero todo lo necesario para que la aplicación funcione. El mayor inconveniente que tienen es el tamaño de los paquetes, puesto que incluyen todo lo necesario para ser independientes. Los tres formatos que disponemos son **AppImage**, **Snap** y **Flatpak**. 

En este apartado conoceremos **AppImage**, un formato de paquete que he utilizado para instalar varias aplicaciones como [MarkText](https://marktext.app/) (editor de texto Markdown).

La principal diferencia existente entre **AppImage**  y las otras dos tecnologías es que no necesitamos ninguna otra aplicación o demonio para que funcionen los programas. Lo único necesario es que el paquete se encuentre descargado en nuestro sistema y tenga **permisos de ejecución**. 

!!! note

    Si buscamos una similitud con respecto al Sistema Operativo Windows, es como si tratasemos con aplicaciones portables.

Una vez que el fichero con "extensión" **.appimage** tiene habilitado el permiso de ejecución bastará con hacer doble click en este para abrir la aplicación. Estos paquetes no se integran con el resto de forma predeterminada, con lo que si queremos abrir estos programas debemos acudir al explorador de ficheros y ejecutarlos desde su ubicación. 

Para "corregir" este comportamiento hay varias alternativas, desde herramientas que se encargan de realizar todo el trabajo de forma automática hasta ficheros que podemos crear manualmente.

### Herramientas útiles:

* **[AppImageUpdate](https://github.com/AppImage/AppImageUpdate):** esta herramienta se utiliza para la actualización de las aplicaciones empaquetadas en AppImage.
* **[AppImageLauncher](https://github.com/TheAssassin/AppImageLauncher)**: es utilizado para integrar las aplicaciones AppImage con el resto de aplicaciones del sistema. Esto incluye características como integración de la app en el escritorio, gestión de actualizaciones y eliminación de las mismas (la app y todo tipo de ficheros que han generado cuando se han integrado dentro del sistema). Para la descarga de la misma debemos acudir a [este enlace](https://github.com/TheAssassin/AppImageLauncher/releases).
* **[appimaged](https://github.com/AppImage/appimaged):** es una solución más antigua a la anterior, pero el funcionamiento es similar, trata la app como si se encontrase instalada localmente.

!!! note

    En el siguiente [enlace]([https://docs.appimage.org/user-guide/run-appimages.html#integrating-appimages-into-the-desktop) se explica el funcionamiento tanto de AppImageLauncher como de appimaged.

### Añadir ficheros manualmente

Las opciones vistas en el apartado anterior obligan a instalar más paquetes en nuestro sistema. Los ficheros [Desktop Files](https://docs.appimage.org/reference/desktop-integration.html) que se almacenan en la ruta `$HOME/.local/share/application/marktext.desktop` son los encargados de integrar estas aplicaciones de tipo AppImage en nuestro escritorio.

Para seguir con el ejemplo de MarkText, el **desktop file** correspondiente ha esta apliación ha quedado del siguiente modo ([enlace de referencia](https://github.com/marktext/marktext/blob/develop/resources/linux/marktext.desktop)).

```ini
[Desktop Entry]
Name=Mark Text
Comment=Next generation markdown editor
Exec=/home/tzinm/AppImages/marktext.AppImage %F
Terminal=false
Type=Application
Icon=/home/tzinm/AppImages/icons/marktext.png
Categories=Office;TextEditor;Utility;
MimeType=text/markdown;
Keywords=marktext;
StartupWMClass=marktext
Actions=NewWindow;

[Desktop Action NewWindow]
Name=New Window
Exec=marktext --new-window %F
Icon=marktext
```

Después de añadir este fichero debemos actualizar la base de datos para que se integre con nuestro escritorio.

```bash
update-desktop-database ~/.local/share/applications/
```

##### Referencias:

* [Documentación oficial de AppImage](https://docs.appimage.org/index.html)
* [Hilo interesante sobre los formatos de paquetes disponibles en Linux](https://www.hard2mano.com/topic/212906-instalando-aplicaciones-que-no-est%C3%A1n-en-tu-repositorio-flatpak/?tab=comments#comment-1680205): 
* [Ventajes de la instalación de paquetes AppImage]([https://geekland.eu/instalar-paquetes-appimage-ventajas/)
* [AppImageLauncher 2.0 released](https://assassinate-you.net/posts/2019/10/appimagelauncher-2.0.0-released/)
* [Issues with Fedora 30 after integrating an AppImage](https://github.com/TheAssassin/AppImageLauncher/issues/256)

## Unidades compartidas

En mi domicilio cuento con un [NAS](https://www.hard2mano.com/topic/216704-%C2%BFqu%C3%A9-se-consigue-con-un-procesador-mejor-o-m%C3%A1s-ram-en-un-nas/?do=findComment&comment=1702310) que utilizo como dispositivo de almacenamiento centralizado, para que pueda acceder a todo el contenido desde cualquier dispositivo. Para no preocuparme del montaje de las unidades compartidas cada vez que inicio sesión, configuraré Fedora para que las monte automáticamente.

En Linux disponemos de un fichero de configuración en el que podemos añadir entradas para el montaje automático de diferentes unidades, el fichero en cuestión es `/etc/fstab`.

Dejaré de ejemplo una de las líneas que he añadido al fichero **fstab**, y desglosaré los punto más relevantes de esta.

```bash
//192.168.1.10/Documentos /home/tzinm/Samba/OMV-Documentos cifs credentials=/home/tzinm/.contrasmb,rw,uid=1000,gid=100 0 0
```

![Recursos compartidos](https://dl.dropboxusercontent.com/s/97woj84afeaxajq/recursos-compartidos.png?dl=0 "Recursos compartidos")

* **Credentials**: el usuario y contraseña de acceso al NAS se puede definir en la propia línea, pero creo que es más adecuado tenerlo en un fichero externo.
  
  El fichero será de tipo oculto, con permiso de lectura y escritura para el propietario dle fichero.

```bash
-rw-------.  1 usr-tzinm usr-tzinm    40 nov  2 12:47 .contrasmb
```

- **Usuario**: he añadido el id del usuario que utilizo en el sistema y el id del grupo al que pertenece. De este modo se monta el recurso compartido con permisos de lectura y escritura (**rw**) para el usuario definido. Es posible establecer que cualquier usuario pueda montar las unidades compartidas, cambiando **uid** y **gid** por **users**.

Para montar las nuevas unidades que hemos añadido a fstab no es necesario reinicar el sistema, será suficiente con ejecutar `mount -fav`. Podremos comprobar los sistemas de ficheros que tenemos montados en el sistema con el comando `findmnt --df`.

##### Referencias:

- [Mounting a samba share in Fedora](https://gerrywilliams.net/2016/12/mounting-a-samba-share-in-fedora/)
- [Montar unidad samba con permisos de usuario](https://diario.mosqueteroweb.eu/2019/10/montar-unidad-samba-con-permisos-de.html)
- [Fstab Wiki ArchLinux](https://wiki.archlinux.org/index.php/Fstab_(Espa%C3%B1ol))

## Aplicaciones

### Syncthing

Utilizo la aplicación Syncthing para mantener sincronizados ficheros entre los diferentes dispositivos de mi hogar. Aunque se encuentra disponible en los diferentes formatos de paquetería que hemos visto más arriba, voy a instalarlo a través del gestor de paquetes **dnf** ya que según la [documentación](https://docs.syncthing.net/users/contrib.html#fedora-centos) se encuentra en los [repositorios oficiales](https://src.fedoraproject.org/rpms/syncthing) de Fedora.

```bash
sudo dnf install syncthing
```

Una vez que se encuentra instalado hay que iniciar el servicio *Syncthing* para ponerlo en funcionamiento, pero además desearemos que el servicio se inicie de forma automática. En [Linux](https://docs.syncthing.net/users/autostart.html?highlight=service#linux) disponemos de varias formas de hacerlo, en mi caso he elegido utilizar **systemd** a nivel de sistema para administrar el servicio.

```bash
sudo systemctl enable --now syncthing@tzinm.service
sudo systemctl start syncthing@tzinm.service
```

La ventaja de configurar Syncthing como un servicio del sistema es que cada vez que se inice el equipo, Syncthing se pondrá en marcha. Además, se evita el inconveniente de tener que recargar el servicio después de actualizarlo tal y como ocurre configurándolo como servicio a nivel de sesión de usuario.

En nuestro **home** se crea un directorio vacío denominado **Sync**, que puede ser modificado desde la interfaz web que podemos encontrar en la dirección **localhost:8384**. En esta dirección es donde interactuaremos con Syncthing.

Disponemos de una [extensión de GNOME Shell](https://extensions.gnome.org/extension/989/syncthing-icon/) para tener un acceso desde la barra superior del sistema.

![Syncthing GNOME extension](https://dl.dropboxusercontent.com/s/tepu1fqnc7q5ytd/Syncthing-extension.png?dl=0 "Syncthing GNOME extension")

##### Referencias

* [Documentación oficial Syncthing](https://docs.syncthing.net/)

### Ulauncher

Ulauncher es uno de los [múltiples lanzadores](https://www.atareao.es/podcast/en-busca-del-mejor-lanzador-de-aplicaciones-para-linux/) de aplicaciones que podemos encontrar para Linux. Funciona muy bien, dispone de algunas configuraciones como los atajos de teclado o el inicio automático con el sisetma.

![Preferences Ulauncher](https://dl.dropboxusercontent.com/s/p29irj0lloeea8v/ulauncher-pantalla-inicio.png?dl=0 "Preferences Ulauncher")

Los desarrolladores de Ulauncher han habilitado una [API](https://docs.ulauncher.io/en/latest/) para que otros desarrolladores puedan añadir funciones a través de diferentes extensiones que podemos encontrar en su [web](https://ext.ulauncher.io/).

#### Extensiones

Actualmente estoy utilizando dos extensiones de forma habitual, [ssh launcher](https://github.com/jetbug123/ulauncher-ssh) y [remmina](https://github.com/noam09/ulauncher-remmina).

![Ulauncher extensiones](https://dl.dropboxusercontent.com/s/udeqh3as4fd6z5b/ulauncher-extensiones.png?dl=0 "Ulauncher extensions")

* **SSH Launcher:** a partir del fichero **config** que se encuentra en el directorio **~/.ssh** podemos lanzar las conexiones ssh escribiendo en el lanzador `ssh nombre_host`. 
  
  Partiendo del siguiente contenido en el fichero config, podríamos escribir en el lanzador `ssh nas` y se abriría la terminal con la conexión ssh al NAS establecida.
  
  ```bash
  #Adding shortcut entries
  
  ## OpenMediavault ##
  Host nas
      HostName 192.168.1.25
      User tzinm
      IdentityFile ~/.ssh/id_rsa.pub
  ```
  
  *Nota:* he realizado una [pequeña modificación](https://github.com/jetbug123/ulauncher-ssh/commit/dae2cc875b90ae5d625cf177127626e1c8b9719f) a esta extensión, la cual permite asignar varios nombres a un mismo host.

* **Remmina**: esta extensión permite lanzar conexiones a los escritorios remotos que tenemos configurados en la aplicación con el [mismo nombre](https://remmina.org/). Si hemos modificado la ubicación donde se almacenan los perfiles de configuración, deberemos indicarla en los ajustes de la extensión.
  
  Para lanzar la conexión al escritorio remoto deberemos escribir en el lanzador `r nombre_conexión`.
  

##### Referencias:

* **[Configuración ssh](https://www.atareao.es/podcast/configuracion-ssh/)**
* **[OpenSSH config File Examples](https://www.cyberciti.biz/faq/create-ssh-config-file-on-linux-unix/)**
* **[How to configure custom connection options for your SSH Client](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client)**

## VirtualBox

Actualmente sigo utilizando tanto **Fedora** como **Windows** indistintamente, por lo que hay ciertos ficheros que necesito compartir entre ambas máquinas. 

#### ¿Qué es imprescindible compartir?

Por un lado las máquinas virtuales que utilizo bajo VirtualBox, y por otro lado los ficheros que necesito consutlar/editar independientemente del sistema operativo en el que me encuentre. La parte de los ficheros lo he resuelto con Syncthing, pero debido al tamaño de algunas máquinas virtuales no quiero tenerlas duplicadas en varios unidades de almacenamiento.

#### ¿Cómo lo he solucionado?

En uno de las unidades de almacenamiento de Windows he creado una partición exclusiva para almacenar todos los discos virtuales y demás ficheros que genera VirtualBox. En la máquina Linux monto la partición que he creado en Windows en el directorio `/mnt/windows`.

Al igual que con las unidades de red, he añadido en el fichero `/etc/fstab` una línea adicional para que se automonte al inicio esta partición. 

```bash
UUID=998AHWT45ZA69DE54    /mnt/windows        ntfs-3g            windows_names,rw,relatime,uid=1005,gid=112,rw,relatime    0 0
```

La partición ha sido creada inicialmente en Windows por lo que el sistema de ficheros es de tipo [NTFS](https://es.wikipedia.org/wiki/NTFS), necesario tenerlo en cuenta para establecer los parámetros correctamente.

##### Referencias:

* [NTFS-3G](https://wiki.archlinux.org/index.php/NTFS-3G_(Espa%C3%B1ol))

## Terminal

Una de las aplicaciones que más utilizo en mi equipo es el emulador de terminal. La terminal nos permite realizar una multitud de tareas, alguna de las más frecuentes está relacionada con ficheros y configuraciones del sistema. En alguna ocasión necesito abrir algún fichero con alguna aplicación externa, para poder hacerlo desde la terminal he encontrado una herramienta llamada **xdg-open**.

El funcionamiento es sencillo, simplemente ejecutamos el comando pasando el fichero que queremos abrir como parámetro. Se abrirá el fichero con la aplicación predeterminada para el tipo de fichero que sea.

```bash
xdg-open imagen.png
```

Junto a esta herramienta disponemos de otra, **xdg-mime**, que nos ofrece información sobre el manejo de los diferentes tipos de ficheros.

Si queremos conocer el tipo de fichero ejecutaremos el siguiente comando:

=== "Comando"

        ```bash

        [tzinm@tzinmpc mis-apuntes]$ xdg-mime query filetype README.md

        ```

=== "Resultado"

        ```bash

        text/markdown

        ```

Sabiendo el tipo de fichero, podemos conocer cual es la aplicación predeterminada que asociada a este.

=== "Comando"

        ```bash

        [tzinm@tzinmpc mis-apuntes]$ xdg-mime query default text/markdown

        ```

=== "Resultado"

        ```bash

        marktext.desktop

        ```

Si queremos cambiar la aplicación predeterminada para un tipo de fichero tenemos un comando que se encarga ello.

```bash
xdg-mime default aplicacion text/markdown
```

!!! note

    Para indicar la aplicación es necesario indicar el id del fichero de escritorio o el nombre de la aplicación en formato **aplicacion.desktop**.



* https://docs.fedoraproject.org/en-US/fedora/rawhide/system-administrators-guide/kernel-module-driver-configuration/Working_with_the_GRUB_2_Boot_Loader/#sec-Changing_the_Default_Boot_Entry)