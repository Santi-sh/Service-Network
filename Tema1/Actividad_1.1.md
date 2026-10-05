# Paso 1: Instalar Apache y actualizar el firewall

Para la instalación solo requerimos de introducir dos comandos en el terminal ``sudo apt update`` y ``sudo apt install apache2``
(**Para ambas acciones se nos pedirá nuestra password**)

<img width="800" height="500" alt="imagen" src="https://github.com/user-attachments/assets/f8bc3fec-19eb-42c0-9dec-4a696333b400" />

# Paso 2: Instalar MySQL

Ahora que disponemos de un servidor web funcional, deberá instalar un sistema de base de datos para poder almacenar y gestionar los datos de su sitio. ``MySQL`` es un sistema de administración de bases de datos popular que se utiliza en entornos ``PHP``.

<img width="850" height="460" alt="imagen" src="https://github.com/user-attachments/assets/e165f3f0-52d0-4263-9543-bd5b243bcd4e" />

Cuando la instalación se complete, se recomienda ejecutar una secuencia de comandos de seguridad. Con esta secuencia de comandos se eliminarán algunos ajustes predeterminados poco seguros y se bloqueará el acceso a su sistema de base de datos. 
Iniciamos la secuencia de comandos interactiva ejecutando lo siguiente: ``sudo mysql_secure_installation``

<img width="553" height="199" alt="imagen" src="https://github.com/user-attachments/assets/d938eba1-4ffb-46d9-956f-bd853b1ae960" />

Elija ``Y`` para indicar que sí, o cualquier otra cosa para continuar sin la habilitación y se le solicitará que seleccione un nivel de validación de contraseña, en nuestro caso elegiremos el nivel ``LOW`` escribiendo ``0``.

<img width="734" height="440" alt="imagen" src="https://github.com/user-attachments/assets/6003c620-ca38-4253-9120-62aa93ab14d9" />

Cuando termine, compruebe si puede iniciar sesión en la consola de MySQL al escribir lo siguiente: ``sudo mysql``

<img width="509" height="209" alt="imagen" src="https://github.com/user-attachments/assets/c5e62ddb-be18-4732-9412-963c2daa6a3c" />

# Paso 3: Instalar PHP

``PHP`` es el componente de nuestra configuración que procesará el código para mostrar contenido dinámico al usuario final.

Necesitaremos ``php-mysql``, un módulo PHP que permite que este se comunique con bases de datos basadas en MySQL y ``libapache2-mod-php`` para habilitar Apache para gestionar archivos PHP.

<img width="842" height="236" alt="imagen" src="https://github.com/user-attachments/assets/baa5ff6b-e1a4-4ad6-a37f-0491f3f512f2" />

Una vez que la instalación se complete, podrá ejecutar el siguiente comando para confirmar su versión de PHP: ``php -v``

<img width="447" height="109" alt="imagen" src="https://github.com/user-attachments/assets/f94b2890-ce5b-4767-a8e5-1d6a9fc51c2a" />

# Paso 4: Crear un host virtual para su sitio web

``Ubuntu 20.04`` tiene habilitado un bloque de servidor por defecto, que está configurado para proporcionar documentos del directorio ``/var/www/html``.

Creamos el directorio para your_domain de la siguiente manera:

<img width="642" height="85" alt="imagen" src="https://github.com/user-attachments/assets/bc8f661d-2799-4122-a5eb-2e562109154c" />

A continuación, asignamos la propiedad del directorio con la variable de entorno ``$USER``, que hará referencia a su usuario de sistema actual: ``sudo chown -R $USER:$USER /var/www/your_domain``

Y luego, abra un nuevo archivo de configuración en el directorio ``sites-available`` de ``Apache`` usando el editor de línea de comandos que prefiera. En este caso, utilizaremos ``nano``:

<img width="724" height="82" alt="imagen" src="https://github.com/user-attachments/assets/5e187f69-3d65-4d68-a1b4-6e80c57fe2e6" />

De esta manera, se creará un nuevo archivo en blanco. Pegue la siguiente configuración básica:

<img width="414" height="200" alt="imagen" src="https://github.com/user-attachments/assets/adc1cd91-5e38-4c85-860b-7961099e1df3" />

Con esta configuración de ``VirtualHost``, le indicamos a Apache que proporcione ``your_domain`` usando ``/var/www/your_domain`` como directorio root web.

Ahora, puede usar ``a2ensite`` para habilitar el nuevo host virtual:

<img width="491" height="61" alt="imagen" src="https://github.com/user-attachments/assets/da83a640-2932-4682-b42f-9c4390425b26" />

Puede ser conveniente deshabilitar el sitio web predeterminado que viene instalado con Apache. Es necesario hacerlo si no se utiliza un nombre de dominio personalizado, dado que, en este caso, la configuración predeterminada de Apache sobrescribirá su host virtual. Para deshabilitar el sitio web predeterminado de Apache, escriba lo siguiente:

<img width="499" height="59" alt="imagen" src="https://github.com/user-attachments/assets/deb416c9-b557-4555-9bf8-ef79ca9dc283" />

Por último, vuelva a cargar Apache para que estos cambios surtan efecto:

<img width="204" height="31" alt="imagen" src="https://github.com/user-attachments/assets/8b929ded-fc5b-4e58-95b7-c067beecbbf9" />

Ahora, su nuevo sitio web está activo, pero el directorio root web ``/var/www/your_domain`` todavía está vacío. Cree un archivo ``index.html`` en esa ubicación para poder probar que el host virtual funcione según lo previsto:

<img width="235" height="20" alt="imagen" src="https://github.com/user-attachments/assets/7120a1d3-1af5-4aca-a079-cf7f76214405" />

Incluya el siguiente contenido en este archivo:

<img width="441" height="85" alt="imagen" src="https://github.com/user-attachments/assets/30bac58c-3db7-4669-9736-542c5293c8d0" />

Si desea cambiar este comportamiento, deberá editar el archivo ``/etc/apache2/mods-enabled/dir.conf`` y modificar el orden en el que el archivo ``index.php`` se enumera en la directiva ``DirectoryIndex``:

<img width="290" height="29" alt="imagen" src="https://github.com/user-attachments/assets/5b080b76-eb73-474c-af36-2dd3863a8044" />

Lo que debemos hacer es cambiar de lugar el ``index.php`` a primera posición y ``index.html`` en la segunda para que tenga prioridad el ``.php``.

<img width="576" height="108" alt="imagen" src="https://github.com/user-attachments/assets/7c613ccf-bc70-4aba-9c8d-2b5b86f681ba" />

Después de guardar y cerrar el archivo, deberá volver a cargar Apache para que los cambios surtan efecto:

<img width="198" height="19" alt="imagen" src="https://github.com/user-attachments/assets/595714db-44a2-4340-88fc-4e6756de3bae" />

# Paso 4: Probar el procesamiento de PHP en su servidor web

Cree un archivo nuevo llamado info.php dentro de su carpeta root web personalizada:

``nano /var/www/your_domain/info.php``

Con esto se abrirá un archivo vacío. Añada el siguiente texto, que es el código PHP válido, dentro del archivo:

<img width="502" height="111" alt="imagen" src="https://github.com/user-attachments/assets/1f45c9f4-8e6a-45bd-aa35-af2190dda975" />

Cuando termine, guarde y cierre el archivo.

Para probar esta secuencia de comandos, diríjase a su navegador web y acceda al nombre de dominio o la dirección IP de su servidor, seguido del nombre de la secuencia de comandos, que en este caso es ``info.php``:

``http://server_domain_or_IP/info.php``

Tras comprobar la información pertinente sobre su servidor ``PHP`` a través de esa página, es recomendable que elimine el archivo que creó, dado que contiene información confidencial sobre su entorno ``PHP`` y su servidor de ``Ubuntu``. Puede usar ``rm`` para hacerlo: 

<img width="248" height="18" alt="imagen" src="https://github.com/user-attachments/assets/cd6e9912-a0cb-4a59-b530-7ca05531824c" />

Siempre puedes recrear esta página si necesitas acceder a la información posteriormente.

# Paso 6: Probar la conexión con la base de datos desde PHP (opcional)






















