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

Errores producidos dependiendo del modelo de nuestros dispositivos, en este caso se esta utilizanddo un portatil

EN CONSTRUCCION DISCULPARD LAS MOLESTIAS


***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda.Un saludo.
