# README - Configuración de Servidor Web con Nginx en Vagrant

Este proyecto configura un servidor web con Nginx en una máquina virtual Debian usando Vagrant. Se utiliza un repositorio GitHub para alojar un sitio estático.


## Pasos para Ejecutar



### Iniciar la máquina virtual

   
   vagrant up
   

### Acceder a la máquina virtual

   vagrant ssh


### Acceder al sitio web
   Cambiamos el archivo hosts a:
		192.168.57.10 		mrm  www.mrm www.mrm.com mrm.com


   Abre tu navegador y accede a `http://mrm`, y te saldrá la página web del diamante
  
   Tambien sirve al poner `http://mrm.com` o `http://www.mrm.com`


