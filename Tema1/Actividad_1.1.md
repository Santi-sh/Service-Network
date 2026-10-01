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














