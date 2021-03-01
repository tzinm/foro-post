## Raspberry Pi 4 - Sustituyendo mi equipo

Sigo varios blogs de tecnología que de vez en cuando publican alguna entrada relacionada con la Raspberry Pi.

* [uglyduck.ca](https://uglyduck.ca/)
* [www.jeffgeerling.com](https://www.jeffgeerling.com/blog)

Esto me ha animado a "aparcar" mi ordenador principal en el uso de Linux (actualmente utilizo Fedora) y darle una oportunidad a la Raspberry Pi 4.

¿Qué componentes he adquirido para llevar a cabo esta prueba?

* [Raspberry Pi 8GB](https://www.raspipc.es/index.php?ver=tienda&accion=verArticulo&idProducto=1826)
* [Placa de expansión mSata](https://es.aliexpress.com/item/4000051492209.html?spm=a2g0s.9042311.0.0.274263c0Sjck1l)
* [SSD mSata 120GB](https://www.amazon.es/gp/product/B07HJ6Z9Z5/ref=ppx_yo_dt_b_asin_title_o01_s00?ie=UTF8&psc=1)
* [Carcasa de acrílico con ventilador](https://es.aliexpress.com/item/32755096717.html?spm=a2g0s.9042311.0.0.274263c0Sjck1l)
* [Disipadores](https://www.amazon.es/gp/product/B07W9JTDFZ/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&psc=1)
* [Fuente de alimentación oficial](https://www.raspipc.es/index.php?ver=tienda&accion=verArticulo&idProducto=1754)

![Raspberry Pi 4](https://dl.dropboxusercontent.com/s/nkqqddri1jhalcy/Raspberry-pi-4.jpg?dl=0 "Raspberry Pi 4")


¿Qué Software he elegido?

Hay diversas alternativas en lo que respecta a distribuciones de escritorio para Raspberry Pi. Después de valorar las opciones, he tenido en cuenta únicamente la distribución oficial desarrollada por la fundación, llamada **Raspberry Pi OS** (anteriormente conocida como Raspbian) y **Ubuntu Desktop**.

Como primera opción me he decantado por Ubuntu Desktop ya que ofrece versión de **64 bits**, "necesario" para aprovechar los 8GB de la versión de Raspberry Pi que he adquirido. La versión de escritorio disponible en 64 bits es la **20.10** (soporte únicamente durante 9 meses), que ya he instalado y he estado realizando alguna prueba con ella.

El proceso de instalación de esta distribución (el de la mayor parte de ellas) es realmente sencillo.

* Por un lado tenemos una herramienta llamada [**Raspberry Pi Imager**](https://www.raspberrypi.org/blog/raspberry-pi-imager-imaging-utility/) desarrollada por la fundación este año 2020 que nos facilita el trabajo a la hora de *quemar* la imagen en la tarjeta micro SD.
* Por otro lado **Canonical** nos proporciona una [breve guía](https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#1-overview) para poner en funcionamiento Ubuntu en nuestra Raspberry Pi. 

Dentro de esta guía de Canonical nos encontramos un punto adicional para aprovechar la funcionalidad que han añadido recientemente desde la fundación. Esta funcionalidad proporciona la opción de arrancar un Sistema Operativo desde un dispositivo de almacenamiento conectado al puerto USB. Nos brinda la posibilidad de utilizar un SSD como dispositivo principal, lo que se entiende por un mejor rendimiento general además de evitar los problemas de corrupción de las tarjetas micro SD con el paso del tiempo.

Los pasos a seguir se encuentran en este [enlace](https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#4-optional-usb-boot). Para finalizar con el proceso que se indica es necesario reiniciar la Raspberry Pi, pero antes ejecutaremos el comando `dd` para copiar el Sistema Operativo que se encuentra en la tarjeta al SSD.

````bash
dd if=/dev/mmcbk0 of=/dev/sda bs=1M
````

* **if** (input file): indicamos el fichero de origen, es decir la tarjeta micro SD.
* **of** (output file): indicamos el fichero de destino, es decir el SSD.

Para saber si merece la pena utilizar un SSD conectado vía USB en la Raspberry he hecho unas pruebas de rendimiento, tanto de la tarjeta micro SD como del SSD. El resultado de estas pruebas es el siguiente:

#### Tarjeta micro SD

Prueba de escritura:

````bash
#Comando
dd if=/dev/zero of=~/test.tmp bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 28,4288 s, 18,4MB/s
````

Prueba de lectura:

````bash
#Comando
dd if=~/test.tmp of=/dev/null bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 0,800215 s, 655MB/s
````

![Raspberry Pi prueba de rendimiento micro SD](https://dl.dropboxusercontent.com/s/qwlkq09iumcq0py/Raspberrry-pi-4-rendimiento-microSD.png?dl=0 "Raspberry Pi prueba de rendimiento tarjeta micro SD")

### Dispositivo de almacenamiento SSD

Prueba de escritura:

````bash
#Comando
dd if=/dev/zero of=~/test.tmp bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 4,87303 s, 108MB/s
````

Prueba de lectura:

````bash
#Comando
dd if=~/test.tmp of=/dev/null bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 1,00886 s, 520MB/s
````

![Raspberry Pi prueba de rendimiento SSD](https://dl.dropboxusercontent.com/s/p57b5u52qa2qmn8/Raspberrry-pi-4-rendimiento-SSD.png?dl=0 "Raspberry Pi 4 prueba de rendimiento io SSD")

El sistema en general, tanto con el SSD como con la tarjeta micro SD no se mueve de forma fluido (sobre todo con este último). A pesar de mejorar el rendimiento de escritura con el SSD sigue siendo una cifra bastante baja para conseguir un rendimiento adecuado para trabajar cómodamente con Ubuntu Desktop. 

Debido a este bajo rendimiento he decidido probar la otra alternativa, [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit) en su versión de **32 bits**. El proceso de *quemar* el Sistema Operativo en la tarjeta y la puesta en marcha es tan sencilla o más que Ubuntu.

Veamos qué rendimiento ofrecen los diferentes dispositivos de almacenamiento respecto a Ubuntu.

### Tarjeta micro SD

Prueba de escritura:

````bash
#Comando
dd if=/dev/zero of=~/test.tmp bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 24,6459 s, 21,3MB/s
````

Prueba de lectura:

````bash
#Comando
dd if=~/test.tmp of=/dev/null bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 0,919298 s, 570MB/s
````

![Raspberry Pi 4 rendimiento micro SD](https://dl.dropboxusercontent.com/s/js32xch4vzk7xl2/Raspberry-pi-4-raspios-rendimiento-microSD.png?dl=0 "Raspberry Pi 4 rendimiento micro SD")

### Dispositivo de almacenamiento SSD

Prueba de escritura:

````bash
#Comando
dd if=/dev/zero of=~/test.tmp bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 2,66282 s, 197MB/s
````

Prueba de lectura:

````bash
#Comando
dd if=~/test.tmp of=/dev/null bs=500K count=1024

#Resultado
524288000 bytes (524 MB, 500MiB) copied, 0,92517 s, 567MB/s
````

![Raspberry Pi 4 rendimiento SSD](https://dl.dropboxusercontent.com/s/njemjcvxwalhbes/Raspberry-pi-4-raspios-rendimiento-SSD.png?dl=0 "Raspberry Pi 4 rendimiento SSD")

El comportamiento del Sistema Operativo es realmente bueno, se mueve todo tremendamente fluido y apenas se sobre carga el procesador abriendo varias ventanas de aplicaciones como la terminal, el explorador de archivos y algún entorno de desarrollo ligero como Geany o BlueJ.

El rendimiento del SSD en escritura es notablemente superior (casi el doble de rápido), pero este no es el motivo por el que SO vaya tan fluido. El rendimiento también es muy bueno con la micro SD, y en las pocas pruebas que he hecho hasta ahora no se aprecia diferencia alguna.

A pesar del buen rendimiento que ofrece Raspberry Pi OS, uno de los motivos por los que probablemente no continúe con ella es por ser de 32 bits. Hay una versión de [64 bits en desarrollo](https://www.raspberrypi.org/forums/viewtopic.php?p=1668160), está en fase beta y se puede instalar pero por el momento no es tan estable cómo es esta. Depender de una versión de 32 bits implica la perdida de compatibilidad con cierto software que solo tiene soporte para arquitecturas ARM de 64 bits como por ejemplo Docker.

Como conclusión, Raspberry Pi OS es un sistema operativo con un muy buen rendimiento, pero para alcanzar ese buen rendimiento debe prescindir de ciertas características. Para mí no es suficiente como sustituto de mi actual equipo con Fedora. En cambio, Ubuntu sí que podría ser un buen sustituto, un sistema operativo completo con el buen desarrollo que hay para las versiones x86 trasladadas a la arquitectura ARM. Es una pena que el rendimiento alcanzado no ha sido suficiente como para trabajar fluidamente en la Raspberry.

## Más allá de pruebas de rendimiento

Me gustaría añadir cierta información adicional que puede resultar interesante. Desde hace algunas versiones la fundación ha desarrollado los denominados "Compute Module" derivados de las diferentes Raspberry Pi que se han ido comercializando a lo largo del tiempo. Este año tenemos el [Compute Module 4](https://www.raspberrypi.org/products/compute-module-4/?variant=raspberry-pi-cm4001000) disponible en varias versiones en función de su conectividad wifi, RAM y almacenamiento (eMMC).

Estos módulos son pequeñas placas que prescinden de puertos y que dependen de otros módulos como el   [Compute Module 4 IO Board](https://www.raspberrypi.org/products/compute-module-4-io-board/) que expone las interfaces de esta para posibilitar diferentes conexiones. Una de las más interesantes es el puerto **PCIe Gen 2 x1**. A este puerto PCIe se pueden conectar diferentes dispositivos en función de nuestras necesidades, en el apartado **"About that PCIe Slot"** de [este enlace](https://www.jeffgeerling.com/blog/2020/raspberry-pi-compute-module-4-review) hay una pequeña lista de componentes que funcionan (dispositivos de almacenamiento, tarjetas gráficas, etc.).

Para terminar, otro de los productos interesantes para este Compute Module es una placa llamada [Turing Pi 2](https://turingpi.com/). Esta placa permite poner en marcha un clúster de una forma más o menos económica. La segunda iteración (sobre la primera no hay stock) aún no está disponible a la venta, pero será un producto más que interesante para montar pequeños labs para trabajar con tecnologías como Kubernetes.

![Turing Pi 1](https://www.jeffgeerling.com/sites/default/files/images/turing-pi-cluster-board.jpeg "Turin Pi 1")

### Referencias
* [My Raspberry Pi Desktop](https://uglyduck.ca/my-raspberry-pi-desktop/)

* [Upgrade your Raspberry Pi 4 with a NVMe boot drive](https://alexellisuk.medium.com/upgrade-your-raspberry-pi-4-with-a-nvme-boot-drive-d9ab4e8aa3c2)
* [How to Boot Raspberry Pi 4 From a USB SSD or Flash Drive](https://www.tomshardware.com/how-to/boot-raspberry-pi-4-usb)