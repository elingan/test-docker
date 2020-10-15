# Docker

## Portainer

Instalar **Portainer**

```bash
docker volume create portainer_data
# cabiamos el puerto
docker run -d -p 8888:8000 -p 9999:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

Abrir [portainer](http://localhost:9999) con el usuario y contraseña admin/adminadmin

## Contenedores

Este proceso esta basado en los siguientes ejemplos:

- [WordPress Deployment with NGINX, PHP-FPM and MariaDB using Docker Compose](https://medium.com/swlh/wordpress-deployment-with-nginx-php-fpm-and-mariadb-using-docker-compose-55f59e5c1a)
- [Ubuntu Linux Add a User To Group www-data ( Apache Group )](https://www.cyberciti.biz/faq/ubuntu-add-user-to-group-www-data/)

si se crean los volumenes dependiendo del tipo de contenedor, se pueden copiar los archivos (si se tiene un backup) o se utilizan los archivos generados durante la creación, por ejemplo:

**Para /mariadb:**

- La carpeta **/conf** contiene el archivo de configuración del sistema de DB
- La carpeta **/data** contiene los archivos de la DBs creadas y todo lo requerido por la versión de MariaDB, tener en cuenta que la primera vez esta vacia y se copian los archivos, para las siguientes veces solo se reutiliza el contenido

**Para /nginx:**

- El archivo de configuración de la carpeta **/conf** se ha copiado en la del contenedor
- Hay que darle permisos a la carpeta **/html**

Se debe crear una red para agrupar todos los contenedores

```bash
docker network create localhost-network
```

Ejecutar por cada docker-compose.yml

```bash
docker-compose up -d
```

## MariaDB

### Acceder a la terminar

```bash
docker exec -it mariadb bash
```

### Crear una nueva DB y usuario

```bash
mysql -uroot -p"root" \
    -e "CREATE DATABASE IF NOT EXISTS wp_db; \
    GRANT ALL PRIVILEGES ON wp_db.* TO 'wp_user'@'%' IDENTIFIED BY 'wp_pass'; \
    FLUSH PRIVILEGES;"
```

## Nginx

## Wordpress

### Permisos

Cambiar los permisos de la carpeta **_/wordpress/html_**

```bash
# Agrega eduardo al grupo www-data
sudo adduser eduardo www-data

# Cambia los permisos de la carpeta **html** para poder editar localmente
sudo chown -R eduardo:www-data html
```

```bash
sudo groupdel www-data
sudo groupadd -g 33 www-data
sudo groups eduardo # add www-data, el mismo id que tiene el container
sudo adduser eduardo www-data # se agrega el usuario al grupo
sudo deluser eduardo www-data # remover el usuario del grupo
sudo chmod g+w wp-config.php # permisos de grupo al archivo para poder editarlo
sudo usermod -aG http $USER
sudo chown $USER wp-config.php # cambiamos la propiedad del archivo para poder editarlo, no da problemas con docker
```

- [Docker Wordpress](https://hub.docker.com/_/wordpress/)
- [Installing Multiple WordPress Instances](https://wordpress.org/support/article/installing-multiple-blogs/#the-multisite-feature)
- [Child themes](https://developer.wordpress.org/themes/advanced-topics/child-themes/)

### Crear/Editar un Tema

Abrir la terminal del contendor y crear la carpeta _wp-content/themes/[theme]-child_ y luego cambiar los permisos

```bash
sudo mkdir html/wp-content/themes/twentytwenty-child
sudo chown -R $USER:html twentytwenty-child
# permisos de grupo a la carpeta del tema para editar
sudo chmod go+w wp-content/themes/twentytwenty-child
```
