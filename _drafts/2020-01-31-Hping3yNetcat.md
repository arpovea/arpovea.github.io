---
layout: post
title: Probando Dig y Wireshark
excerpt_separator: <!--more-->
author:
- Adrián Rodríguez Povea
---

***

Buenas! En esta ocasión vamos a probar un poco el comando Hping3 y  NetCat, con los cuales realizaremos pruebas en un escenario creado en una máquina virtual con Mininet. Probaremos a modificar los paquetes de ping y a conectarnos a distintos puertos y realizar varias conexiones.

***

<!--more-->

## Mininet

A continuación se expondra el escenario y los comandos que se van a utilizar en esta práctica:    

Esquema de escenario:    

`10.0.100.0/24 -> h1, h2 y r1`    
`10.0.110.0/24 -> r1 y r2`    
`10.0.120.0/24 -> r2 y r3`    
`10.0.130.0/24 -> h3, h4  y r3`    

![escenario]({{ site.baseurl }}/assets/img/Hping3yNetCat/escenario.png)

Se dejan por aquí los enlaces a los scripts tanto del escenario como del despliegue en Mininet:    

[Scrip Escenario](https://github.com/arpovea/arpovea.github.io/blob/master/assets/img/Hping3yNetCat/escenariotrasnporte2.mn)

[Scrip Despliegue](https://github.com/arpovea/arpovea.github.io/blob/master/assets/img/Hping3yNetCat/escenariotrasnporte2.py)

Una vez desplegago el escenario los comandos para configurar la red en cada máquina son:    

```bash
#H1 
ip a add 10.0.100.3/24 dev h1-eth0
ip r add default via 10.0.100.1

#H2
ip a add 10.0.100.4/24 dev h2-eth0
ip r add default via 10.0.100.2

#R1
ip a add 10.0.110.1/24 dev r1-eth0
ip a add 10.0.100.1/24 dev r1-eth1
ip a add 10.0.100.2/24 dev r1-eth2
ip r add default via 10.0.110.2

#R2
ip a add 10.0.110.2/24 dev r2-eth0
ip a add 10.0.120.1/24 dev r2-eth1
ip r add 10.0.100.0/24 via 10.0.110.1
ip r add 10.0.130.0/24 via 10.0.120.2

#R3
ip a add 10.0.120.2/24 dev r3-eth0
ip a add 10.0.130.1/24 dev r3-eth1
ip a add 10.0.130.2/24 dev r3-eth2
ip r add default via 10.0.120.1

#H3
ip a add 10.0.130.3/24 dev h3-eth0
ip r add default via 10.0.130.1

#H4
ip a add 10.0.130.4/24 dev h4-eth0
ip r add default via 10.0.130.2
```    

***

## HPing3

Ahora se va a realizar con el comando HPing3 un Echo Request desde H1 hacia H4 capturando el tráfico en R3 con Wireshark:    

Comando H1:

```bash
hping3 -K 0 -c 5 10.0.130.4
```
La opción -K indica que utilice Echo Request, este vendria por defecto pero con esto se le especifica.    
La opción -c indica el número de paquetes que se enviarán en este caso "5".     

Estas son las capturas realizadas con Wireshark en R3:    

![echorequest1]({{ site.baseurl }}/assets/img/Hping3yNetCat/capturaechorequest1.png)    
![echorequest2]({{ site.baseurl }}/assets/img/Hping3yNetCat/capturaechorequest2.png)    

A continución cambiaremos la TTL para que no sea alcanzable H4:    

Comando H1:    

```bash
hping -K 0 --ttl 3 -c 3 10.0.130.4
```

La opcion --ttl cambia por defecto la ttl de 64(por defecto) a en este caso 3.

![TTL]({{ site.baseurl }}/assets/img/Hping3yNetCat/TTL.png)

Como se puede observar en la imagen no llega a su destino.    


Ahora se intentará realizar una conexion desde el puerto 4000 al puerto 80:    
```bash
hping3 -s 4000 -p 80 10.0.130.4
```

Capturas en Wireshark:

![TCP4000-1]({{ site.baseurl }}/assets/img/Hping3yNetCat/capturaTCP4000-1.png)    
![TCP4000-2]({{ site.baseurl }}/assets/img/Hping3yNetCat/capturaTCP4000-2.png)    

Como se puede observar al no estar el puerto 80 activo se recibe la etiqueta [RST,ACK] que indica que no hay nada en ese puerto, si hubiera un servicio en ese puerto escuchando se mostraría la etiqueta [SYN,ACK].    


*** 

## NetCat



***
