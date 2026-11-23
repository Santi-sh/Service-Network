# Actividad #5: Directiva Directory

## 1. Creación de los directorios `dir1` y `dir2`

Creamos ambos directorios dentro de la raíz web de Apache (`/var/www/html`) y un archivo `index.html` de prueba en cada uno:

```bash
sudo mkdir -p /var/www/html/dir1 /var/www/html/dir2
echo "Directorio 1" | sudo tee /var/www/html/dir1/index.html
echo "Directorio 2" | sudo tee /var/www/html/dir2/index.html
```

![Captura 1 - Creación de directorios en Ubuntu](capturas/01-creacion-directorios.png)
> *Demostración en terminal de la creación de `/var/www/html/dir1` y `/var/www/html/dir2` junto con el listado `ls -l`.*

---

## 2. Diferencia entre bloques de ejemplo y equivalencia con `Require`

### Bloque 1: `Order Deny,Allow`

```apache
<Directory /var/www/example1>
    Order Deny,Allow
    Deny from All
    Allow from 192.168.1.100
</Directory>
```

* **Explicación:** Al evaluarse primero `Deny` y después `Allow` (prevaleciendo la última regla que coincida), **niega el acceso a todos excepto a la IP `192.168.1.100`**.
* **Equivalencia en Apache 2.4:**

```apache
<Directory /var/www/example1>
    Require ip 192.168.1.100
</Directory>
```

### Bloque 2: `Order Allow,Deny`

```apache
<Directory /var/www/example1>
    Order Allow,Deny
    Deny from All
    Allow from 192.168.1.100
</Directory>
```

* **Explicación:** Se evalúan primero las reglas `Allow` y luego las `Deny` (el acceso está denegado por defecto y `Deny` siempre tiene prioridad). Al existir `Deny from All`, **niega el acceso a todos**.
* **Equivalencia en Apache 2.4:**

```apache
<Directory /var/www/example1>
    Require all denied
</Directory>
```

---

## 3. Reglas individuales de acceso para `dir1`

Todas estas directivas se aplican dentro de `<Directory /var/www/html/dir1>` en el archivo `/etc/apache2/sites-available/000-default.conf`, recargando el servicio con `sudo systemctl reload apache2` tras cada modificación.

### 3.1. Permitir acceso desde `10.3.0.100`

* **Apache 2.2 (obsoleto):** `Allow from 10.3.0.100`
* **Apache 2.4 (actual):** `Require ip 10.3.0.100`

![Captura 2 - Permitir IP 10.3.0.100](capturas/02-dir1-ip.png)
> *Configuración en `000-default.conf` y comprobación de acceso mediante terminal (`curl`).*

### 3.2. Permitir acceso desde `"marisma.intranet"`

* **Apache 2.2 (obsoleto):** `Allow from marisma.intranet`
* **Apache 2.4 (actual):** `Require host marisma.intranet`

![Captura 3 - Permitir host marisma.intranet](capturas/03-dir1-host.png)
> *Configuración `Require host marisma.intranet` aplicada y comprobada en Ubuntu.*

### 3.3. Permitir acceso desde cualquier subdominio de `"marisma.intranet"`

* **Apache 2.2 (obsoleto):** `Allow from .marisma.intranet`
* **Apache 2.4 (actual):** `Require host .marisma.intranet`

![Captura 4 - Permitir subdominios de marisma.intranet](capturas/04-dir1-subdominios.png)
> *Configuración `Require host .marisma.intranet` en el archivo de Apache y recarga del servicio.*

### 3.4. Permitir acceso desde `"10.3.0.100"` con máscara `"255.255.0.0"`

* **Apache 2.2 (obsoleto):** `Allow from 10.3.0.100/255.255.0.0`
* **Apache 2.4 (actual):** `Require ip 10.3.0.100/255.255.0.0`

![Captura 5 - Permitir red con máscara 255.255.0.0](capturas/05-dir1-mascara.png)
> *Configuración aplicada y validación de sintaxis con `sudo apache2ctl configtest`.*

---

## 4. Modificación final de acceso para `dir1`

* **Requisito:** Permitir acceso a `"marisma.intranet"` y denegarlo desde `"10.3.0.101"`.
* **Configuración en Apache 2.4:**

```apache
<Directory /var/www/html/dir1>
    <RequireAll>
        Require host marisma.intranet
        Require not ip 10.3.0.101
    </RequireAll>
</Directory>
```

![Captura 6 - Configuración RequireAll en dir1](capturas/06-dir1-config-final.png)
> *Bloque `<RequireAll>` editado en `/etc/apache2/sites-available/000-default.conf`.*

![Captura 7 - Prueba de acceso en dir1](capturas/07-dir1-prueba-final.png)
> *Comprobación del funcionamiento de la restricción desde navegador o terminal.*

---

## 5. Modificación final de acceso para `dir2`

* **Requisito:** Permitir acceso a `"10.3.0.100/8"` y denegarlo a `"marisma.intranet"`.
* **Configuración en Apache 2.4:**

```apache
<Directory /var/www/html/dir2>
    <RequireAll>
        Require ip 10.3.0.100/8
        Require not host marisma.intranet
    </RequireAll>
</Directory>
```

![Captura 8 - Configuración RequireAll en dir2](capturas/08-dir2-config-final.png)
> *Bloque `<RequireAll>` configurado para `/var/www/html/dir2`.*

![Captura 9 - Prueba de acceso en dir2](capturas/09-dir2-prueba-final.png)
> *Verificación final de acceso a `dir2` desde la máquina virtual Ubuntu.*

