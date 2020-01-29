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

![dig1](/_images/DigyWireshark/digmarca.png)   

```bash
dig www.elmundo.com
```
Captura de pantalla correspondiente:    

![dig2](/_images/DigyWireshark/digelmundo.png)       

Como se puede observar en las imágenes ambas webs estan alojadas en el servidor `151.101.133.50` con el CNAME(alias del dominio) "unidadeditorial.map.fastly.net".    

***

## Wireshark

Prueba
