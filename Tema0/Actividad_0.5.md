# Actividad 0.5 - Práctica servidor web con Python (2º ASIR)

Despliegue y prueba de tres servidores web ligeros utilizando **Python 3** en **Ubuntu**, documentando cada ejecución y su acceso desde el navegador.

---

## 0. Instalación y comprobación de Python en Ubuntu

Antes de lanzar los servidores, actualizamos los repositorios e instalamos Python 3 junto con `git` (necesario para clonar el segundo ejemplo):

```bash
sudo apt update
sudo apt install python3 git -y
python3 --version
```

### Evidencia de instalación en la máquina virtual
> *Sustituye la ruta de la imagen por la captura de tu terminal mostrando la versión de Python instalada.*

![Instalación de Python en Ubuntu](./img/00-instalacion-python.png)

---

## 1. Ejemplo 1: Simple Web Server (módulo nativo `http.server`)

Este servidor forma parte de la librería estándar de Python y permite servir de forma rápida los archivos del directorio actual mediante peticiones HTTP `GET` y `HEAD`.

* **Documentación oficial:** [http.server — HTTP servers](https://docs.python.org/3/library/http.server.html)

### Comandos ejecutados
Creamos una carpeta de prueba con un archivo HTML y levantamos el servidor en el puerto `8000`:

```bash
mkdir -p ~/practica-web/ejemplo1 && cd ~/practica-web/ejemplo1
echo "<h1>Hola desde Simple Web Server (Python)</h1>" > index.html
python3 -m http.server 8000
```

### Evidencias de funcionamiento
1. **Terminal** con el servidor escuchando en el puerto `8000` y recibiendo las peticiones HTTP (`200 OK`):
   ![Terminal Ejemplo 1](./img/01-ejemplo1-terminal.png)

2. **Navegador** accediendo a `http://localhost:8000` (o a la IP de la máquina virtual):
   ![Navegador Ejemplo 1](./img/01-ejemplo1-navegador.png)

---

## 2. Ejemplo 2: HTTP Server con subida y descarga (`simple_http_server`)

A diferencia del módulo nativo, este script añade una interfaz web que permite tanto descargar archivos como **subir ficheros** (de forma individual o por lotes) al servidor.

* **Repositorio:** [freelamb/simple_http_server](https://github.com/freelamb/simple_http_server)

### Comandos ejecutados
Clonamos el repositorio, entramos en el directorio y ejecutamos el servidor en el puerto `8000`:

```bash
cd ~/practica-web
git clone https://github.com
cd simple_http_server
python3 simple_http_server.py 8000
```

*(Nota: Por seguridad escucha solo en `127.0.0.1`; si se accede desde fuera de la MV, se añade `--bind 0.0.0.0`).*

### Evidencias de funcionamiento
1. **Terminal** ejecutando `simple_http_server.py` y mostrando el registro de subida/descarga:
   ![Terminal Ejemplo 2](./img/02-ejemplo2-terminal.png)

2. **Navegador** mostrando la interfaz web de subida y descarga de archivos:
   ![Navegador Ejemplo 2](./img/02-ejemplo2-navegador.png)

---

## 3. Ejemplo 3: Dummy Web Server (`socketserver` + `http.server`)

Implementación personalizada en un script Python que combina los módulos `socketserver.TCPServer` y `http.server.SimpleHTTPRequestHandler`, añadiendo control de excepciones (`try/except OSError`) por si el puerto `8000` ya está ocupado.

* **Código fuente (Gist):** [kabinpokhrel/server.py](https://gist.github.com/kabinpokhrel/6fd1275603e9d5f1e284be717cbd1bff)

### Código utilizado (`server.py`)

```python
import http.server as httpserver
import socketserver

def main(port=None):
    if port is None:
        port = 8000
    handler = httpserver.SimpleHTTPRequestHandler
    try:
        httpd = socketserver.TCPServer(("", port), handler)
        print("serving at port", port)
        httpd.serve_forever()
    except OSError:
        print("Given PORT:{} is unavailable. Try running with different PORT Number!".format(port))

if __name__ == '__main__':
    main()
```

### Comandos ejecutados

```bash
mkdir -p ~/practica-web/ejemplo3 && cd ~/practica-web/ejemplo3
# Guardamos el código anterior en server.py y lo ejecutamos:
python3 server.py
```

### Evidencias de funcionamiento
1. **Terminal** mostrando el mensaje `serving at port 8000` (y/o el aviso si el puerto está ocupado):
   ![Terminal Ejemplo 3](./img/03-ejemplo3-terminal.png)

2. **Navegador** mostrando el listado de directorios servido por `server.py`:
   ![Navegador Ejemplo 3](./img/03-ejemplo3-navegador.png)

