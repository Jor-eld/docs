		Índice Page
		Bookmarker Tutorial
		Conseguir CakePHP
		Comprobación de nuestra instalación
		La creación de la base de datos
		Configuración de base de datos
		Generación de Código Andamios
		Adición de Hashing de contraseñas
		Conseguir Marcadores con una etiqueta específica
		Crear el Método Buscador
		Creación de la Vista
		
 tutorial   Bookmarker 
 
 Este tutorial te guiará a través de la creación de una aplicación de marcadores simples (bookmarker). Para empezar, vamos a estar instalando CakePHP, la creación de nuestra base de datos, y utilizando las herramientas CakePHP ofrece para conseguir nuestra aplicación rápidamente.

Esto es lo que necesitas:

Un servidor de base de datos. Vamos a utilizar el servidor MySQL en este tutorial. Usted debe tener conocimento suficiente acerca de SQL con el fin de crear una base de datos: CakePHP tomará las riendas a partir de ahí. Ya que estamos usando MySQL, también asegurarse de que usted ha habilitado pdo_mysql  en PHP.
Conocimiento básico de PHP.
si deseas conocer la aplicacion final completa, cheka cakephp/bookmarker. si ya completas los requisitos pues manos a la obra!.

instalar CakePHP

La forma más fácil de instalar CakePHP es utilizar composer. Composer es una forma sencilla de instalar CakePHP desde el indicador de línea terminal o de comandos. En primer lugar, tendrás que descargar e instalar Composer si no lo has hecho ya. Si usted tiene cURL instalado, es tan fácil como ejecutar lo siguiente:

curl -s https://getcomposer.org/installer | php


O bien, puede descargar composer.phar desde el sitio web Composer.

Después, simplemente escriba la siguiente línea en el terminal desde el directorio de instalación para instalar las raices de CakePHP y montar el directorio del mismo:

php composer.phar create-project --prefer-dist cakephp/app bookmarker

Si ha descargado y ejecutado composer desde Windows Installer, a continuación, escriba la línea siguiente en el terminal desde el directorio de instalación (es decir, C:\Wamp\www\dev\cakephp3):

composer create-project --prefer-dist cakephp/app bookmarker

La ventaja de utilizar Composer es que completará automáticamente un conjunto importante las tareas tales como la definición de los permisos de archivo correcto y la creación de su archivo config/app.php.

Hay otras maneras de instalar CakePHP. Si usted no puede o no desea utilizar Composer, echa un vistazo a la instalación en la siguentes lineas de  sección.

Independientemente de cómo se ha descargado e instalado CakePHP, una vez que se termina su puesta en marcha, la configuración de su directorio debe ser algo como lo siguiente:

			
			/bookmarker
			    /bin
			    /config
			    /logs
			    /plugins
			    /src
			    /tests
			    /tmp
			    /vendor
			    /webroot
			    .editorconfig
			    .gitignore
			    .htaccess
			    .travis.yml
			    composer.json
			    index.php
			    phpunit.xml.dist
			    README.md
			    
Ahora puede ser un buen momento para aprender un poco sobre cómo funciona la estructura de directorios de CakePHP: echa un vistazo a la estructura de carpetas CakePHP en esta sección.

Comprobación de nuestra instalación


Podemos comprobar rápidamente que nuestra instalación es correcta, marcando la página de inicio predeterminada. Antes de que pueda hacer eso, usted necesita para iniciar el servidor de desarrollo:

bin/cake server

Para Windows, el comando tiene que ser bin\cake server (tenga en cuenta la barra invertida).

Esto iniciará el servidor web incorporado de PHP en el puerto 8765. Abra http://localhost:8765  en su navegador web para ver la página de bienvenida. Todas las viñetas deben ser distintas de las marcas de verificación CakePHP poder conectarse a su base de datos. Si no, puede que tenga que instalar extensiones PHP adicionales o permisos de directorio establecidos.

La creación de la base de datos


A continuación, vamos a configurar la base de datos para nuestra aplicación de marcadores. Si aún no lo ha hecho, cree una base de datos vacía para su uso en este tutorial, con un nombre de su elección, por ejemplo cake_bookmarks. Puede ejecutar el siguiente código SQL para crear las tablas necesarias:

				CREATE TABLE users (
				    id INT AUTO_INCREMENT PRIMARY KEY,
				    email VARCHAR(255) NOT NULL,
				    password VARCHAR(255) NOT NULL,
				    created DATETIME,
				    modified DATETIME
				);
				
				CREATE TABLE bookmarks (
				    id INT AUTO_INCREMENT PRIMARY KEY,
				    user_id INT NOT NULL,
				    title VARCHAR(50),
				    description TEXT,
				    url TEXT,
				    created DATETIME,
				    modified DATETIME,
				    FOREIGN KEY user_key (user_id) REFERENCES users(id)
				);
				
				CREATE TABLE tags (
				    id INT AUTO_INCREMENT PRIMARY KEY,
				    title VARCHAR(255),
				    created DATETIME,
				    modified DATETIME,
				    UNIQUE KEY (title)
				);
				
				CREATE TABLE bookmarks_tags (
				    bookmark_id INT NOT NULL,
				    tag_id INT NOT NULL,
				    PRIMARY KEY (bookmark_id, tag_id),
				    FOREIGN KEY tag_key(tag_id) REFERENCES tags(id),
				    FOREIGN KEY bookmark_key(bookmark_id) REFERENCES bookmarks(id)
				);
				
Usted puede haber notado que la tabla bookmarks_tags  utiliza una clave principal compuesta. CakePHP es compatible con claves primarias compuestas casi en todas partes, por lo que es más fácil construir aplicaciones multi-tenanted.

Los nombres de tablas y columnas que utilizamos no fueron arbitrarias. Mediante el uso de convenciones de nombres CakePHP, podemos aprovechar CakePHP mejor y evitar tener que configurar el marco. CakePHP es lo suficientemente flexible para adaptarse a esquemas de bases legado incluso incoherentes, pero la adhesión a las convenciones le ahorrará tiempo.

Configuración de base de datos
A continuación, vamos a decirle a CakePHP en nuestra base de datos y cómo conectarse a ella. Para muchos, esta será la primera y última vez que tendrá que configurar.

La configuración debe ser bastante sencillo: basta con sustituir los valores en el Datasources.default  en el archivo config/app.php  con las que se aplican a su configuración. Una matriz de configuración de ejemplo completado podría ser algo como lo siguiente:
				
				return [
				    // More configuration above.
				    'Datasources' => [
				        'default' => [
				            'className' => 'Cake\Database\Connection',
				            'driver' => 'Cake\Database\Driver\Mysql',
				            'persistent' => false,
				            'host' => 'localhost',
				            'username' => 'cakephp',
				            'password' => 'AngelF00dC4k3~',
				            'database' => 'cake_bookmarks',
				            'encoding' => 'utf8',
				            'timezone' => 'UTC',
				            'cacheMetadata' => true,
				        ],
				    ],
				    // More configuration below.
				];
				
Una vez que hayas guardado el archivo config/app.php , debería ver que CakePHP es capaz de conectarse a la base de datos de la sección tiene una marca de verificación.

Una copia del archivo de configuración por defecto de CakePHP se encuentra en config/app.default.php.

Generación de Código Scaffold

Debido a nuestra base de datos está siguiendo las convenciones de CakePHP, podemos utilizar la consola de aplicación bake  para generar rápidamente una aplicación básica. En la línea de comandos ejecute los siguientes comandos:

//  en windows tu nesecitaras usar la instancia bin\cake
bin/cake bake all users
bin/cake bake all bookmarks
bin/cake bake all tags

Esto generará los controladores, modelos, puntos de vista, sus casos de prueba correspondientes, y accesorios para nuestros usuarios, marcadores y recursos de etiquetas. Si usted ha detenido su servidor, reinícielo y vaya a http://localhost:8765/bookmarks.

Usted debe ver una aplicación que proporciona acceso a los datos básico pero funcional a las tablas de bases de datos de la aplicación. Una vez que estás en la lista de favoritos, añadir un usuarios, los marcadores y las etiquetas.

Si usted ve una error Página no encontrada (404) , confirme que se ha cargado el módulo mod_rewrite de Apache.

Adición de Hashing de contraseñas

Cuando creó sus usuarios, te habrás dado cuenta de que las contraseñas se almacenan en texto plano. Esto es bastante malo desde el punto de vista de seguridad, por lo que vamos a llegar a la establecida.

Este es también un buen momento para hablar de la capa del modelo en CakePHP. En CakePHP, separamos los métodos que operan sobre una colección de objetos, y un solo objeto en diferentes clases. Los métodos que operan en la colección de entidades se ponen en la tabla de clase, mientras que las características que pertenecen a un solo registro se ponen en la Entidad de clase.
