# HomeSound
A DIY project that involves AmI (Ambiance Intelligence)

Para poder recrear el proyecto necesitaremos tanto software como hardware, a continuacion una pequeña lista:

Hardware:
-Tarjeta de audio externa USB
-Modulo receptor de audio Bluetooth 
-Raspberry Pi B 2
-Chip ADC (convertidor Analogico a Digital) MCP3008
-Sensor FSR (Round Force Resistor)
-Antena Wifi
-Bocinas con alimentacion externa y entrada de jack 3.5 mm

Software
-Raspbian (sistema operativo para Raspberry Pi)
-IceCast2 servidor de streamming multimedia
-DarkIce cliente de IceCast
-Omxplayer reproductor de video 


Es necesario por lo menos contar con dos Raspberry's Pi, una sera el servidor y la otra sera el cliente, pueden agregarse mas como clientes.

Para descargar e instalar el sistema operativo en la Raspberry Pi podemos seguir el siguiente tutorial:
	Descarga:
	
	-https://www.raspberrypi.org/downloads/
	Instalación:
	
	-https://www.raspberrypi.org/documentation/installation/installing-images/README.md

Una vez instalado el sistema operativo es recomendable ejecutar los siguientes comandos:

	-sudo apt-get update
	-sudo apt-get upgrade


DESCARGA DE DARK ICE
Una vez actualizado instalaremos DarkIce el cliente de IceCast2:

En el directorio "/etc/apt/source.list" añadiremos la siguiente linea:

	-deb-src http://mirrordirector.raspbian.org/raspbian/ wheezy main contrib non-free rpi

Si ya existe no es necesario realizar ningun cambio

Instalamos las dependencias de DarkIce con el siguiente comando

	-sudo apt-get --no-install-recommends install build-essential devscripts autotools-dev fakeroot dpkg-dev debhelper autotools-dev dh-make quilt ccache libsamplerate0-dev libpulse-dev libaudio-dev lame libjack-jackd2-dev libasound2-dev libtwolame-dev libfaad-dev libflac-dev libmp4v2-dev libshout3-dev libmp3lame-dev
	-mkdir src; cd src/
	-apt-get source darkice

En el siguiente directorio modificaremos la configuración:
	-cd darkice-1.0
	-vi debian/rules

Dentro de "/debian/rules" debemos tener los siguiente
	!/usr/bin/make -f

	%:
    	dh $@

	.PHONY: override_dh_auto_configure
	override_dh_auto_configure:
    	ln -s /usr/share/misc/config.guess .
    	ln -s /usr/share/misc/config.sub .
    	dh_auto_configure -- --prefix=/usr --sysconfdir=/usr/share/doc/darkice/examples --with-vorbis-prefix=/usr/lib/arm-linux-gnueabihf/ --with-jack-prefix=/usr/lib/arm-linux-gnueabihf/ --with-alsa-prefix=/usr/lib/arm-linux-gnueabihf/ --with-faac-prefix=/usr/lib/arm-linux-gnueabihf/ --with-aacplus-prefix=/usr/lib/arm-linux-gnueabihf/ --with-samplerate-prefix=/usr/lib/arm-linux-gnueabihf/ --with-lame-prefix=/usr/lib/arm-linux-gnueabihf/ CFLAGS='-march=armv6 -mfpu=vfp -mfloat-abi=hard'


En caso de no compilar podemos descargar las reglas del siguiente enlace:
	-http://mattkaar.com/misc/debian/rules

Para permitir MP3 mecesitaremos ejecutar el siguiente comando:
	-debchange -v 1.0-999~mp3+1

INSTALACION

Para construit nuestro darkice:
-dpkg-buildpackage -rfakeroot -uc -b
	-sudo dpkg -i ../darkice_1.0-999~mp3+1_armhf.deb
	-sudo cp /usr/share/doc/darkice/examples/darkice.cfg /etc/


INSTALACION DE ICECAST2

Ahora necesitamos instalar el servidor
	-sudo apt-get install icecast2

El instalador nos pedire que asignemos contraseñas el servidor y el darkice deben tener la misma contraseña.

Para dar de alta el servicio de icecast2
	-sudo service icecast2 start
	-sudo darkice



INSTALACION omxplayer

Para instalar el reproductor multimedia:
	-sudo apt-get install omxplayer

Para poder escuchar alguna transmision del servidor ejecutamos el siguiente comando

	-omxplayer -o local [direccion ip de icecast:puerto/transmision]

El parametro "-o local" nos permite utilizar el jack 3.5mm como las salida de audio estandar.

Presionando en el lado del cliente "Crtl + c" podemos dejar de escuchar. 


Para poder recrear el sistema HomeSound
Modulo receptor de audio Bluetooth, esto nos permite conectar cualquier dispositivo Bluetooth y empezar transmision de audio, una vez conectado a algun dispositivo ya no estara disponible hasta que la conexion se cierre.

Como la Raspberry Pi no cuenta con una entrada de audio utilizamos una tarjeta de audio externa USB, lo cual no permite tomar el audio que sale por el modulo bluetooth.
Una de las ventajas de IceCast es que nos permite hacer stream de un dispositivo, en este caso ese dispositivo sera la entrada de nuestra tarjeta de audio.

En el archivo de configuracion de DarkIce seleccionamos el dispositivo.

Es necesario utilizar el codigo "sensores.py" el cual realiza lecturas de los sensores a traves de la GPIO y permite conexion y desconexion hacia el servidor de stream.

Es necesario revisar diagramas de conexión en Raspberry Pi para los sensores.

Futuras mejoras:
-Utilizar Raspberry Pi Zero
-Una tarjeta de audio usb de mejor calidad
-Un dispositivo Bluetooth Low Energy
-Comandos de voz

