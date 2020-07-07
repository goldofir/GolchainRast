# GolchainRastreabilidade
Rastreabilidad Golchcain


Prerrequisitos
Esta guía describe cómo instalar Golchain en Ubuntu Server 18.04 LTS. Los requisitos de hardware dependen de la base de datos elegida y la cantidad de dispositivos conectados al sistema. 

Para ejecutar Golchain y PostgreSQL en una sola máquina, necesitará al menos 1 Gb de RAM. 

Para ejecutar Golchain y Cassandra en una sola máquina, necesitará al menos 8 Gb de RAM.

###########################

Paso 1. Instalar Java 8 (OpenJDK)
El servicio se ejecuta en Java 8. Siga estas instrucciones para instalar OpenJDK 8:

sudo apt update
sudo apt install openjdk-8-jdk


No olvide configurar su sistema operativo para usar OpenJDK 8 de forma predeterminada. 

Puede configurar qué versión es la predeterminada con el siguiente comando:

sudo update-alternatives --config java
Copiar al portapapeles
Puede verificar la instalación con el siguiente comando:

java -version

El resultado esperado del comando es:

openjdk version "1.8.0_xxx"
OpenJDK Runtime Environment (...)
OpenJDK 64-Bit Server VM (build ...)


Paso 2. Instalación del servicio en Golchain

Descargue el paquete de instalación.

wget https://dist.thingsboard.io/thingsboard-3.0.1pe.deb

Instalar Golchain como un servicio

sudo dpkg -i thingsboard-3.0.1pe.deb


Paso 3. Obtenga y configure la clave de licencia


Una vez que obtenga el secreto de la licencia, debe colocarlo en el archivo de configuración del tablero de cosas. 

Abra el archivo para editar con el siguiente comando:

sudo nano /etc/thingsboard/conf/thingsboard.conf
Copiar al portapapeles
Localice el siguiente bloque de configuración:

UNCOMMENT NEXT LINE AND PUT YOUR LICENSE SECRET:
export TB_LICENSE_SECRET=
y pon tu licencia en secreto. 
No olvides descomentar la declaración de exportación. 


UNCOMMENT NEXT LINE AND PUT YOUR LICENSE SECRET:
export TB_LICENSE_SECRET=YOUR_LICENSE_SECRET_HERE


Paso 4. Configurar la base de datos
Puede utilizar el enfoque de base de datos híbrida o SQL. 
Vea la página de arquitectura correspondiente para más detalles.

PostgreSQL
(recomendado para <5K msg / seg)

PostgreSQL + Cassandra híbrido

(recomendado para> 5K msg / seg)

PostgreSQL + TimescaleDB híbrido

(para profesionales de TimescaleDB)
recomendamos utilizar PostgreSQL para entornos de desarrollo y producción con una carga razonable (<5000 msg / seg). 

Muchos proveedores de la nube admiten servidores PostgreSQL administrados, que es una solución rentable para la mayoría de las instancias .

Instalación de PostgreSQL
Las instrucciones enumeradas a continuación lo ayudarán a instalar PostgreSQL.

 install ** wget** if not already installed:
sudo apt install -y wget

i mport the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

add repository contents to your system:
RELEASE=$(lsb_release -cs)
echo "deb http://apt.postgresql.org/pub/repos/apt/ ${RELEASE}"-pgdg main | sudo tee  /etc/apt/sources.list.d/pgdg.list

install and launch the postgresql service:

sudo apt update
sudo apt -y install postgresql-12
sudo service postgresql start

Una vez que PostgreSQL esté instalado, es posible que desee crear un nuevo usuario o establecer la contraseña para el usuario principal. 

Las siguientes instrucciones ayudarán a establecer la contraseña para el usuario principal de postgresql

sudo su - postgres
psql
\password
\q
Luego, presione "Ctrl + D" para volver a la consola de usuario principal y conectarse a la base de datos para crear la base de datos del panel de control:

psql -U postgres -d postgres -h 127.0.0.1 -W
CREATE DATABASE thingsboard;
\q


Configuración
Editar el archivo de configuración

sudo nano /etc/thingsboard/conf/thingsboard.conf

Agregue las siguientes líneas al archivo de configuración. 

No olvide reemplazar "PUT_YOUR_POSTGRESQL_PASSWORD_HERE" con su contraseña de usuario real de postgres :

#DB Configuration 
export DATABASE_ENTITIES_TYPE=sql
export DATABASE_TS_TYPE=sql
export SPRING_JPA_DATABASE_PLATFORM=org.hibernate.dialect.PostgreSQLDialect
export SPRING_DRIVER_CLASS_NAME=org.postgresql.Driver
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/thingsboard
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=PUT_YOUR_POSTGRESQL_PASSWORD_HERE
export SPRING_DATASOURCE_MAXIMUM_POOL_SIZE=5
Specify partitioning size for timestamp key-value storage. Allowed values: DAYS, MONTHS, YEARS, INDEFINITE.

export SQL_POSTGRES_TS_KV_PARTITIONING=MONTHS


Ejecute el script de instalación
Una vez que se instala el servicio y se actualiza la configuración de la base de datos, puede ejecutar el siguiente script:

--loadDemo option will load demo data: users, devices, assets, rules, widgets.

sudo /usr/share/thingsboard/bin/install/install.sh --loadDemo


Paso 8. Inicie el servicio Golchchain

Ejecute el siguiente comando para iniciar Golchain:

sudo service thingsboard start

Copiar al portapapeles
Una vez iniciado, podrá abrir la interfaz de usuario web mediante el siguiente enlace:

http://localhost:8080/

Datos de instalacion, licencia y claves Postgress Encriptados en la nube.
