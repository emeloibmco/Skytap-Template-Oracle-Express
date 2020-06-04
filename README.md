# GUIA DE INSTALACIÓN DE ORACLE EXPRESS EN SKYTAP

_Para el desarrollo de este Despliegue se tiene como base una maquina Ubuntu 18.04.1 que se encuentra alojada en Skytap._



## Login y acceso a la maquina ubuntu en Skytap:

#### Para realizar de manera corracta el login y acceso a la maquina ubuntu debemos seguir los siguientes pasos:

_1. Acceder a la plataforma de Skytap, para esto debemos acceder desde algun Browser al siguiente link:_

```
https://cloud.skytap.com/
```
_2. Ahora debemos realizar el login con nuestras credenciales._

<p align="center">
 <img width="956" alt="login" src="https://user-images.githubusercontent.com/60987042/83697833-9549bf00-a5c5-11ea-8fe6-d0b404d6028b.PNG">
 </p>

_3. Al estar en la pagina principal de Skytap debemos dirigirnos a la sección de Environments y estando ahí debemos elegir el Environment en cual queramos trabajar._

<p align="center">
 <img width="850" alt="envi" src="https://user-images.githubusercontent.com/60987042/83697860-a1358100-a5c5-11ea-80cb-d7c9fa00a70a.PNG">
 </p>

_4. Ahora debemos hacer clic en la maquina que queremos trabajar_

<p align="center">
 <img width="949" alt="maquinas" src="https://user-images.githubusercontent.com/60987042/83697874-ab577f80-a5c5-11ea-8df4-c75cd91a394a.PNG">
 </p>


## Preparacion de la maquina Ubuntu para la Instalación 🚀

#### Para la correcta instalación de Oracle Express en ubuntu debemos seguir los siguientes pasos:

_1. Inicialmente debemos descargar el instalador del Oracle Express, y lo podemos hacer desde el siguiente link:_
```
https://www.oracle.com/database/technologies/xe-prior-releases.html
```
<p align="center">
<img width="439" alt="IBMIM" src="https://user-images.githubusercontent.com/60987042/83689743-33815900-a5b5-11ea-84a6-40a486002b4c.PNG">
</p>

_2.	Debemos abrir una terminal, la cual se puede abrir mediante el siguiente comando:_

```
Crtl+Alt+T
```
<p align="center">
<img width="551" alt="term" src="https://user-images.githubusercontent.com/60987042/83689785-45fb9280-a5b5-11ea-9474-639a35eaee67.PNG">
</p>

_3. Debemos dirigirnos a la capeta en la cual se descargo el instalador._

```
cd Desktop
```
ó
```
cd Descargas
```

_4.	Ahora debemos descomprimir el archivo que se descargo:_

```
unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip 
```

_5. Ahora debemos instalar los complementos necesarios para la instalación y lo podemos hacer con el siguinete comando:_

```
sudo apt-get install alien libaio1 unixodbc
```

_6.	A continuanción, debemos convertir el paquete de formato RPM alformato DEB (Este formato es el usado por ubuntu) usando el siguiente comando:_

```
sudo alien --scripts -d oracle-xe-11.2.0-1.0.x86_64.rpm
```
_7. Una vez se tenga el archivo .deb, necesitamos crear el script chkconfig requerido y se hace usando el siguiente comando:	_

```
sudo nano /sbin/chkconfig
```
_Al ejecutar el comando anterior el editor de texto nano se debe haber iniciado y los comandos se muestran en pantalla. Ahora, copiamos y pegamos lo siguiente dentro del archivo y guardamos:_

```
#!/bin/bash
# Oracle 11gR2 XE installer chkconfig hack for Ubuntu
file=/etc/init.d/oracle-xe
if [[ ! `tail -n1 $file | grep INIT` ]]; then
echo >> $file
echo '### BEGIN INIT INFO' >> $file
echo '# Provides: OracleXE' >> $file
echo '# Required-Start: $remote_fs $syslog' >> $file
echo '# Required-Stop: $remote_fs $syslog' >> $file
echo '# Default-Start: 2 3 4 5' >> $file
echo '# Default-Stop: 0 1 6' >> $file
echo '# Short-Description: Oracle 11g Express Edition' >> $file
echo '### END INIT INFO' >> $file
fi
update-rc.d oracle-xe defaults 80 01
```
_Para guardar el contenido de este archivo podemos precionar la tecla F2 y dar enter._

_8.	Ahora, debemos cambiar los permisos del archivo chkconfig usando el siguiente comando:_

```
sudo chmod 755 /sbin/chkconfig  
```
_9.	Ahora, debemos establecer los parametros del kernel. Oracle 11gR2 XE requiere de parametros adicionales del kernel los cuales añadimos usando el siguiente comando:_

```
sudo nano /etc/sysctl.d/60-oracle.conf
```
_Al ejecutar el comando anterior el editor de texto nano se debe haber iniciado y los comandos se muestran en pantalla. Ahora, copiamos y pegamos lo siguiente dentro del archivo y guardamos:_

```
# Oracle 11g XE kernel parameters  
fs.file-max=6815744  
net.ipv4.ip_local_port_range=9000 65000  
kernel.sem=250 32000 100 128 
kernel.shmmax=536870912 
```
_Para guardar el contenido de este archivo podemos precionar la tecla F2 y dar enter._

_10.	Ahora, debemos cargar los parámetros del núcleo y lo realizamos usando el siguiente comando:_

```
sudo service procps start
```
_11.	Ahora, Configuramos un punto de montaje /dev/shm para Oracle. Editamos el archivo mediante el siguiente comando:_

```
sudo nano /etc/rc2.d/S01shm_load
```
_Al ejecutar el comando anterior el editor de texto nano se debe haber iniciado y los comandos se muestran en pantalla. Ahora, copiamos y pegamos lo siguiente dentro del archivo y guardamos:_

```
#!/bin/sh
case "$1" in
start) mkdir /var/lock/subsys 2>/dev/null
       touch /var/lock/subsys/listener
       rm /dev/shm 2>/dev/null
       mkdir /dev/shm 2>/dev/null
       mount -t tmpfs shmfs -o size=2048m /dev/shm ;;
*) echo error
   exit 1 ;;
esac 
```
_Para guardar el contenido de este archivo podemos precionar la tecla F2 y dar enter._

_12.	Ahora, debemos cambiar los permisos del archivo usando el siguiente comando:_

```
sudo chmod 755 /etc/rc2.d/S01shm_load
```
_Con el paso anterior damos por terminada la preparación de la maquina para la instalación de Oracle Express._

#### REINICIAMOS LA MAQUINA

## Instalación del Oracle Express: 🚀

_1.	Instalamos el sistema gestor de oracle usando siguiente comando:_

```
sudo dpkg --install oracle-xe_11.2.0-2_amd64.deb
```
_2.	Configuramos Oracle usando el siguiente comando:_

```
sudo /etc/init.d/oracle-xe configure 
```
**NOTA:**
_Introducimos la siguiente informacion:
• Un puerto HTTP valido para la aplicacion Oracle Application Express (por default es: 8080).
• UN puerto valido para el escuchador de la base de datos de Oracle (por default es: 1521).
• Una contraseña para el administrador de cuentas de usuario de SYS y SYSTEM. 
• Confirmar las contraseñas.
• Seleccionar si deseamos que  el sistema gestor se inicie cuando inicie el sistema._

_3.	Ahora, Configuramos las variables de entorno editando nuestro archivo .bashrc y lo realizamos mediante el siguienete comando:_

```
sudo nano ~/.bashrc
```
_Al ejecutar el comando anterior el editor de texto nano se debe haber iniciado y los comandos se muestran en pantalla. Ahora, copiamos y pegamos lo siguiente dentro del archivo y guardamos:_

```
export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe
export ORACLE_SID=XE
export NLS_LANG=`$ORACLE_HOME/bin/nls_lang.sh`
export ORACLE_BASE=/u01/app/oracle
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export PATH=$ORACLE_HOME/bin:$PATH
```
_Para guardar el contenido de este archivo podemos precionar la tecla F2 y dar enter._
_4.	Ejecutamos los cambios usando el siguiente comando:_

```
sudo . ~/.profile
```

_5.	Iniciamos Oracle 11gR2 XE usando el siguiente comando:_

```
sudo service oracle-xe start
```
_6.	Agregamos un usuario YOURUSERNAME al grupo dba usando el siguiente comando:_

```
sudo usermod -a -G dba <YOURUSERNAME>
```

**Con el paso anterior damos por terminada la instalación de Oracle Express**


### USO DEL COMAND SHELL DE ORACLE

_1.	Iniciamos los servicios de Oracle XE 11gR2 server usando el comando:_

```
sudo service oracle-xe start
```
_2.	Iniciamos la linea de comando como el administrador del sistema:_

```
sqlplus sys as sysdba
```
**Nota: Aca debemos ingresar la contraseña que seleccionamos durante la instalación.**

_3.	Creamos una cuenta de usuario regular usando el comando:_

```
create user <USERNAME> identified by <PASSWORD>;
```
**Nota: Reemplazamos USERNAME y PASSWORD con el usuario y contraseña que hayamos elejido.**

_4.	Ahora, debemos Asignar privilegios a la cuenta de usuario usando el comando SQL:_

```
grant connect, resource to USERNAME;
```
_5.	Salimos con el siquiente comando SQL:_

```
exit;
```
_6.	Iniciamos la linea de comando como un usuario regular usando el comando:_

```
sqlplus
```

## Autores ✒️

_Equipo IBM Cloud Tech sales Colombia._
