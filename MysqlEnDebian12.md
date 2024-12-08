# Creacion de una base de datos mysql en un servidor vps con distribucion de debian 12

En este readme se redacta como realizar una instalacion y configuracion de un servidor de base datos mysql en un servidor vps con la distribucion de debian 12
a continuacion se muestran los pasos a realizar

## Instalacion
Como primer paso realizacion una actualizacion de nuestros paquetes

```bash
sudo apt update -y && sudo apt upgrade -y
```
Seguido con wget instalamos el repositorio de mysql
```bash
wget https://dev.mysql.com/get/mysql-apt-config_0.8.30-1_all.deb
```

una vez instalado el repositorio del paquete procedemos instalarlo utilizando el gestor de paquetes de debian dpkg
```bash
sudo dpkg -i mysql-apt-config_0.8.30-1_all.deb
```

una vez instalado el paquete en nuestro servidor nos aparecera una pequeña interfaz de instalacion donde nos pedira que herramientas deseamos instalar
nos mostrara una interfaz como la siguiente
```bash

----------------------------------------
| Configuring mysql-apt-config          |
----------------------------------------
| Which MySQL product do you wish to    |
| configure?                            |
|                                        |
| MySQL Server & Cluster                |
|   (Currently selected: mysql-8.0)     |
|                                        |
| MySQL Tools & Connectors              |
|   (Currently selected: Enabled)       |
|                                        |
| MySQL Preview Packages                |
|   (Currently selected: Disabled)      |
|                                        |
| Ok                                     |
----------------------------------------
                     <Ok>


```
seleccionamos la opcion de MySQL Server & Cluster una vez selecionada nos pedira una version de mysql en mi caso yo seleciona nysql.8.0 una vez selecionado esa parte sola damos
a la opcion de ok
<br/>
Una vez realizado lo anterior solo damos una actualizacion a nuestros paquetes
```bash
sudo apt update
```
## instalacion apt de mysql server
una vez realizados los pasos anteriores procederemos a instalar los paquetes via apt
```bash
sudo apt install mysql-server -y
```
Una vez que se realize la instalacion nos pedira una constraseña(password) para nuestro usuario root de nuestro servidor es ideal que recordemos nuestra contraseña ya que 
usaremos en paso mas adelantes. siguiendo con el manual una vez creada nuestra contraseña la instalacion nos pidira estas configuraciones(opcional)
```bash
Use Strong Password Encryption (RECOMMENDED)                                     
Use Legacy Authentication Method (Retain MySQL 5.x Compatibility)
```
Esto podemos omitirlo selecionando la opcion ok que nos arrojara pero si quisieramos mejor la seguridad estos pasos son importantes pero para este manual omitiremos esto
ya que esto es para fines practicos aunque para un entorno de produccion lo ideal seria agregar una capa de seguridad para nuestras aplicaciones.

## verificacion de la instalacion 
una vez realizados los pasos anteriores para verificar si se instalo correctamente usamos el siguiente comando
```bash
mysql --version
```
lo cual no arrojora un mensaje con la pequeña descripcion de nuestra version de mysql server


## Activando el servidor
una vez realizado correctamente los pasos de instalacion anterior ahora si procederemos a instalar nuestro servidor
```bash
sudo systemctl enable mysql
```
Ejecutando nuestro servidor
```bash
sudo systemctl start mysql
```
Verificando el status de nuestro servidor
```bash
sudo systemctl status mysql
```
Pero por el momento solo funciona para un entorno local para poder exponer nuestra servidor de base de datos con clientes externos procederemos a realizar una pequeñas configuraciones
## Configuracion para exponer nuestra servidor a clientes externos
accedemos a la ubicacion del siguiente archivo y con tu editor de texto favorito agregamos una pequeña configuracion en mi caso usare nano

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
una vez estando ahi agregamos lo siguiente al final de archivo
```bash
bind-address  = 0.0.0.0
```
Lo que hara esta pequeña instrucion es exponer al publico 
una vez realizado eso guardamos 

##  Permitir conexiones remotas al usuario de nuestra bd
Accedemos a la terminal de MySql para poder realizar nuestra configuraciones ejecutamos el comando siguiente una vez ejecutado nos pedira la contraseña registrada al inicio 
de nuestra configuracion.

```bash
sudo mysql -u root -p
```
Una vez dentro de la terminal de mysql ejecutamos el siguiete comando lo cual permitira el acceso remoto al usuario root.
```bash
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '__contraseñaRegistrada__' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
una vez ejecutado lo anterior para salir de la terminal mysql escribimos **exit** para salir de ella una vez fuera reiniciamos nuestro servidor mysql con el 
siguiente comando
```bash
sudo systemctl restart mysql
```
para conectarnos externamente necesitaremos de lo siguientes
- **El host o la ip donde esta alojada nuestra base de datos**
- **usuario de nuestra servidor BD en este ejemplo es root pero podria agregar mas usuario donde se apliquen restricciones de permiso**
- **La contraseña de nuestro servidor bd**     

```bash
HostOrIp = "ip o host"
usuario = "root"
password = "__ContraseñaRegistrada__""
```

## Notas adicional
En el caso de estar usando un firewall en el servidor se recomienda abrir el puerto por defecto de mysql ( 3306) en nuesto servidor



