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

![digwww.marca.com](assets/digmarca.png)    

```bash
dig www.elmundo.com
```
Captura de pantalla correspondiente:    

![digwww.elmundo.es]:/images/DigyWireshark/digelmundo.png    

![ddigwww.elmundo.es](assets/DigyWireshark/digelmundo.png)    

![digwww.elmundo.es]:({{ site.baseurl }}images/digelmundo.png)    

![digwww.elmundo.es]:({{ site.baseurl }}assets/digmarca.png)    

![digwww.elmundo.es]:/images/DigyWireshark/digelmundo.png    

![]:({{ site.baseurl }}images/DigyWireshark/digelmundo.png)      




Como se puede observar en las imágenes ambas webs estan alojadas en el servidor `151.101.133.50` con el CNAME(alias del dominio) "unidadeditorial.map.fastly.net".    

***

## Wireshark



