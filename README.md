# Speaking Mickey Mouse
Create a speech recognition and synthesis Raspberry Pi 3. It is something like Google Home, but completely customizable. 

### Overview
A Raspberry can recognize speech, convert it to text, translate it to a different language and finally speak it.

In this project I'll use:
- HTML5 Speech Recognition to convert speech to text
- Google Translate to translate text from a language to a different one (Italian to English in my case).

### Collect Hardware
1. [Raspberry PI 3 Model B Scheda madre CPU 1.2 GHz Quad Core, 1 GB RAM](https://www.amazon.it/gp/product/B01CD5VC92/ref=oh_aui_search_detailpage?ie=UTF8&psc=1) bought on Amazon
2. USB microphone [I bought this on Amazon](https://www.amazon.it/Microfono-Piccolo-desktop-Discorso-registrazione/dp/B00XU1GHO4/ref=sr_1_5?s=electronics&ie=UTF8&qid=1517425711&sr=1-5&keywords=microfono+usb)
3. amplified speaker (any)
4. (optional) HDMI monitor or TFT LCD 3.5" screen

### Prepare your Raspberry
1. Start from a clean sd: I tested 8M and 32M SD Samsung cards.
2. Install "Raspian Jessie with Desktop" or "Raspbian Stretch with Desktop", I tested:
   - Stretch "2017-11-29-raspbian-stretch.img" downloaded and with "installation guide" at [Download Raspbian Stretch](https://www.raspberrypi.org/downloads/raspbian/)
   - Jessie "2017-04-10-raspbian-jessie.img"
   - Jessie "2016-11-25-raspbian-jessie.img"
3. (Optional, if you don't have screen, keyboard and mouse) Prepare the SD you just created for headless operations following these instructions. [
Raspbian Stretch Headless Setup Procedure](https://www.raspberrypi.org/forums/viewtopic.php?t=191252) 

### Set speaker and microphone
1. Simply connect any audio amplified speaker to the 3.5mm audio output jack of your Raspberry
2. Simply insert your USB microphone to any USB socket of your Raspberry
3. Determine card and device of your "bcm2835 ALSA" integrated audio which serves the 3.5mm jack with:


### Install Software
1. Install the NGINX web server

Follow these instructions to install and start the NGINX web server in your Raspberry Pi, including PHP support. 

[SETTING UP AN NGINX WEB SERVER ON A RASPBERRY PI](https://www.raspberrypi.org/documentation/remote-access/web-server/nginx.md)

2. Set SSL on NGINX web server

Create the two certificate files server.key and server.crt that will be used ny NGINX web server when serving HTTPS
```
sudo mkdir /etc/nginx/conf.d/ssl 
cd /etc/nginx/conf.d/ssl
sudo openssl genrsa -out server.key 2048
sudo openssl req -new -key server.key -out server.csr
sudo openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```
Now change /etc/nginx/sites-enabled/default to set HTTPS
```
cd /etc/nginx/conf.d/sites-enabled
sudo nano default
```
adding the following server section: 
```
server {
	# SSL configuration
	#
	listen 443 ssl default_server;
	listen [::]:443 ssl default_server;
	#
	# Note: You should disable gzip for SSL traffic.
	# See: https://bugs.debian.org/773332
	#
	# Read up on ssl_ciphers to ensure a secure configuration.
	# See: https://bugs.debian.org/765782
	#
	# Self signed certs generated by the ssl-cert package
	# Don't use them in a production server!
	#
	# include snippets/snakeoil.conf;
	ssl on;
	ssl_certificate     /etc/nginx/conf.d/ssl/server.crt;
	ssl_certificate_key /etc/nginx/conf.d/ssl/server.key;

	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	# pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	}
}
```
3. Install googletrans

This is a python program which is able to use Google Translate. 
It acts like the [Google Translate Web Site](https://translate.google.it/)
For further info see [https://pypi.python.org/pypi/googletrans](https://pypi.python.org/pypi/googletrans)
```
sudo pip3 googletrans
```
