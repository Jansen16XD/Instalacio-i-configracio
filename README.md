# Owncloud
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Instalación de ownCloud en Ubuntu</title>
</head>
<body>
    <h1>Guía para Instalar ownCloud en Ubuntu</h1>
    <p>Esta guía te ayudará a instalar <strong>ownCloud</strong> en un servidor Linux (idealmente con <strong>Ubuntu 20.04</strong> o <strong>Ubuntu 22.04</strong>) para que puedas tener tu propio sistema de almacenamiento en la nube, similar a Dropbox o Google Drive, pero totalmente bajo tu control.</p>

    <h2>Paso 1: Preparación del sistema</h2>
    <p>Actualiza los paquetes del sistema para evitar problemas de compatibilidad:</p>
    <pre><code>sudo apt update && sudo apt upgrade -y</code></pre>

    <h2>Paso 2: Instalar Apache, MariaDB y PHP</h2>
    <p>ownCloud necesita un servidor web (Apache), una base de datos (MariaDB) y PHP. Instálalos con el siguiente comando:</p>
    <pre><code>sudo apt install apache2 mariadb-server libapache2-mod-php7.4 -y</code></pre>
    <p>Luego, instala módulos adicionales de PHP que ownCloud requiere:</p>
    <pre><code>sudo apt install php7.4 php7.4-gd php7.4-mysql php7.4-curl php7.4-xml php7.4-mbstring php7.4-zip php7.4-intl php7.4-json php7.4-bcmath php7.4-imagick -y</code></pre>
    <p><em>Nota:</em> Si tu versión de Ubuntu es más reciente, puede que necesites cambiar <code>php7.4</code> por <code>php8.1</code> u otra versión.</p>

    <h2>Paso 3: Configurar MariaDB</h2>
    <p>Configura la base de datos ejecutando el asistente de configuración:</p>
    <pre><code>sudo mysql_secure_installation</code></pre>
    <p>Después de configurar la seguridad de MariaDB, crea una base de datos y un usuario para ownCloud:</p>
    <pre><code>sudo mysql -u root -p</code></pre>
    <pre><code>
CREATE DATABASE owncloud;
CREATE USER 'ownclouduser'@'localhost' IDENTIFIED BY 'tu_contraseña_segura';
GRANT ALL PRIVILEGES ON owncloud.* TO 'ownclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
    </code></pre>

    <h2>Paso 4: Descargar y preparar ownCloud</h2>
    <p>Descarga ownCloud desde el sitio oficial y descomprime el archivo en el directorio de Apache:</p>
    <pre><code>wget https://download.owncloud.org/community/owncloud-latest.tar.bz2
tar -xjf owncloud-latest.tar.bz2
sudo mv owncloud /var/www/html/</code></pre>
    <p>Configura los permisos necesarios para que Apache pueda acceder a los archivos:</p>
    <pre><code>sudo chown -R www-data:www-data /var/www/html/owncloud/
sudo chmod -R 755 /var/www/html/owncloud/</code></pre>

    <h2>Paso 5: Configurar Apache</h2>
    <p>Crea un archivo de configuración para ownCloud en Apache:</p>
    <pre><code>sudo nano /etc/apache2/sites-available/owncloud.conf</code></pre>
    <p>Dentro del archivo, agrega lo siguiente:</p>
    <pre><code>
&lt;VirtualHost *:80&gt;
    DocumentRoot /var/www/html/owncloud
    ServerName tu_dominio_o_IP

    &lt;Directory /var/www/html/owncloud/&gt;
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
       &lt;IfModule mod_dav.c&gt;
           Dav off
       &lt;/IfModule&gt;
    &lt;/Directory&gt;

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
&lt;/VirtualHost&gt;
    </code></pre>
    <p>Guarda el archivo y habilita el sitio y los módulos necesarios:</p>
    <pre><code>sudo a2ensite owncloud.conf
sudo a2enmod rewrite headers env dir mime
sudo systemctl restart apache2</code></pre>

    <h2>Paso 6: Completar la instalación desde el navegador</h2>
    <p>Abre un navegador y dirígete a <code>http://tu_dominio_o_IP/owncloud</code>. Esto te llevará a la pantalla de configuración de ownCloud.</p>
    <ul>
        <li><strong>Crear una cuenta de administrador:</strong> Ingresa un nombre de usuario y una contraseña para el administrador.</li>
        <li><strong>Configurar la base de datos:</strong></li>
        <ul>
            <li>Tipo de base de datos: <code>MySQL/MariaDB</code></li>
            <li>Usuario de la base de datos: <code>ownclouduser</code></li>
            <li>Contraseña de la base de datos: <code>tu_contraseña_segura</code></li>
            <li>Nombre de la base de datos: <code>owncloud</code></li>
            <li>Servidor de la base de datos: <code>localhost</code></li>
        </ul>
    </ul>
    <p>Completa estos campos y haz clic en "Finalizar la instalación".</p>

    <h2>Paso 7 (Opcional): Configurar HTTPS</h2>
    <p>Para que ownCloud sea más seguro, es recomendable habilitar HTTPS. Puedes hacer esto con <strong>Certbot</strong> y Let’s Encrypt, que ofrece certificados SSL gratuitos:</p>
    <pre><code>sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d tu_dominio_o_IP</code></pre>
    <p>Sigue las instrucciones para obtener y configurar el certificado SSL.</p>

    <p>Con esto, tu instalación de ownCloud estará lista. Puedes comenzar a almacenar y gestionar tus archivos de forma privada y segura.</p>
</body>
</html>
