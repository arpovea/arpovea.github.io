---
layout: post
title: Instalación Debian Buster con LVM
excerpt_separator: <!--more-->
author:
- Adrián Rodríguez Povea
---

***

Buenas! En esta ocasión vamos a instalar el sistema operativo Debian Buster con particiones LVM, y vamos a exponer los errores producidos y las soluciones que hemos realizado.

***

<!--more-->

## Esquema de particiones realizado en la instalación:

En este caso se tiene un  SSD de 250 GB con una instalación de windows que ocupa algo mas de 100 GB, al final disponen de unos 140 GB para la instalación del sistema Debian Buster.

Una vez se llega a la parte de particionado de la instalación del sistema Debian Buster:

Se va a crear una partición de 500MB para `/boot` aparte del sistema LVM ya que esto no interesa que cambie en un futuro, una vez realizado esto selecciona "Configurar el Gestor de Volúmenes Lógicos (LVM)" en el cual pedirá:

 - Crear un grupo de volumenes y seleccionar el disco que se va a utilizar:   

   En este caso se le llama al grupo de volumenes "Sistema" y se elige el espacio libre para que lo utilize como si fuera un disco físico, en total unos 140 GB como se ha mencionado anteriormente.

 - Luego hay que crear los volúmenes lógicos dentro de ese grupo de volúmenes:   

   Para ellos selecciona el grupo de volúmenes de "Sistema" y crea dos volúmenes lógicos, a uno se le llama "root" donde se montará el raiz y al otro "home" donde se montará /home. El sistema de fichero utilizados en estos dos volumenes es "xfs".


Una vez realizado esto procedemos con la instalación normalmente hasta finalizarla.

Una vez iniciado el sistema, con los siguientes comandos se comprueba que todo a quedado como se pretendia:

Listamos Dispositivos de bloque del sistema:

```bash
lsblk
```

![lsblk]({{ site.baseurl }}/assets/img/Insta.S.O/lsblk.png)  

Listamos los dispositivos fisicos agregados a LVM:

```bash
sudo pvdisplay
```

![pvdisplay]({{ site.baseurl }}/assets/img/Insta.S.O/pvdisplay.png)

Listamos los volúmenes lógicos:

```bash
sudo lvdisplay
```

![lvdisplay]({{ site.baseurl }}/assets/img/Insta.S.O/lvdisplay.png)

Listamos los grupos de vólumenes:

```bash
sudo vgs
```

![vgs]({{ site.baseurl }}/assets/img/Insta.S.O/vgs.png)


***


## Solución de errores:

Errores producidos dependiendo del modelo de nuestros dispositivos, en este caso se esta utilizanddo un portatil MSI [GS73 7RE Stealth Pro](https://es.msi.com/Laptop/GS73-7RE-Stealth-Pro/Specification):

### 1. El equipo se queda congelado en el arranque:

En este caso concreto lo que provocaba la congelación era problema de la gráfica nvidia.   

Procede a iniciar el equipo con la opción `nouveau.modeset=0` esto obliga el kernel a no cargar el controlador libre de nvidia que es nouveau lo cual elimina el congelamiento ya que aun no estan instalados los drives nvidia. 

Para realizar esto una vez arranque grub pulsamos la tecla "e" lo cual permitirá editar las opciones de arranque por defecto y no se realiza de forma permanente, hay que escribir esta opción al final de la linea que comienza por "linux", para iniciar con estas opciones que se han editado pulsar la tecla "F10".    

Una vez se puede acceder a una TTY lo primero a realizar es activar los repositorios `non-free` para poder instalar los controladores necesarios para la tarjeta gráfica para ello edita los repositorios:    

```bash
sudo nano /etc/apt/source.list 
```
Y agrega al final del repositorio deseado `contrib non-free`, ejemplo:

```bash
deb http://deb.debian.org/debian buster main contrib non-free
```    
Luego actualizamos la lista de paquetes:

```bash
sudo apt update
```    
Instala nvidia-detect, para detectar el paquete que utiliza tu gráfica y instala dicho paquete:

```bash
sudo apt install nvidia-detect
sudo nvidia-detect
sudo apt install nvidia-driver
```    
Una vez realizado esto reinicia el equipo si todo va bien deberia de funcionar, en nuestro caso esto no funciono, ya que la tarjeta gráfica tenia varios bugs tanto en la versión del Kernel que estaba utilizando la version de Buster instalada como en la versión del paquete "nvidia-driver".    
	
Si todo a fallado realiza la desistalación completa del driver de nvidia, de nuevo en una TTY:

```bash
sudo apt purge nvidia*
```    
Ahora se va a utilizar los backport para instalar tanto el kernel como el paquete mas actual disponible. Para ello agrega una nueva linea al `source.list`:

```bash
deb http://deb.debian.org/debian buster-backports main contrib non-free
```    
Realiza un update, comprueba versiones, y instala dichas versiones:

```bash
sudo apt update
sudo apt policy linux-image-amd64
sudo apt policy nvidia-driver
sudo apt install -t buster-backports linux-image-amd64 nvidia-driver
```    
Una vez reiniciado esto soluciono el tema gráfico del equipo.

### 2. La tarjeta wifi no funciona:

Esto suele ocurrir sobre todo con las tarjetas Realtek y las Atheros, simplemente instalando el firmware/driver de la tarjeta del equipo se soluciona, en este caso:

```bash
sudo apt install firmware-atheros
```

### 3. Problema con bluetooth y Thunderbolt

Al iniciar el equipo todo funciona correctamente, pero si realizamos una comprobación de los logs con `journalctl` en este equipo en concreto se observan errores con algunos componentes, como son el bluetooth y Thunderbolt, en principio como son dos dispositivos que no se van a utilizar vamos a desactivar el módulo del Kernel de ambos que da problemas, para ello se crea un fichero en `/etc/modprobe.d/blacklist.conf` con el siguiente contenido:

```
blacklist btusb
blacklist bluetooth
blacklist thunderbolt
```

Con esto no se cargaran estos módulos del kernel al iniciar, evitando dichos errores.

### 4. Distintos problemas que aparecen en los logs

Uno de los errores que se repetian en los logs de este equipo era con los puertos PCIe del tipo:

```
pcieport 0000:00:03.0: PCIe Bus Error: AER / Bad TLP
```

Buscando bastante información, encontre que la CPU se comunica con los controladores de bus PCIe mediante unos paquetes (TLP) y el hardware detecta cuando hay fallos y el kernel de linux informa de estos errores, con la opción `pci=nommconf` deshabilitas una configuración de los PCI llamada `Memory-Mapped PCI` que esta implementada en el Kernel linux desde el 2.6 antes de esto todos los dispositivos PCI tenian un area que describia a este dispositivo, y el metodo original para acceder a este area era con I/O mientras que con el nuevo "metodo" se simplifica este mecanismo para que sea mas eficiente, pues deshabilitando este nuevo "metodo" y volviendo a los anteriores metodos de acceso, soluciona este problema.

Esto es un caso particular, por los distintos componentes y su union, pero utilizando esta opcion del Kernel se soluciona el problema.

Para que esta opción sea definitiva vamos a editar una linea en el fichero `/etc/default/grup` quedando por ejemplo así:

```
GRUB_CMDLINE_LINUX_DEFAULT="pci=nommconf"
```

Una vez guardado realiza:

```bash
sudo update-grub
```
Y una vez se reinicie la máquina el Kernel arrancara con esa opción.

Otras opciones interesantes para corregir los errores en los logs del sistema con los PCI/PCIe son las opcion `pci=noaer` y `pci=nomsi` pero ya dependera de vuestro caso, podeis consultar la siguiente página para comprobar o buscar parametros del Kernel [aqui](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html).

***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda.Un saludo.
