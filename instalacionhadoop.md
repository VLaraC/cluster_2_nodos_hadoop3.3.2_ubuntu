# Instalacción cluster de 3 nodos con Hadoop 3.3.2 en Ubuntu 20.04.4 LTS

## Prerrequisitos

Sistema operativo Linux Ubuntu 20.04 LTS.

## Configuración

Los siguientes requerimientos se deben realizar en cada equipo que se va a utilizar, sino se encuentran, se deben instalar:

    * Java con version 8 o superior
    * Protocolo SSH
    * Usuario Hadoop
    
Dirigiéndonos a la *terminal* realizamos los siguientes pasos:

### Instalar la versión de JAVA 

Instalar **java 8** en cada equipo como *súper usuario*.

* No olvidar antes de instalar siempre hacer este paso, para que la instalación de cualquier programa no genere problemas durante su proceso.

```shell
sudo su
apt update  
apt upgrade 
```

Después de actualizar e instalar actualizaciones, procedemos a instalar **java** con el siguiente comando:

```shell
apt install openjdk-8-jre-headless
apt install openjdk-8-jdk-headless
```

Para verificar que *java* quedó instalada, ejecute:

```shell
java -version
javac -version
```

### Cambio del *Hostname* para identificación 

El *hostname* es el nombre de cómo vamos a reconocer nuestro equipo. Para ello, vamos a crear el nombre del *maestro*:

```shell
nano /etc/hostname
```

Saldrá un archivo con una única línea, donde veremos el nombre del dispositivo. Borramos y colocamos el nombre con el que queremos reconocer el equipo. En nuestro caso que este es el principal, le colocaremos *maestro*.

```note
maestro
```
Seguido a esto, daremos *CTRL + O*, *Enter* y *CTRL + X*.

Lo mismo haremos en los otros dos equipos nombrándolos como *esclavo1* y *esclavo2* respectivamente.

### Creación de host para las ip del cluster 

Se debe saber la *ip* de cada uno de los equipos. Sino se sabe la *ip* del equipo, se ingresa:

```shell
ip r
```

El cual mostrará un mensaje como este:

![conf ip](/Imagenes/1.png)

