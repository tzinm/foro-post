## [TIP] Cargar versión móvil automáticamente

Estaba revisando (desde el ordenador) un par de enlaces que tenía  guardados para leer cuando tuviese algo de tiempo. Uno de estos enlaces  era de la Wikipedia, lo he abierto y me he percatado que el formato de  visualización había cambiado. He pensado que tenía que ver con [alguna noticia](https://www.genbeta.com/web/wikipedia-va-a-cambiar-su-diseno-primera-vez-10-anos) que leí hace algún tiempo sobre el cambio de diseño que se iba a producir.

Aún sabiendo lo de esta noticia, me resultaba raro porque no se  mencionaba nada sobre un cambio en el formato de visualización (se puede apreciar en dos imágenes comparativas que dejo más abajo). Le doy  alguna vuelta más al asunto y se me ocurre revisar la url (ya ves, que  tonetería xD), es cuando me doy cuenta que lo que estoy visualizando es  la versión móvil de la wikipedia (el enlace lo había guardado desde el  móvil).

Diseño clásico ([https://es.wikipedia.org/wiki/Random)](https://es.wikipedia.org/wiki/Random):

[![wikipedia-clasico.png?dl=0](https://dl.dropboxusercontent.com/s/z0vxegfv68562az/wikipedia-clasico.png?dl=0)](https://dl.dropboxusercontent.com/s/z0vxegfv68562az/wikipedia-clasico.png?dl=0)

Diseño "moderno" ([https://es.m.wikipedia.org/wiki/Random)](https://es.m.wikipedia.org/wiki/Random), como se puede apreciar en la imagen el formato es similar al del foro,  el texto se centra para que la lectura sea más sencilla:	 	[![wikipedia-moderno.png?dl=0](https://dl.dropboxusercontent.com/s/prqjdbsojnmj9vx/wikipedia-moderno.png?dl=0)](https://dl.dropboxusercontent.com/s/prqjdbsojnmj9vx/wikipedia-moderno.png?dl=0)

Después de disfrutar de la comodidad que proporciona leer la versión  móvil de la wikipedia en el pc, me ha surgido la necesidad de buscar la  forma de automatizar el proceso de abrir cualquier enlace de la  wikipedia en fomato móvil.

Lo primero que se me ha ocurrido es buscar algún método que incluya  Firefox (el navegador que utilizo) o alguna extensión (esto me hacía  menos gracia) que sea capaz de modificar la url, ya que la modificación  necesaria siempre sería la misma, añadir el "subdominio" que hace  referencia a la versión móvil. Es entonces cuando me ha venido a la  cabeza que tenía una extensión para cambiar el user-agent del navegador, extensión que utilizaba apara algunas webs que necesitaba cargar en  versión móvil. He pensado que esta extensión debería tener la forma de  aplicar un user-agent a un dominio en concreto, he revisado la  documentación y así es.

Esta extensión dispone de tres modos para cambiar el user-agent a dominios que nosotros deseemos.

- Black-list mode 
- White-list mode 
- Custom mode 

Los que nos van a interesar son los dos primeros, que son los más  rápidos y sencillos de configurar. La diferencia entre ambos radica en  que en el modo black-list se aplica el user-agent definido en la  extensión a todos los dominios exceptuando a los que se encuentren en la lsita, y en el modo white-list es al revés, se aplica el user-agent  únicamente a los dominios que se encuentran definidos en la lista. Con  lo cual, para que wikipedia cargue en versión móvil únicamente es  necesario añadir **wikipedia.org** a la white-list.	 

![wikipedia-user-agent.png?dl=0](https://dl.dropboxusercontent.com/s/3gn4c0hpnv1qi32/wikipedia-user-agent.png?dl=0)

Sí amigos, tanto rollo para una costa tan simple xD. Eso sí, ahora  siempre que abro un enlace de Wikipedia se carga la versión móvil  automáticamente.