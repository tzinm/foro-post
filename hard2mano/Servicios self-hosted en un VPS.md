## Montando servicio self-hosted en VPS

Hasta el momento siempre que he querido trastear con **aplicaciones cliente/servidor** he utilizado/reaprovechado dispositivos que tengo en casa. Por un lado un Microserver HP Gen 8 —actualmente está funcionando como NAS (OpenMediaVault) y lo descarto para realizar pruebas— y por otro lado varias Raspberry Pi, con las que he podido montar un clúster de [Kubernetes](https://tzinm.me/Kubernetes/introduccion-kubernetes/).

Hasta el momento la mayor parte de los servicios que estaba utilizando los consumía en mi red local, y a los que necesitaba acceder desde fuera utilizaba una VPN. A pesar de que el rendimiento no está nada mal, no es lo mismo que una conexión "directa". Por este motivo (entre otros) he decidido contratar un [VPS](https://es.m.wikipedia.org/wiki/Servidor_virtual_privado) , que lo destinaré como servidor de aplicaciones **self-hosted** y en algunas ocasiones como **laboratorio de pruebas**.

He contratado el VPS en [Hetzner](https://www.hetzner.com/), compañía alemana con servidores localizados en Alemania y en Finlandia a precios muy competitivos. La instancia más económica asciende a [3,01€](https://www.hetzner.com/cloud) (CX11) mensuales. Las características son las siguientes:

* 1 vCPU
* 2GB RAM
* 20GB Almacenamiento
* 20TB de tráfico

### TL;DR

Para aquellos que no sepan muy bien sobre esta temática y no quieran emplear su tiempo en la lectura completa del post, les dejo un pequeño resumen de qué se podrán encontrar en este post.

Desde hace un tiempo, he decidido no solo utilizar las herramientas/servicios/aplicaciones que facilitan el trabajo u ocio que realizo diaramiente desde un dispositivo, sino que también concienciado en conocer quién gestiona esas aplicaciones, cómo lo hace y qué datos recopila. Además, también estoy en el proceso de utilizar alternativas "**open source**" como sustitución de software clásico que no lo es.

Por este motivo, quería realizar una serie de post relacionados con esta temática, que tratase de explicar qué acciones he llevado a cabo para mejorar la infraestructura tecnológica que utilizo diariamente. Aunque hay aún muchas cosas por hacer, algunos de los temas que trataré serán los siguientes:

* Gestión de cuentas de correo - disponer de un dominio personalizado y prescindir de servicios como gmail.
* Servicios auto-hospedados (**self-hosted**) - tratar de controlar la mayor parte de los datos que generamos y apoyar el [FLOSS](https://es.m.wikipedia.org/wiki/Software_libre_y_de_c%C3%B3digo_abierto).
* Servidor VPN ([Algo VPN](https://github.com/trailofbits/algo)) en un VPS - prescindir de servicios de terceros e interconectar diferentes redes de forma segura.

Este post tratará sobre el segundo de los puntos, tratará de ser una guía de iniciación sobre cómo poner en marcha un VPS y securizarlo. También aportaré algún ejemplo de servicios **self-hosted** que podemos utilizar, y lo sencillo que es su implementación una vez que tenemos todo en marcha.

Evidentemente llevar a cabo este proceso es decisión personal de cada uno, pero he de decir que además de la parte positiva que ya he comentado, si la tecnología te resulta interesante este proceso permite adquirir muchísimos conocimientos, y en el momento histórico que estamos viviendo es una forma de "matar" el tiempo.

Para terminar con esta introducción/resumen, si os interesa el tema pero no disponéis de tiempo suficiente o de conocimientos, algunas personas han puesto a disposición de los usuarios su infraestructura para poder disfrutar de algunos de estos servicios. Recordad que si utilizáis estos servicios los datos no los controlamos nosotros.

* [NixNet](https://nixnet.services/)
* [NoGAFAM](https://www.nogafam.es/)

## Configuración VPS

### Gestión de usuarios

La máquina con la que nos encontramos es una instalación limpia del sistema operativo que hayamos elegido (habitualmente Linux). Por lo tanto, solo disponemos de un usuario, que es el usuario [**root**](https://es.m.wikipedia.org/wiki/Root) (superusuario).

Los primeros pasos que daremos será establecer/cambiar la contraseña de root y crear un usuario nuevo al que introduciremos en el grupo [**sudo**](https://es.m.wikipedia.org/wiki/Sudo).

1. Cambiar password de root.
```bash
passwd
```
2. Creación de usuario (evitar uso del usuario root)
```bash
adduser usuario
```
3. Añadir el usuario al grupo sudo
```bash
usermod -gG sudo tzinm
```
### Conexión SSH

Supongo que los que habéis llegado hasta aquí conocéis esta herramienta, que nos permite acceder de forma remota a un servidor a través de una conexión cifrada.

Esta herramienta se encuentra instalada por defecto en todas las distribuciones orientadas a servidor más populares (Debian, Ubuntu o CentOS). Para hacer uso de esta herramienta debemos seguir el proceso explicado en este [enlace](https://www.cyberciti.biz/faq/how-to-set-up-ssh-keys-on-linux-unix/), pero resumiendo se trata de lo siguiente:

1. **Crear las claves** público-privadas.
2. **Almacenar la clave pública** en el fichero `~/.ssh/authorized_keys` del servidor.
3. Personalizar el fichero `sshd_config` del servidor para aumentar el nivel de seguridad.

#### Tips adicionales

Las claves público-privadas se pueden crear tanto en el servidor como en el cliente, simplemente hay que tener claras dos cuestiones:

1. El servidor debe poseer la clave pública de los clientes que quieran conectarse a este, y esta debe estar almacenada en el fichero `authorized_keys`.

2. El cliente tendrá tanto la clave pública como la privada, ambas almacenadas (se puede modificar su ubicación) en el directorio `~/.ssh`.

Si disponemos de varios clientes con los  que conectarnos al servidor, podemos configurar estos clientes de dos formas:

1. Crear para cada cliente una clave público-privada y compartir con el servidor la clave pública.
2. Compartir la clave público-privada que ya tenemos generada con todos lo clientes. A nivel de seguridad no es lo más eficiente, si una de los clientes compromete las claves el resto deberá cambiarlas.

En mi caso he implantado la segunda opción por mayor comodidad. No dispongo de muchos clientes con estas claves, con lo que si algún cliente compromete las claves no me ocasionará mucho trabajo volver a generar unas nuevas y sustituirlas por las que han sido revocadas.

En el directorio `~/.ssh` de nuestro usuario podemos añadir un fichero denominado `config` en el que podemos almacenar nuestras conexiones para poder conectarnos al servidor de forma más sencilla. En función de la configuración de ssh podemos pasar de teclear un comando *complejo* de recoradar como  `ssh -p 42112 -l miusuario -i ~/.ssh/miclavepublica.pub 114.208.145.21` a un comando tan sencillo como `ssh mivps`.

Si tomamos como referencia el comando anterior, el fichero `config` podría contener lo siguiente:

```bash
Hostname mivps
			Hostname 114.208.145.21		#Dirección IP del servidor
			User miusuario				#Usuario con el que realizamos la conexión
			Port puerto_elegido			#Especificar el puerto si hemos cambiado el de por defecto
			IdentityFile clave_pública	#Especificar la clave pública si hemos personalizado la ubicación y/o el nombre de esta.
```

### Iptables

Habitualmente se confunden los terminos Iptables y Netfilter, siendo el primero una herramienta que nos permite configurar el filtrado de paquetes del firewall del kernel de Linux, es decir, Netfilter.

En este apartado no voy a entrar en profundidad a explicar el funcionamiento de iptables, hay multituid de guías/tutoriales por la red (revisad el apartado de referencias) e incluso libros/documentos más extensos como [este](https://artica.es/docs/Guia_Netfilter.pdf) en el que se explica con más detalle el funcionamiento de iptables.

El proceso que sigo para aplicar las reglas de filtrado es el siguiente:

1. Creo un fichero (script) en el que almaceno los diferentes comandos que quiero ejecutar sobre iptables. Almacenar todas las reglas en un script me permite modificar las reglas y aplicarlas de una forma sencilla. 

   Un ejemplo de un script sencillo con comentarios podría ser el siguiente:

``` bash
#!/usr/bin/env bash

iptables -F                    #Eliminar todas las reglas de la tabla por defecto (filter).
iptables -X                    #Eliminar todas las cadenas definidas por el usuario.
iptables -Z                    #Reiniciar a cero los contadores.
iptables -t nat -F			   #Eliminar todas las reglas de la tabla nat.

#Modificar la política por defecto de la cadena INPUT.
iptables -P INPUT DROP

#Permitir los paquetes que son parte de una conexión. Para ello es necesario cargar el módulo de seguimiento de conexiones "cstate" e indicarle el estado de los paquetes que se aceptarán.
iptables -A INPUT -m conntrack --cstate ESTABLISHED,RELATED -j ACCEPT

#Permitir la conexión con localhost.
iptables -A INPUT -i lo -j ACCEPT
  
#Permitir la conexión SSH (el puerto está personalizado) y registrar dicha conexión.
iptables -A INPUT -p tcp --dport 41212 -j LOG --log-prefix 'CONEXIÓN SSH ' --log-level 4
iptables -A INPUT -p tcp --dport 41212 -j ACCEPT

#Guardar las reglas de Iptables en un fichero para poder restaurarlas posteriormente.
iptables-save > /etc/iptables/rules.v4
```

2. En el script anterior hay una lína que referencia al **log** de la conexión ssh. Los logs generados por iptables se almacenan por defecto en `/var/log/kern.log`, donde se almacenan todos los mensajes referentes al kernel de Linux. Para poder realizar un seguimiento más exahustivo podemos elegir otra ubicación donde almacenar estos logs. Si por ejemplo queremos que estos se almacenen en `/var/log/iptables.log` debemos realizar lo siguiente:

   * Añadir `kern.warning /var/log/iptables.log` al fichero `/etc/rsyslog.conf` (este fichero puede variar en función del sistema). Es necesario tener en cuenta que si solo añadimos esta línea el fichero **kern.log** seguirá almacenando los logs de iptables.
   * Reiniciar el servicio **rsyslog** ejecutando `sudo systemctl rsyslog.service`.

3. Las reglas de iptables no son persistentes, es decir, si reiniciamos el sistema las reglas aplicadas desaparecerán. Disponemos de un comando para restaurar las reglas desde un fichero, este comando es `iptables-restore`. Pare que el proceso sea automático crearemos un pequeño script que añadiremos al directorio `/etc/network/if-pre-up.d` (este directorio contiene los scripts que deben ser ejecutados antes de levantar las interfaces de red).

   ``` bash
   #!/bin/bash
   
   #Restaurar las reglas iptables, por defecto elimina todas las reglas anteriores.
   iptables-restore < /etc/iptables/rules.v4
   ```

#### Referencias

* [An in depth-guide to iptables](https://www.booleanworld.com/depth-guide-iptables-linux-firewall/)
* [Crear un log propio para iptables](https://www.linuxtotal.com.mx/index.php?cont=info__tips_008)
* [Log traffic using Iptables](https://www.youtube.com/watch?v=xHniJUuSTEE)
* [How to save IPtables rules in Debian](https://websistent.com/how-to-save-iptables-rules-in-debian/)
* [Best practices: iptables](https://major.io/2010/04/12/best-practices-iptables/)
* [How To Set Permanent DNS Nameservers in Ubuntu and Debian](https://www.tecmint.com/set-permanent-dns-nameservers-in-ubuntu-debian/)
* [Configuración básica de seguridad en un vps](https://fwhibbit.es/configuracion-basica-de-seguridad-en-un-vps)

### Docker e Iptables

El proceso que hemos visto en el apartado anterior es más o menos sencillo en función de las reglas que necesitemos implementar para mantener el sistema correctamente securizado. En este caso como la máquina contará con [Docker](https://www.docker.com/) como herramienta para gestionar contenedores, la gestión de iptables se *complica* un poco. En su [documentación oficial](https://docs.docker.com/network/iptables/) nos indican que Docker crea dos nuevas cadenas personalizadas denominadas `DOCKER-USER` y `DOCKER`.

* **DOCKER**: todas las reglas de iptables generadas por Docker se añaden a esta cadena. Esta cadena **no debe ser manipulada manualmente** por el usuario.
* **DOCKER-USER**: es una cadena que nos proporciona Docker para poder añadir reglas que serán interpretadas **antes** que las reglas de la cadena Docker. Esta es la cadena que debemos utilizar si queremos añadir reglas manualmente.

Teniendo en cuenta esta pequeña introducción, el ejemplo de script anterior y el proceso explicado, en este escenario no sería la mejor opción por varios motivos.

* El primer bloque de comandos que se encarga de **limpiar** iptables podría ocasionar algún problema en el funcionamiento de Docker.
* Para restaurar las reglas de iptables utilizamos el comando `iptables-restore`, este comando [elimina cualquier regla preexistente](https://rlworkman.net/howtos/iptables/spanish/chunkyhtml/x1223.html). Docker crea reglas dinámicas que no deberíamos eliminar si queremos evitar problemas.

* Con las cadenas **INPUT** o **OUTPUT** no securizamos los servicios de Docker (Docker expone los [puertos publicados](https://docs.docker.com/config/containers/container-networking/#published-ports) por defecto), para ello debemos utilizar la cadena **DOCKER-USER**.

En este caso, el proceso a seguír variará respecto al del apartado de iptables. Partimos de un sistema sin aplicar ninguna regla en iptables y con el servicio Docker iniciado. Como hemos dicho, Docker genera sus propias reglas, en ese momento vamos a almacenar estas reglas que ha generado para posteriormente poder restaurarlas.

```bash
iptables-save > /etc/iptables/rules.v4
```

Con el comando anterior hemos guardado las reglas que están actualmente activas en nuestro sistema —podemos ejecutar `iptables -S` para ver por pantalla qué reglas se almacenarán en el fichero—, esto nos permitirá editar manualmente el fichero para crear las reglas personalizadas que deseemos. 

Tomando como ejemplo el script del apartado anterior vamos a añadir dichas reglas al fichero `rules.v4` en el que ya tenemos las reglas de Docker almacenadas.

```bash
-F INPUT		#Eliminar todas las reglas de la cadena input de la tabla por defecto (filter).
-F DOCKER-USER	##Eliminar todas las reglas de la cadena DOCKER-USER de la tabla por defecto (filter).

#Permitir la conexión con localhost.
-A INPUT -i lo -j ACCEPT

#Permitir los paquetes que son parte de una conexión. Para ello es necesario cargar el módulo de seguimiento de conexiones "cstate" e indicarle el estado de los paquetes que se aceptarán.
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

#Permitir la conexión SSH (el puerto está personalizado) y registrar dicha conexión.
-A INPUT -p tcp --dport 41212 -j LOG --log-prefix 'CONEXIÓN SSH ' --log-level 4
-A INPUT -p tcp --dport 41212 -j ACCEPT

#Desechar todos los paquetes que no cumplan con las reglas fijadas en la cadena INPUT
-A INPUT -j DROP
```

Como se puede ver la sintáxis es prácticamente igual, únicamente prescindimos del comando `iptables`. Lo más destacable es la parte inicial donde eliminamos todas las reglas dejando únicamente las correspondientes a las cadenas **INPUT** y **DOCKER-USER**, que son las que nosotros podemos personalizar y que no afectan al funcionamiento de Docker.

Después de añadir las reglas personalizadas vamos a aplicarlas al sistema, para ello utilizaremos el comando del que ya hemos hablado (**iptables-restore**) del siguiente modo: `iptables-restore -n /etc/iptables/rules.v4`. El argumento **-n** es importante, de lo contrario se eliminarían todas las reglas que previamente estaban en iptables.

Para terminar, en el apartado anterior utilizabamos el directorio `if-pre-up.d` para almacenar el script necesario para restaurar las reglas de iptables. En este caso crearemos un **service unit** que gestionará [systemd](https://www.freedesktop.org/wiki/Software/systemd/). 

```bash
[Unit]
Description=Restaruar reglas de iptables
Before=network-pre.target
Wants=network-pre.target

[Service]
Type=oneshot
ExecStart=/sbin/iptables-restore -n /etc/iptables/rules.v4

[Install]
WantedBy=multi-user.target
```

Los  parámetros más relevantes son:

* **Before** y **Wants**: este servicio debe ejecutarse antes de que cualquier interfaz de red se inicie.
* **ExecStart**: script o comando que debe ejecutar este servicio.
* **WantedBy**: qué target debe tener en cuenta este servicio. 

#### Referencias


* [Docker meet firewall - finally an answer](https://unrouted.io/2017/08/15/docker-firewall/)
* [Secure Docker with iptables firewall and Ansible](https://ryandaniels.ca/blog/secure-docker-with-iptables-firewall-and-ansible/)
* [Manage iptables firewall for Docker/Kubernetes](https://medium.com/swlh/manage-iptables-firewall-for-docker-kubernetes-daa5870aca4d)
* [Best practices: iptables](https://major.io/2010/04/12/best-practices-iptables/)
* [How to create systemd service unit in Linux](https://linuxconfig.org/how-to-create-systemd-service-unit-in-linux)
* [Running services after the network is up](https://www.freedesktop.org/wiki/Software/systemd/NetworkTarget/)

### Fail2ban

Es una fantástica herramienta destinada a la **prevención de intrusos** (intentos de acceso por fuerza bruta) a través del análisis de los archivos logs de nuestro sistema. [Fail2Ban](https://www.fail2ban.org/wiki/index.php/Main_Page) actualiza las reglas de iptables dinámicamente atendiendo a los parámetros establecidos en sus ficheros de configuración.

Existe un [paquete](https://fail2ban.org/wiki/index.php/MANUAL_0_8#Installation) para las distribuciones más conocidas —en mi caso he instalado el paquete para Debian ejecutando `apt-get install fail2ban`— y varias imágenes para ponerlo en funcionamiento a través de [Docker](https://hub.docker.com/r/crazymax/fail2ban).

* **Fail2Ban** parte del directorio `/etc/fail2ban` del cual cuelgan diferentes directorios y ficheros de configuración.

  ``` bash
  /etc/fail2ban/
  ├── action.d
  │   ├── iptables.conf
  │   ├── mail.conf
  │   └── ...
  ├── fail2ban.conf
  ├── filter.d
  │   ├── apache-auth.conf
  │   ├── sshd.conf
  │   ├── vsftpd.conf
  │   └── ...
  ├── fail2ban.local
  ├── jail.conf
  ├── jail.d
  	├── traefik.conf
  	└── ...
  └── jail.local	
  ```

  Los ficheros con *extensión* **.conf** son generados por Fail2Ban en el momento de su instalación. Según la documentación oficial no se recomienda la modificación de estos ficheros , si queremos realizar alguna modificación deberemos crear ficheros con extensión **.local**. 

  El proceso que sigue Fail2Ban consiste en leer en primer lugar el fichero con extensión **.conf** para después pasar al fichero con extensión **.local**. Este último fichero sobreescribirá las líneas que se hayan indicado, por lo tanto únicamente es necesario incluir los parámetros del fichero **.conf** que deseamos modificar/sobreescribir.

  ¿Qué función tiene cada directorio/fichero que se encuentra bajo `/etc/fail2ban`?

  * **fail2ban.conf**: incluye las configuraciones referentes al propio servicio Fail2Ban.
  * **jail.conf**: es el fichero más relevante, donde se encuentran las configuraciones generales sobre las acciones que se deben tomar en Fail2Ban. En este fichero nos encontraremos una sección denominada **[DEFAULT]** en la que se definen estas configuraciones generales (es posible aplicar por cada servicio otros parámetros que sobreescribirán los definidos en este apartado). Algunos de estos parámetros:
    
    * **ignoreip**: direciones ip que Fail2Ban **ingorará** a pesar de que cumpla con los filtros especificados.
    * **bantime**: el tiempo de **baneo en segundos**. Un número en negativo indica baneo permanente.
    * **findtime**: es la **ventana de tiempo** durante la que se tiene cuenta el total de intentos.
    * **maxretry**: el número máximos de intentos fallidos durante **findtime**.
    * **enabled**: se utiliza para habilitar/deshabilitar los **jails**. Por defecto está deshabilitado, de ese modo habrá que habilitar cada **jail** de forma individual.
    * **chain**: es la cadena que Fail2Ban tendrá en cuenta a la hora de actualizar iptables de forma dinámica.
* **banaction**: la acción que llevará a cabo Fail2Ban. Estas acciones están definidas en el directorio `/etc/fail2ban/action.d`. La acción configurada por defecto es **iptables-multiport.conf**.
    
* **action.d**: directorio en el que se encuentran los ficheros de configuracion en el que están definidos las acciones que debe llevar a cabo Fail2Ban. Estas acciones se *disparan* en el momento que Fail2Ban encuentra en el log una coincidencia con lo definido en los filtros.
  
* **filter.d**: cuenta con diferentes ficheros que a su vez estos incluyen los filtros —en forma de expresiones regulares definidas en la variable **failregex**— que los **jails** tendrán en cuenta.
  
* **jail.d**: este directorio no tiene ningún contenido, pero podemos añadir **jails** personalizados que sobreescribirán a los que haya definidos en el fichero **jail.conf**.
  
    ```bash
    [sshd]
    enable = true					#Habilitamos el jail.
    port = 41212					
    filter = sshd					#Buscará un fichero denominado sshd.conf en el directorio filter.d.
    logapath = /var/log/auth.log
    maxretry = 3					#Este parámetro sobreescribe al definido en la sección [DEFAULT] de jail.local.
    ```

Para ver un ejemplo de un servicio sobre el que no hay definido ningún fichero de configuración nos vamos a adelantar a un apartado que se desarrollará más adelante. Este servicio es [traefik](https://traefik.io/), un **proxy inverso** que será utilizado para poder acceder desde un único punto a todos los servicios que deseemos exponer a internet.

Como este es uno de los únicos puntos de entrada a nuestra máquina (además del servicio SSH), configuraremos Fail2Ban para que añada dinámicamente reglas sobre aquellos intentos repetidos de acceso a nuestra máquina. Este servicio recibe peticiones tanto por el puerto 80 (**http**) como por el puerto 443 (**https**).

Los ficheros utilizados para esta configuración han sido los siguientes:

* **traefik.conf** (directorio `/etc/fail2ban/jail.d`)

  ``` bash
  [traefik-auth]
  enabled = true
  port = http,https
  filter = traefik-auth
  logpath = /var/log/traefik/access.log
  chain = DOCKER-USER
  maxretry = 3
  
  [traefik-botsearch]
  enabled = true
  port = http,https
  filter = traefik-botsearch
  logpath = /var/log/traefik/access.log
  chain = DOCKER-USER
  maxretry = 3
  ```

  Al igual que en el punto anterior, teniendo en cuenta que Fail2Ban debe bloquear el intento de acceso vía http/https a nuestros contenedores, deberemos personalizar los comandos para que se añadan las reglas adecuadamente y no modifiquen las reglas que inserta Docker en iptables para su correcto funcionamiento.

* **traefik-auth.conf** (directorio `/etc/fail2ban/filter.d`)

  ```bash
  [Definition]
  failregex = ^<HOST> \- \S+ \[\] \"(GET|POST|HEAD) .+\" 40[134] .+$
  ```

* **traefik-botsearch.conf** (directorio `/etc/fail2ban/filter.d`)

  ```bash
  [INCLUDES]
  before = botsearch-common.conf
  
  [Definition]
  failregex = ^<HOST> \- \S+ \[\] \"(GET|POST|HEAD) \/<block> \S+\" 404 .+$
  ```

A partir de esta configuración podemos hacer uso de varias herramientas de línea de comandos que nos proporciona Fail2Ban.

* `fail2ban-client` --> permite configurar Fail2Ban desde la línea de comandos.
* `fail2ban-regex` --> permite realizar test sobre las expresiones regulares que hemos definido.

Algunos ejemplos utilizando ambos comandos.

``` bash
$ sudo fail2ban-cliente status

Status
|- Number of jail:      3
`- Jail list:   sshd, traefik-auth, traefik-botsearch
```

```bash
$ sudo fail2ban-client status traefik-auth

Status for the jail: traefik-auth
|- Filter
|  |- Currently failed: 1
|  |- Total failed:     58
|  `- File list:        /var/log/traefik/access.log
`- Actions
   |- Currently banned: 0
   |- Total banned:     5
   `- Banned IP list:
```

```bash
$ sudo fail2ban-regex /var/log/traefik/access.log /etc/fail2ban/filter.d/traefik-auth.conf 

Running tests
=============

Use   failregex filter file : traefik-auth, basedir: /etc/fail2ban
Use         log file : /var/log/traefik/access.log
Use         encoding : UTF-8


Results
=======

Failregex: 2083 total
|-  #) [# of hits] regular expression
|   1) [2083] ^<HOST> \- \S+ \[\] \"(GET|POST|HEAD) .+\" 40[134] .+$
`-

Ignoreregex: 0 total

Date template hits:
|- [# of hits] date format
|  [19366] Day(?P<_sep>[-/])MON(?P=_sep)ExYear[ :]?24hour:Minute:Second(?:\.Microseconds)?(?: Zone offset)?
`-

Lines: 19366 lines, 0 ignored, 2083 matched, 17283 missed
[processed in 1.45 sec]

Missed line(s): too many to print.  Use --print-all-missed to print all 17283 lines
```

#### Referencias

* [Fail2Ban Manul (0.8)](https://www.fail2ban.org/wiki/index.php/MANUAL_0_8)
* [HowTo Fail2Ban spanish](https://www.fail2ban.org/wiki/index.php/HOWTO_fail2ban_spanish)
* [How Fail2Ban works to protect services on a Linux Server](https://www.digitalocean.com/community/tutorials/how-fail2ban-works-to-protect-services-on-a-linux-server)
* [List of HOWTOs](https://www.fail2ban.org/wiki/index.php/HOWTOs)
* [Python - Regular expression operations](https://docs.python.org/3/library/re.html)
* [Docker Fail2Ban](https://github.com/crazy-max/docker-fail2ban#notes)

### Segunda parte

Esta segunda parte será menos densa, donde veremos algunos de los servicios que podemos instalar de forma sencilla para prescindir de aquellos servicios que no alojamos nosotros y por tanto no tenemos el control de los datos.

Lo primero que haremos será disponer de un nombre de dominio para poder acceder desde fuera evitando utilizar la dirección ip pública que nos ha proporcionado nuestro proveedor de VPS. De momento los servicios que voy a utilizar son para consumo propio, con lo que el nombre de dominio no toma relevancia.

Después de estudiar varios servicios de [dns dinámicos](https://tzinm.me/Asus/Custom-DDNS/#dinamyc-dns), he decidido utilizar [nsupdate.info](https://www.nsupdate.info/), servicio gratuito y de código abierto. En principio la dirección ip pública que me ha proporcionado el VPS es fija, con lo que no necesitaría ningún cliente que actualizase la dirección en caso de que esta cambiase. Basta con configurar la ip en la web de nsupdate para que apunte a nuestra ip pública. Aún así, os cuento las opciones que hay por si decidís contratar otro VPS que no os proporcione una ip fija.

Para este último recurso he utilizado un servicio archiconocido como [inadyn](https://hub.docker.com/r/troglobit/inadyn). Este servicio se encuentra disponible tanto para instalar *manualmente* en Debian como bajo Docker. En mi caso lo he instalado como servicio, siguiendo los pasos que aparecen [aquí](https://github.com/troglobit/inadyn#debianubuntu). Para que funcione de forma autónoma lo he integrado en [systemd](https://github.com/troglobit/inadyn#debianubuntu).

#### Servicios instalados

###### Lector de feeds

Supongo que la mayoría sabréis para que sirve un lector RSS o lector de feeds, si no, os dejo [este enlace](https://es.m.wikipedia.org/wiki/RSS) para que echéis un vistazo.

Algunas de las alternativas que tenemos:

*  [FreshRSS](https://www.freshrss.org/) - he escogido este servicio. De los diferentes que he probado es el que mejor funciona con la aplicación de móvil [Reeder](https://www.reederapp.com/) que utilizo como lector de feeds..

* [Miniflux](https://miniflux.app/) - esta es muy interesante por el diseño minimalista que tiene a la hora de consumir contenido desde un navegador. 
* [TinyTinyRSS](https://tt-rss.org/) - es un servicio muy conocido, aunque personalmente me parece mejor servicio FreshRSS. Enlaces interesantes sobre este servicio: [Self-Hosted RSS with Tiny Tiny RSS in Docker](https://webworxshop.com/self-hosted-rss-with-tiny-tiny-rss-in-docker/) y [Awesome-TTRSS](https://github.com/HenryQW/Awesome-TTRSS/blob/master/docker-compose.yml)

Para aprovechar al máximo FreshRSS he añadido una extensión a Firefox, [FreshRSS Notify](https://github.com/Purexo/FreshRSS-Notify), aunque actualmente tiene un [pequeño fallo](https://github.com/Purexo/FreshRSS-Notify/issues/19) que no permite deshabilitar las notificaciones sobre los artículos no leídos.

###### Almacén de artículos

Entiendo como *almacén de artículos* a servicios similares a [Pocket](https://getpocket.com/). En este caso la única alternativa interesante que he encontrado ha sido [Wallabag](https://wallabag.org/en), que dispone de versión autohospedada además de otra versión de pago gestionada por sus desarrolladores.

Al igual que FreshRSS, dispone de una extensión que facilita su uso a través de un navegador. La extensión en cuestión es [Wallabager](https://github.com/wallabag/wallabagger).

En lo que respecta a aplicación móvil, tenemos opción de utilizar la *oficial* (no me ha gustado demasiado, al menos en iOS) o aplicaciones de terceros que dispongan opción de conectarse a Wallabag. La aplicación que utilizo es [Fiery Feeds](https://apps.apple.com/us/app/fiery-feeds-rss-reader/id1158763303) que a pesar de tener versión de pago la versión gratuita es más que suficiente.

###### Otros servicios pendientes por instalar

Por el momento son los principales servicios que estoy utilizando (el consumo de recursos del VPS es bastante bajo) aunque hay algunos otros que pronto los dejaré funcionando.

* **Acortadores de URL**
  * [Lstu](https://framagit.org/fiat-tux/hat-softwares/lstu/blob/master/README.md) -> dispone de [extensión](https://addons.mozilla.org/fr/firefox/addon/frama-link-shortener/).
  * [Polr](https://www.polrproject.org/)

* **Listas to-do**
  * [Vikunja](https://vikunja.io/features/) -> después de echar un vistazo a esta [lista de referencia](https://github.com/awesome-selfhosted/awesome-selfhosted#task-managementto-do-lists) me ha parecido la más completa.
* **Herramientas de sincronización**
  * [xBroserSync](https://github.com/xbrowsersync/api) -> herramienta para sincronizar datos entre diferentes navegadores.

Los servicios que he mencionado son una ínfima parte de la infinidad de ellos que hay disponibles para autohospedar en un VPS. Hay que tener en cuenta que hay servicios que tienen un consumo mayor de recursos y es posible que una instancia como la que he contratado no sea suficiente. Dejo un par de enlaces para curiosear sobre este tipo de software:

* [r/selfhosted](https://www.reddit.com/r/selfhosted/)
* [Awesome-Selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted#)

#### Proxy inverso

Los servicios ya mencionados (incluido el proxy inverso, y probablemente futuros servicios) están implementados en Docker. Es cierto que no he comentado nada sobre la instalación y/o configuración de Docker, para ello os recomiendo que vayáis a [este enlace](https://tzinm.me/Docker/introduccion-docker/). En él encontraréis una guía bastante amplia con los conceptos esenciales.

Hay disponibles diferentes proxy para trabajar con Docker, algunos más complejos de configurar que otros. En mi caso he elegido [traefik](https://traefik.io/), quizá no es el más sencillo pero una vez se comprende su funcionamiento es fácil implementar nuevos servicios detrás de este proxy.

No voy a entrar en detalle en la configuración, pero sí que voy a dejar dos artículos que me parecen esenciales para comprender como funciona traefik (v2).

* [A comprehensive introduction to traefik v2 with Docker](https://offby2.com/posts/002-introduction-to-traefik-v2-with-docker/)
* [Traefik 2.0 & Docker 101](https://traefik.io/blog/traefik-2-0-docker-101-fc2893944b9d/)

Una vez comprendido el funcionamiento básico, hay dos cuestiones relevantes a destacar:

* Configurar adecuadamente el acceso al dashboard de traefik. Modificar la configuración de la api de Traefik, del modo *insecure* al modo *[secure](https://doc.traefik.io/traefik/operations/dashboard/#secure-mode)*.

* Configuración de los **logs** de traefik. Ya no solo es importante para saber qué es lo que ocurre sin necesidad de acudir a Docker (`docker logs`), sino que será utilizado por parte de Fail2Ban para actualizar las reglas de iptables.

  * Es necesario indicar en el fichero de configuración de traefik dónde se almacenará (dentro del contenedor) el fichero **log**.

    ```dockerfile
    accessLog:
    	filePath: "/var/log/access.log"
    ```

  * En el fichero Dockerfile si queremos acceder desde fuera del contenedor es necesario realizar un **[bind mount](https://docs.docker.com/storage/bind-mounts/)**.

##### Referencias

* https://fmlab.no/
* [Documentación oficial](https://doc.traefik.io/traefik/v2.0/)

* [Ultimate Docker Home Server with Traefik 2, LE, and OAuth / Authelia [2020]](https://www.smarthomebeginner.com/traefik-2-docker-tutorial/)
