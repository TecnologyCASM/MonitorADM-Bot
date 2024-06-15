# Mi HomeMonitor:
- `Portainer "MGMT Container"`
- `Docker Controller Bot "MGMT Container Bot Telegram"`
  
Este proyecto esta diseñado con la finalidad de utilizarse en una red domestica, utilizando una Raspberry Pi B 8GB con dispositivo, donde contamos con la configuracion de dos servicios de administracion de contenedores de docker como Portainer y Docker Controller.

![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/dc9c6986-a497-49c6-a1f5-05eeb92cb2de)
![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/df035d60-2d1a-466c-8ccd-cb2c30bd6b7c)
![image](https://github.com/TecnologyCASM/MonitorADM-Bot/assets/107158068/89ffaebc-c4e2-4519-bcfb-a34be0f25ba4)

# Prerequisitos:
* `Raspberry Pi 4 Modelo B 2GB`. https://amzn.to/3K7diXR
* `64GB tarjeta MicroSD`. https://amzn.to/3ynPiNz
* `Lector de tarjetas SD USB`. https://amzn.to/3wGN8bs
  
Nota: Dicho esto, este proceso debería funcionar en cualquier Raspberry Pi.

# Instalacion de Sistema Operativo en Raspberry Pi:
1) Descarga la aplicacion "[RaspberryOS](https://www.raspberrypi.com/software/)" de la pagina oficial.
2) Conecta a la PC el lector SD con la memoria micro y segue los pasos como se muestra en la imagen mas abajo.
3) Elige el sistema operativo recomendado por raspberry.
![image](https://github.com/TecnologyCASM/PiHoleUnbound/assets/107158068/4173438b-eca6-497a-85d0-ec96bf698629)
![image](https://github.com/TecnologyCASM/PiHoleUnbound/assets/107158068/3a84ef2b-4204-4585-a62f-6c5adf6b9236)
4) Una vez instalado el sistema operativo en la raspberry, conecta esta a la red via cable y conecta un monitor para completar las configuraciones iniciales.
5) En este paso, debemos realizar los ajustes de `Network`, ya que estaremos brindando los servicios mencionados, debemos fijar un direccionamiento IP al equipo, donde estaremos ejecutando el siguiente comando, para que nos arroje un asistente:
```shell
sudo nmtui
```
![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/35b590d2-8eab-44af-9d5b-ab48f9270ff5)

6) En este asistente, estaremos eligiendo la opcion `Modificar una conexion`, luego debemos elegir la terjeta de red que dice `Conexion Cableada 1` y presionamos editar:
![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/cc2a65fd-be60-46c5-9aec-0b39bc97c184)
10) Alli aplicamos los ajuste de direccionamiento estatico que sean necesarios, como son:
  * Direcciones: En esta opcion debemos colocar la IP estatica que debera llevar la raspberry, el cual debe llevar el siguiente formato `192.168.1.10/24`.
  * Puerta de Enlace: En esta opcion debemos colocar la IP de nuestro router de internet, el cual se encuentra en el mismo segmento mensionado mas arriba, por ejemplo `192.168.1.1`.
  * Servidores de DNS: En esta opcion colocamos los DNS de nuestra eleccion, en mi caso coloco los de google `8.8.8.8` y `8.8.4.4`.
  * Busqueda de Dominio: Esto es cuando estamos trabajando con un dominio, por lo que es opcional.
7) Despues que la Raspberry Pi inicie el OS despues del reinicio, entonces procederemos con los ajustes de algunos parametros en esta, para ello debemos iniciar el siguiente asistente, con el comando:
```shell
sudo raspi-config
```
Este comando nos llevara a la siguente pantalla:
![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/c138d6d4-2f87-4468-bd1f-2c13102bac31)

8) En este asistente, tendremos que realizar los siguientes ajustes:
  *  `1-System Options=>Boot/Auto Login=>Desktop Autologin`: Opcion utilizada para que la rasperrypi no solicite credenciales al iniciar sesion. (Solo Raspberry Pi Desktop).
  *  `2-Display Options=>VNC Resolutions`: Opcion utilizada para elegir la resolucion de la pantalla de conexion remota a la raspberry pi. (Solo Raspberry Pi Desktop). 
  *  `3-Interface Options=>VNC`: Opcion habilitada para conexion remota por GUI a la raspberry pi. (Solo Raspberry Pi Desktop).
  *  `5-Localisation Options`:
      - Locale: Configuracion del idioma. En mi caso es `es_DO.UTF-8`.
      - Timezona: Configuracion de zona horaria. En mi caso es `America/Santo_Domingo`.
      - Keyboard: Configuracion del Teclado. Solo elegir para autoreconocimiento.
      - WLAN Country: Configuradion de la ciudad para la red wireless. En mi caso `DO Dominican Republic`.
  *  `6-Advanced Options=>Expand FileSystem`: Opcion para expandir por completo el almaceniamiento de la SD de la raspberry.

 Nota: Para que estos ajustes se apliquen debemos presionar `Finish` y este solicitara un reinicio del equipo.
 
# Proceso Actualiacion OS, Instalacion de Docker y agregar usuario a grupo docker: 
1) Una vez la raspberry pi halla iniciado, procederemos a aplicar los siguientes comandos:
      - Actualiazar la lista de repositorios, Sistema Operativo, instalar dependencias, docker, agregar el usuario al grupo docker y reiniciar el equipo:
  ```shell
sudo apt update && sudo apt-get full-upgrade -y \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common \
     vim \
     fail2ban \
     ntfs-3g &&
sudo curl -fsSL https://get.docker.com/ -o get-docker.sh && sudo sh get-docker.sh &&
sudo usermod -aG docker ${USER} && sudo rm -r get-docker.sh &&
sudo reboot
```
2) Una vez la raspberry pi halla iniciado y para validar que el servicio de docker esta instalado, procederemos a descargar un contenedor de prueba llamado `Helo-Wold`:
```shell
docker run hello-world
```
![image](https://github.com/TecnologyCASM/PiHoleUnbound-WG/assets/107158068/58f35f2b-9c35-4381-8186-8f37298e170a)

# Descarga y Ejecucion del proyecto HomePortainer:
1) Ejecutar el siguiente comando para solo descargar el proyecto `PortainerADM-Bot` y acceder a la carpeta del proyecto:
```shell
git clone https://github.com/TecnologyCASM/PortainerADM-Bot.git
cd PortainerADM-Bot
ls -la
```
2) Debe de ir al archivo `.env` y modificar los siguientes parametros:
```shell
# Variables de entorno para Docker Controller Bot
BOT_TOKEN="Mi Token"
CHAT_ID="Mi ID"
```
3) Luego de trabajar en el paso #2, solo resta ejecutar el siguiente comando:
```shell
docker compose up -d && docker ps
```

![image](https://github.com/TecnologyCASM/PortainerADM-Bot/assets/107158068/cd69904b-e0be-4c02-a7d4-f0729300170c)

MUCHAS GRACIAS POR UTILIZAR MI APORTE A LA COMUNIDAD...
