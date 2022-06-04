---
title: "HackTheBox Lame Writeup"
layout: post
--- 



Como dice en el titulo hoy haremos la maquina Lame de HackTheBox.Una maquina Linux de dificultad facil


### Enumeracion 


Lo primero que haremos sera un escaneo de puertos con la herramienta **Nmap** en la ip de la maquina.Esto nos mostrara los puertos abiertos 

```
 nmap -p- --open --min-rate 5000 -Pn -n -v IP -oG puertos

```
Explicacion del comando:

* -p-:Escanea todos los puertos
* --open:Busca puertos abiertos
* --min-rate 5000:Envia como minimo 5000 paquetes por segundo,tambien se puede utilizar -T5
* -Pn:Trata a los hosts como online,asi saltea host discovery
* -n:No hacer resolucion de DNS
* -v:Nos va mostrando los resultados mientras escanea
* IP:La ip de Lame
* -oG:Para guardar el escaneo en archivo grep.

---


