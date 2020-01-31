---
layout: post
title: Probando Dig y Wireshark
excerpt_separator: <!--more-->
author:
- Adrián Rodríguez Povea
---

***

Buenas! En esta ocasión vamos a probar un poco el comando dig y el programa Wireshark, con los cuales realizaremos consultas DNS e investigaremos un poco el tráfico de una petición a una página web.

***

<!--more-->

## DIG    
Se va a realizar una petición DNS con el comando dig a las páginas www.marca.com y www.elmundo.es:

```bash
dig www.marca.com
```
Captura de pantalla correspondiente:    

![dig1]({{ site.baseurl }}/assets/img/DigyWireshark/digmarca.png) 

```bash
dig www.elmundo.com
```
Captura de pantalla correspondiente:    

![dig2]({{ site.baseurl }}/assets/img/DigyWireshark/digelmundo.png)     

Como se puede observar en las imágenes ambas webs estan alojadas en el servidor `151.101.133.50` con el CNAME(alias del dominio) "unidadeditorial.map.fastly.net".    

***

## Wireshark

A continuación se realiza una captura del tráfico con Wireshark accediendo al sitio www.marca.com.
En ella podemos observar lo siguiente:

La petición y respuesta DNS:

![dns1]({{ site.baseurl }}/assets/img/DigyWireshark/capturadnsmarca1.png)
![dns2]({{ site.baseurl }}/assets/img/DigyWireshark/capturadns2.png)
![dns3]({{ site.baseurl }}/assets/img/DigyWireshark/capturadns3.png)

La primera conexión TSL, puesto que es una página que tiene seguridad HTTPS, con esto el cliente le pide los certificados a la página para poder conectarse de forma segura:    

Mensajes del cliente:    

![TSL1]({{ site.baseurl }}/assets/img/DigyWireshark/clientehellow1.png)

![TSL2]({{ site.baseurl }}/assets/img/DigyWireshark/clientehellow2.png)

Mensajes del servidor:    

![TSL3]({{ site.baseurl }}/assets/img/DigyWireshark/serverhellow1.png)

![TSL4]({{ site.baseurl }}/assets/img/DigyWireshark/serverhellow2.png)