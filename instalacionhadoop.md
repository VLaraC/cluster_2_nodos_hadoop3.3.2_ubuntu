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

Instalar **java 8** en cada equipo.

* No olvidar antes de instalar siempre hacer este paso, para que la instalación de cualquier programa no genere problemas durante su proceso.

```shell
sudo su
apt update  
apt upgrade 
```

Después de actualizar e instalar actualizaciones, procedemos a instalar **java** con el siguiente comando:

```shell
  apt install default-jre 
  apt install openjdk-8-jdk -y
```

Para verificar que java quedó instalada, ejecute en la *terminal*.

```shell
java --version
javac -version
```
