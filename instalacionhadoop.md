# Instalacción cluster de 2 nodos con Hadoop 3.3.2 en Ubuntu 20.04.4 LTS

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
### Instalar protocolo SSH

El protocolo **SSH**, nos ayudará a conectarnos entre equipos, para el intercambio de archivos. Ejecute la siguiente línea para instalar el 
protocolo:
    
```shell
apt install openssh-server
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

Se debe saber la *ip* de cada uno de los equipos. Sino se sabe la *ip* del equipo, se ingresa sin salir del súper usuario:

```shell
ip r
```

El cual mostrará un mensaje como este:

![conf ip](/Imagenes/1.png)

En donde la *ip* será la encerrada en el círculo rojo. En cada equipo puede correr este comando para conocer la *ip*.

La creación del *host* es opcional. Este proceso es para identificar mucho más fácíl cuál es el equipo, pero sino se realiza, se deberá poner la *ip* de cada equipo en vez de los nombres de dirección realizados a continuacion:

```shell
nano /etc/hosts
```

Nos abrirá un documento donde ya habrán unas direcciones como la *localhost*. Después de estas direcciones, agregamos las direcciones *ip* de los equipos y sus respectivos nombres para identificar:

```note
192.0.0.1       localhost
10.0.2.8        maestro
10.0.2.9        esclavo1
10.0.2.15       esclavo2
```
Seguido a esto, daremos *CTRL + O*, *Enter* y *CTRL + X*.

** Este proceso se hace en cada equipo.

### Descargar y extraer Hadoop

Se debe descargar y extraer el **Apache Hadoop 3.3.2** en cada equipo. Este se descargará por medio del *terminal* desde el sitio web https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.2/hadoop-3.3.2.tar.gz, el cual es la descarga binaria copiando el primer link. Luego se procederá a realizar la descarga.

* No olvidar antes de instalar siempre hacer el siguiente paso para que la instalación de cualquier programa no genere problemas durante su proceso.

```shell
apt update
apt upgrade 
```

Ahora, la descarga del **Hadoop 3.3.2** se realiza corriendo la siguiente línea:

```shell
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.2/hadoop-3.3.2.tar.gz  
```
Al finalizar la descarga se extrae con la siguiente línea:

```shell
tar -xzvf hadoop-3.3.2.tar.gz   
```

Ahora movemos la carpeta *hadoop-3.3.2* al directorio **/usr/local/hadoop/** con el siguiente comando en los 3 equipos:

```shell
mv hadoop-3.3.2 /usr/local/hadoop/  
```

### Editar el **PATH** y el **JAVA_HOME**

Este procedimiento igualmente se hace en cada equipo. Primero debemos saber dónde se encuentra instalado *java*. Para ello, corremos la siguiente línea:

```shell
update-alternatives --display java
```

Lo que arrojará la siguiente salida:

![conf PATH](/Imagenes/2.png)

De la cual tomaremos el directorio especificado con rojo. A continuación abrimos **/etc/environment** y modificamos el **PATH** con el siguiente comando:

```shell
nano /etc/environment
```

Completaremos el **PATH** con el siguiente código al final de la línea pero dentro de las comillas:

```shell
:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
```

Se define en otra línea la variable **JAVA_HOME** con la dirección obtenida antes:

```shell
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

Guardamos con *CTRL + O*, luego *Enter* y cerramos con *CRTL + X*. 

### Creación de usuario Hadoop y clave de acceso

El *Usuario Hadoop* es el espacio donde se realizará la instalación del *Hadoop* y cada equipo lo debe tener configurado. Para ello, realizamos las siguientes líneas en el *terminal* de cada equipo:

```shell
adduser hadoop
```
En este proceso solo se le dará la *clave* y el *nombre* de usuario que se quiere en cada equipo, a todo lo demás, la tecla **Enter**.

Para darle privilegios de administrador se correrán los siguientes comandos:

```shell
usermod -aG hadoop hadoop
chown hadoop:root -R /usr/local/hadoop
chmod g+rwx -R /usr/local/hadoop
```

### Generación y envío de llave pública

Se entra al *Usuario Hadoop* creado para generar la *llave pública*. Esto con el fin de poder comunicar el *cluster* entre equipos, sin necesidad de solicitar clave cada vez que se conecta. Se utiliza el siguiente código en el *terminal*:

```shell
su hadoop
cd
```

Se ingresa la *clave* del *Usuario Hadoop* configurada anteriormente en el equipo respectivo. Se le pedirá que la confirme y dará **Enter**. Luego de esto, para generar la *llave pública* se corre la siguiente línea:

```shell
ssh-keygen -t rsa
```

Este pedirá especificar dónde se deberá guardar la *llave pública*. Ella trae un directorio por defecto, por tanto, no se modificará. También pedirá un **passphrase** el cual se dejará vacio solo con presionar **Enter** hasta que genere la *llave pública*.

Si estamos realizando el proceso de instalación simultaneamente, podemos enviar las llaves para interconectar los equipos del *cluster* con permisos de la siguiente manera solo en el equipo **maestro**:

```shell
ssh-copy-id hadoop@maestro
ssh-copy-id hadoop@esclavo1
ssh-copy-id hadoop@esclavo2
```
### Configuración del Hadoop Master

#### core-site.xml

Se debe abrir el archivo *core-site.xml* solo en el equipo **maestro** con la siguiente línea:

```shell
nano /usr/local/hadoop/etc/hadoop/core-site.xml
```

Al abrir, nos dirigimos a la parte de configuración y ponemos el siguiente código:

```shell
<configuration>
        <property>
                <name>fs.default.name</name>
                <value>hdfs://maestro:9000</value>
        </property>
</configuration>
```

Guardamos con *CTRL + O*, luego *Enter* y cerramos con *CRTL + X*.

#### hdfs-site.xml

Se debe abrir el archivo *hdfs-site.xml* solo en el equipo **maestro** con la siguiente línea:

```shell
nano /usr/local/hadoop/etc/hadoop/hdfs-site.xml
```

Al abrir, nos dirigimos a la parte de configuración y ponemos el siguiente código:

```shell
<configuration>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/usr/local/hadoop/data/nameNode</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/usr/local/hadoop/data/dataNode</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>2</value>
        </property>
</configuration>
```

Guardamos con *CTRL + O*, luego *Enter* y cerramos con *CRTL + X*.

#### workers

Se debe abrir el archivo *workers* solo en el equipo **maestro** con la siguiente línea:

```shell
nano /usr/local/hadoop/etc/hadoop/workers
```

Al abrir, nos muestra una línea con el nombre *localhost*. Lo borramos y escribimos los nombres de los equipos que trabajarán. Estos serán **esclavo1** y **esclavo2**.

```shell
esclavo1
esclavo2
```

Guardamos con *CTRL + O*, luego *Enter* y cerramos con *CRTL + X*.

#### Copia de archivos de configuración a los esclavos.

Ahora procederemos a copiar los archivos de configuración anteriormente editados y los enviaremos a los nodos del Hadoop Master, los cuales son **esclavo1** y **esclavo2**. Esto se hará corriendo las siguientes líneas:

```shell
scp /usr/local/hadoop/etc/hadoop/* esclavo1:/usr/local/hadoop/etc/hadoop/
scp /usr/local/hadoop/etc/hadoop/* esclavo2:/usr/local/hadoop/etc/hadoop/
```

#### Formatear los archivos de sistema HDFS en el equipo *maestro*

Para esto, debemos correr las siguientes líneas de código:

```shell
source /etc/environmnet
hdfs namenode -format
```
Esperamos a que finalice. 

#### Iniciar los archivos de sistema **HDFS**

Ya con estas configuraciones podemos iniciar los archivos de sistema **HDFS** con el siguiente código:

```shell
start-dfs.sh
```

Al terminar, validamos en la *terminal* de cada uno de los dos esclavos que corrió correctamente con el siguiente código: 

```shell
jps
```

Al correr la línea, mostrará dos datos: el código de *Jps* y el código del *DataNode*.

#### Acceder a la interfaz de usuario web de *HDFS*

Ahora podemos acceder a la interfaz de usuario web de *HDFS* navegando hasta el puerto 9870 de su servidor *maestro* de Hadoop. En él se puede verificar el estado de la capacidad configurada y otros items importantes. A este se accede entrando a su navegador de internet desde el equipo **maestro** a la dirección **https//10.0.2.8:9870**. En este caso, antes de los dos puntos va la dirección ip del equipo **maestro** o el *hostname* que le haya puesto.


![HDFS](/Imagenes/3.png)



#### Iniciar **Yarn** 

Ahora que **HDFS** se está ejecutando, estamos listos para iniciar el programador de **Yarn**. **Hadoop**, por sí solo, puede programar cualquier trabajo, por lo que necesitamos ejecutar **Yarn** para poder programar trabajos en nuestro *clúster* de **Hadoop**. Para ello, debemos correr en nuestro **maestro** las siguientes líneas:

```shell
export HADOOP_HOME="/usr/local/hadoop"
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
```

Luego, en cada uno de sus esclavos de hadoop (esclavo1 y esclavo2) desde el *Usuario hadoop*, debemos editar **yarn-site.xml**. Si estamos en el *Súper usiario* corremos los siguientes comandos para salir de él y entrar en el *Usuario hadoop*:

```shell
exit
su hadoop
```
Nos pedirá la clave y ya estaremos en el *Usuario hadoop*. Para configurar el archivo , corremos la siguiente línea:

```shell
cd
nano /usr/local/hadoop/etc/hadoop/yarn-site.xml
```

En el archivo, editaremos las líneas de configuración con lo siguiente:

```shell
<configuration>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>maestro</value>
        </property>
</configuration>
```

Guardamos con *CTRL + O*, luego *Enter* y cerramos con *CRTL + X*.

Ejecutamos este comando desde la *terminal* del equipo **maestro** para iniciar **Yarn**:

```shell
start-yarn.sh
```

Al finalizar, podemos comprobar que se inició correctamente ejecutando este comando:

```shell
yarn node -list
```

Obteniendo la siguiente salida con la información de los nodos.

![nodos](/Imagenes/4.png)

#### Interfaz web de Hadoop

Puede ver la interfaz de usuario web de Hadoop, en el navegador de internet del equipo **maestro**, yendo a esta URL: **maestro:8088/cluster**. En ella puede ver el estado de su *cluster*.

## Ejemplos de uso del *cluster*




