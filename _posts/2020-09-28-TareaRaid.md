---
layout: post
title: Tareas con RAID5
excerpt_separator: <!--more-->
author:
- Adrián Rodríguez Povea
---

***

Buenas! En esta ocasión vamos a realizar un RAID5 y vamos a realizar varias pruebas. Utilizaremos RAID software en Linux con `mdadm`.

***

<!--more-->

## Tarea RAID5

Para realizar esta tarea se va a utilizar una máquina virtual con vagrant-libvirt la configuración de vagrantfile es la siguiente:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provider :libvirt do |libvirt|
    libvirt.storage :file, :size => '1G'
    libvirt.storage :file, :size => '1G'
    libvirt.storage :file, :size => '1G'
    libvirt.storage :file, :size => '1G'
    libvirt.storage :file, :size => '1G'
  end
  config.vm.box = "debian/buster64"
end
```

Como podemos observar en el fichero agregamos 5 discos para realizar distintas pruebas a lo largo de la tarea.


![lsblk]({{ site.baseurl }}/assets/img/Insta.S.O/lsblk.png)  

Listamos los dispositivos físicos agregados a LVM:

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

Procede a iniciar el equipo con la opción `nouveau.modeset=0` esto obliga el kernel a no cargar el controlador libre de nvidia que es nouveau lo cual elimina el congelamiento ya que aún no están instalados los drives nvidia. 

Para realizar esto una vez arranque grub pulsamos la tecla "e" lo cual permitirá editar las opciones de arranque por defecto y no se realiza de forma permanente, hay que escribir esta opción al final de la línea que comienza por "linux", para iniciar con estas opciones que se han editado pulsar la tecla "F10".    

Una vez se puede acceder a una TTY lo primero a realizar es activar los repositorios `non-free` para poder instalar los controladores necesarios para la tarjeta gráfica para ello edita los repositorios:    


***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda. Un saludo.
