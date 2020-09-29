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

## Preparación para la tarea sobre RAID5

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

Como podemos observar en el fichero agregamos 5 discos para realizar distintas pruebas a lo largo del ejercicio.

Una vez iniciada la máquina deberiamos obtener con el comando `lsblk` lo siguiente:

![lsblk]({{ site.baseurl }}/assets/img/TareaRAID5/lsblk.png)  

Lo siguiente es instalar el paquete `mdadm`, si no lo teneis ya instalado en la máquina virtual:

```bash
sudo apt update && sudo apt install -y mdadm
```

***

## Tareas RAID5

1. Tarea 1: Crea una raid llamado "md5" con los discos que hemos conectado a la máquina. ¿Cuantós discos tienes que conectar? ¿Qué diferencia existe entre el RAID 5 y el RAID1?.    

Para el RAID5 hay que conectar 3 discos, es decir tendremos en total una capacidad de 2GB para ello utilizamos el siguiente comando:

2. Tarea 2: Comprueba las características del RAID. Comprueba el estado del RAID. ¿Qué capacidad tiene el RAID que hemos creado?.    

3. Tarea 3: Crea un volumen lógico (LVM) de 500Mb en el raid 5.    

4. Tarea 4: Formatea ese volumen con un sistema de archivo `xfs`.    

5. Tarea 5: Monta el volumen en el directorio `/mnt/raid5` y crea un fichero. ¿Qué tendríamos que hacer para que este punto de montaje sea permanente?.    

6. Tarea 6: Marca un disco como estropeado. Muestra el estado del raid para comprobar que un disco falla. ¿Podemos acceder al fichero?.    

7. Tarea 7: Una vez marcado como estropeado, lo tenemos que retirar del raid.    

8. Tarea 8: Imaginemos que lo cambiamos por un nuevo disco nuevo (el dispositivo de bloque se llama igual), añádelo al array y 
comprueba como se sincroniza con el anterior.    

9. Tarea 9: Añade otro disco como reserva. Vuelve a simular el fallo de un disco y comprueba como automática se realiza la sincronización con el disco de reserva.    

10. Tarea 10: Redimensiona el volumen y el sistema de archivo de 500Mb al tamaño del raid.    

***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda. Un saludo.    
