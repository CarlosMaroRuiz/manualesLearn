# Creacion de loadBalancer con diferentes api de diferentes tecnologia

## Tecnologias de nuestras api rest
- **Fast api**
- **Express usando typescript para su desarrollo**
- **Nignx :** Que nos servira como servidor para realizar el balanceador de carga
- **MYSQL** Base de datos para el proyecto

En el caso de este proyecto usamos mysql como base de datos centralizada para las apis

## Repositorios de las api rest
- https://github.com/CarlosMaroRuiz/sellerBuyers.git
- https://github.com/pabloin1/sellerBuyers-API

## Paso a realizar



Para realizar el balanceo, primero ejecutamos nuestras API REST en el mismo servidor, lo cual para este proyecto fue suficiente para las necesidades iniciales de un sistema. 
Sin embargo, para garantizar la escalabilidad y mejorar el rendimiento en escenarios con mayor carga de trabajo, es recomendable desplegar las API en diferentes servidores. 
Esta estrategia permite distribuir las solicitudes entrantes de manera eficiente, reducir la sobrecarga en un solo punto y mejorar la tolerancia a fallos.

### Definir los servidores backend con upstream de nginx

- **UPSTREAM :** se refiere a un bloque de configuracion de nginx que define un grupo de servidores backend a los que se puede dirigir el trafico para procesar las solicitudes.

 En nuestro primero definiremos nuestros servidores supongamos que tenenemos nuestro 2 servidores uno con fast api(A) y el otro express(B).
 - A: 192.168.1.10:3001
 - B: 192.168.1.10:3002

una vez definidos nuestros servidores creamos un archivos de configuracion para nuestros servidores en ngnix
```bash
sudo nano /etc/nginx/conf.d/loadbalancer.conf 
```
En el archivo creado usaremos la setencia upstream para realizar la configuracion del grupo de servidores
```nginx
upstream backend_servers {
    server 192.168.1.10:3001  
    server 192.168.1.10:3002 
}
```
Guardamos seguido de eso configuramos nuestro server para reenviar la solicitudes al grupo de servidores
```nginx
server {
    listen 80;
    server_name exampledomain.com; 

    location / {
        proxy_pass http://backend_servers;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
En nuestro caso estamos reenviando al puerto 80 pero si quisiera agregar seguridad podriamos instalar cerbot para ssl pero sera para otra ocasion XD.

una vez realizado lo anterior solo reinciamos nuestro servidor nginx y probamos


 



