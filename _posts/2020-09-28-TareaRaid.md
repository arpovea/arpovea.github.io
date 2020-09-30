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

```bash
sudo mdadm -C /dev/md5 --level=raid5 --raid-devices=3 /dev/vdb /dev/vdc /dev/vdd
```
Si realizas de nuevo un `lsblk` obtendras algo como esto:

![lsblk2]({{ site.baseurl }}/assets/img/TareaRAID5/lsblk2.png)

La diferencia entre RAID5 y RAID1 es:

2. Tarea 2: Comprueba las características del RAID. Comprueba el estado del RAID. ¿Qué capacidad tiene el RAID que hemos creado?.  
La capacidad es de 2GB, para comprobar el estado del RAID5 se consulta un fichero y para los detalles un comando: 

- Consultando el fichero `/etc/proc/mdstat` para ver su estado:

```bash
sudo cat /proc/mdstat
```

- Consultando los detalles:

```bash
sudo mdadm -D /dev/md5
```

En la siguiente imagen se pueden ver tanto el estado como los detalles:

![mdadmstatus]({{ site.baseurl }}/assets/img/TareaRAID5/comprobacionstadoraid.png)

3. Tarea 3: Crea un volumen lógico (LVM) de 500MB en el raid5.    

Para esto hay que añadir el RAID5 a los volumenes físicos, crear un grupo de volúmenes que utilize ese dispositivo y luego crear el volumen lógico:

```bash
sudo pvcreate /dev/md5
sudo vgcreate tareas /dev/md5
sudo lvcreate tareas -L 500M -n tarea3
```

Quedando como en la siguiente imagen:

![Vlogico]({{ site.baseurl }}/assets/img/TareaRAID5/Vlogico.png)

4. Tarea 4: Formatea ese volumen con un sistema de archivo `xfs`.    

Realiza el siguiente comando:

```bash
sudo mkfs.xfs /dev/tareas/tarea3
```

5. Tarea 5: Monta el volumen en el directorio `/mnt/raid5` y crea un fichero. ¿Qué tendríamos que hacer para que este punto de montaje sea permanente?.

Para ello:

```bash
mkdir /mnt/raid5
sudo mount -t xfs /dev/tareas/tarea3 /mnt/raid5
touch /mnt/raid5/fich.txt
```

Para que fuera permanente tendrias que incluir este montaje en el fichero `fstab`.    

6. Tarea 6: Marca un disco como estropeado. Muestra el estado del raid para comprobar que un disco falla. ¿Podemos acceder al fichero?.    

Para marcar un disco por ejemplo `vdb` como fallido utiliza:

```bash
sudo mdadm --manage /dev/md5 --fail /dev/vdb
```

Luego mira el estado con:

```bash
cat /proc/mdstat
```

Mostrará algo similar a la siguiente imagen donde se observa que el disco esta marcado como fallido:

![fallovdb]({{ site.baseurl }}/assets/img/TareaRAID5/fallovdb.png)

El fichero se puede seguir visualizando sin problema.

7. Tarea 7: Una vez marcado como estropeado, lo tenemos que retirar del raid.   

Para ello utiliza el siguiente comando:

```bash
sudo mdadm --manage /dev/md5 --remove /dev/vdb
``` 

Observamos de nuevo el estado del raid5 y mostrará lo siguiente:

```bash
cat /etc/proc/mdstat
```

![removevdb]({{ site.baseurl }}/assets/img/TareaRAID5/removevdb.png)


8. Tarea 8: Imaginemos que lo cambiamos por un nuevo disco nuevo (el dispositivo de bloque se llama igual), añádelo al array y 
comprueba como se sincroniza con el anterior. 

Para ello en este caso con un nuevo disco "vde":

```bash
sudo mdadm --manage /dev/md5 --add /dev/vde
```

Automáticamente se sincroniza con el raid, observa los cambios con:

```bash
lsblk
cat /proc/mdstat
```

Obteniendo algo similar a lo siguiente:

![agregandovde]({{ site.baseurl }}/assets/img/TareaRAID5/agregandovde.png)


9. Tarea 9: Añade otro disco como reserva. Vuelve a simular el fallo de un disco y comprueba como automática se realiza la sincronización con el disco de reserva.

Realiza los siguiente comandos para añadir el disco como reserva:

```bash
mdadm --manage /dev/md5 -add /dev/vdf
```

Puedes comprobar que se ha añadido como "SPARE" con comprobando el estado o los detalles del raid5 con los siguientes comandos:

```bash
sudo mdadm -D /dev/md5
```
O
```bash
cat /proc/mdstat
```

Ahora vuelve a marcar otro dispositivo como fallido esta vez `vdc`:

```bash
sudo mdadm --manage /dev/md5 --fail /dev/vdc
```

Como podemos ver en la siguiente imagen el disco pasa de SPARE (S) a activo y el otro se marca como FAIL (F)

![SPAREautilizado]({{ site.baseurl }}/assets/img/TareaRAID5/SPAREautilizado.png)

Como repunte, si quisieras que el disco se agregara como dispositivo adicional para aumentar las dimesiones del raid, después de agregarlo, realiza el siguiente comando:

```bash
sudo mdadm --grow /dev/md5 --raid-devices=4
```

Y luego aumentar el raid a la máxima capacidad disponible con:

```bash
sudo mdadm --grow /dev/md5 -z max
```

10. Tarea 10: Redimensiona el volumen y el sistema de archivo de 500Mb al tamaño del raid. 

Con los siguientes comandos podras redimensionar el volumen y luego redimensionar el sistema de ficheros:



***
    
Muchas gracias por leer hasta aquí espero que haya servido de ayuda. Un saludo.    
