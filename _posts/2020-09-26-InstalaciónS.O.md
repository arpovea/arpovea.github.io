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

1. El equipo se queda congelado en el arranque:
	En este caso concreto lo que provocaba la congelación era problema de la gráfica nvidia.
	Procedemos a iniciar el equipo con la opción `nouveau.modeset=0` esto obliga el kernel a no cargar el controlador libre de nvidia que es nouveau lo cual elimina el congelamiento ya que aun no estan instalados los drives nvidia.    
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
	Una vez reiniciado soluciono el tema gráfico del equipo.

2. Error Wifi:

***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda.Un saludo.
