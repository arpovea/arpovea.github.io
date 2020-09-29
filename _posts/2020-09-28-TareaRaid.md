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

Una vez iniciada la máquina deberiamos obtener con el comando `lsblk` lo siguiente:

![lsblk]({{ site.baseurl }}/assets/img/TareaRAID5/lsblk.png)  

Lo siguiente es instalar el paquete mdadm si no lo teneis ya instalado en la máquina virtual:

```bash
sudo apt update && sudo apt install -y mdadm
```

***
  
  
***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda. Un saludo.
