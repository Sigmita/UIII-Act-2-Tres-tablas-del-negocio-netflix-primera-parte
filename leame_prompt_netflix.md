Perfecto 👍 — aquí tienes **solo el procedimiento completo paso a paso en Visual Studio Code (VS Code)**, desde la creación del entorno virtual hasta la ejecución final del proyecto Django en el puerto **8024**, siguiendo el proyecto **UIII_Netflix_0624 (Netflix)**.

---

# 🧩 Procedimiento completo en Visual Studio Code (VS Code)

### 🎯 Objetivo

Crear un proyecto Django llamado **UIII_Netflix_0624**, con entorno virtual, configuración del proyecto **backend_Netflix**, aplicación **app_Netflix**, y ejecución del servidor local.

---

## 🪟 1. Crear carpeta del proyecto

1. Crea una carpeta con el nombre:

   ```
   UIII_Netflix_0624
   ```
2. Guarda esta carpeta en un lugar fácil de ubicar, por ejemplo en `Documentos` o en el Escritorio.

---

## 🧭 2. Abrir la carpeta en VS Code

1. Abre **Visual Studio Code**.
2. En el menú superior, selecciona:

   ```
   Archivo → Abrir carpeta...
   ```
3. Busca y selecciona la carpeta **UIII_Netflix_0624**.
4. Da clic en **Seleccionar carpeta**.
   (Ahora estarás trabajando dentro de esa carpeta).

---

## 💻 3. Abrir la terminal en VS Code

1. En el menú superior, selecciona:

   ```
   Ver → Terminal
   ```
2. Se abrirá la terminal integrada en la parte inferior del editor.
   (Asegúrate de que diga algo como `PS ...\\UIII_Netflix_0624>` o similar).

---

## 🌐 4. Crear entorno virtual `.venv`

En la terminal, ejecuta el siguiente comando:

```bash
python -m venv .venv
```

✅ Esto creará una carpeta llamada **.venv** dentro del proyecto.
Contiene el entorno aislado de Python.

---

## ⚡ 5. Activar el entorno virtual

Dependiendo del sistema operativo:

* **Windows (PowerShell):**

  ```powershell
  .\.venv\Scripts\Activate.ps1
  ```

* **Windows (CMD clásico):**

  ```cmd
  .\.venv\Scripts\activate
  ```

* **macOS / Linux:**

  ```bash
  source .venv/bin/activate
  ```

🔹 Si el entorno se activa correctamente, verás algo como:

```
(.venv) PS C:\Users\...\UIII_Netflix_0624>
```

---

## 🐍 6. Seleccionar el intérprete de Python en VS Code

1. Presiona `Ctrl + Shift + P` para abrir la **Paleta de Comandos**.
2. Escribe:

   ```
   Python: Select Interpreter
   ```
3. Selecciona el intérprete que muestre la ruta de tu entorno virtual, por ejemplo:

   ```
   .venv\Scripts\python.exe
   ```

---

## 🧱 7. Instalar Django

Con el entorno activado, ejecuta:

```bash
pip install django
```

Verifica que se instaló correctamente con:

```bash
django-admin --version
```

---

## 🚀 8. Crear el proyecto Django

Asegúrate de seguir en la carpeta **UIII_Netflix_0624**, y ejecuta:

```bash
django-admin startproject backend_Netflix .
```

> 🔸 Nota: El punto (`.`) al final evita crear una carpeta extra y deja los archivos dentro de `UIII_Netflix_0624`.

Deberás ver una estructura así:

```
UIII_Netflix_0624/
│─ backend_Netflix/
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  └─ wsgi.py
│
│─ manage.py
│─ .venv/
```

---

## ⚙️ 9. Ejecutar el servidor en el puerto 8024

En la terminal, escribe:

```bash
python manage.py runserver 8024
```

Luego abre tu navegador y entra al enlace que aparece, por ejemplo:

```
http://127.0.0.1:8024/
```

✅ Si ves la página de bienvenida de Django (“The install worked successfully!”), todo está correcto.

---

## 🎬 10. Crear la aplicación `app_Netflix`

Detén el servidor con `Ctrl + C` y ejecuta:

```bash
python manage.py startapp app_Netflix
```

Ahora verás esta estructura:

```
UIII_Netflix_0624/
│─ app_Netflix/
│  ├─ migrations/
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  ├─ tests.py
│  └─ __init__.py
│
│─ backend_Netflix/
│─ manage.py
```

---

## 🧩 11. Registrar la app en `settings.py`

Abre el archivo:

```
backend_Netflix/settings.py
```

Busca la sección `INSTALLED_APPS` y agrega `'app_Netflix',` al final de la lista:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Netflix',
]
```

Guarda los cambios.

---

## 🧾 12. Crear el modelo de datos (Usuario)

Abre `app_Netflix/models.py` y reemplaza su contenido con:

```python
from django.db import models

class Usuario(models.Model):
    id_usuario = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=255)
    apellido = models.CharField(max_length=255)
    email = models.CharField(max_length=255, unique=True)
    fecha_registro = models.DateField(auto_now_add=True)
    tipo_membresia = models.CharField(max_length=100)
    ultimo_acceso = models.DateTimeField(auto_now=True)
    pais = models.CharField(max_length=100)

    def __str__(self):
        return f"{self.nombre} {self.apellido} ({self.email})"
```

Guarda el archivo.

---

## ⚙️ 13. Crear y aplicar migraciones

Ejecuta los comandos:

```bash
python manage.py makemigrations
python manage.py migrate
```

Esto creará la base de datos `db.sqlite3`.

---

## 🧑‍💻 14. Registrar el modelo en `admin.py`

Abre `app_Netflix/admin.py` y agrega:

```python
from django.contrib import admin
from .models import Usuario

admin.site.register(Usuario)
```

---

## 🔐 15. Crear un superusuario (para entrar al panel admin)

Ejecuta:

```bash
python manage.py createsuperuser
```

Llena los datos (usuario, email y contraseña).
Ejemplo:

```
Username: admin
Email: admin@netflix.com
Password: 1234
```

---

## 🧭 16. Configurar `urls.py` del proyecto

Abre `backend_Netflix/urls.py` y edita para incluir las rutas de tu aplicación:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')),
]
```

---

## 🌐 17. Crear `urls.py` dentro de `app_Netflix`

Crea un nuevo archivo dentro de `app_Netflix` llamado `urls.py` con este contenido:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_netflix, name='inicio'),
]
```

---

## 🧠 18. Crear vista simple de inicio

Abre `app_Netflix/views.py` y coloca:

```python
from django.shortcuts import render

def inicio_netflix(request):
    return render(request, 'inicio.html')
```

---

## 🪶 19. Crear carpeta de plantillas (templates)

Dentro de `app_Netflix`, crea una carpeta llamada `templates`.

Adentro crea un archivo llamado `inicio.html` con este contenido:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Netflix - Inicio</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css">
</head>
<body class="bg-dark text-white text-center">
    <h1 class="mt-5">Bienvenido al sistema Netflix</h1>
    <p class="lead">Proyecto en Django — CBTis 128</p>
</body>
</html>
```

---

## 🏃‍♂️ 20. Ejecutar el servidor nuevamente

En la terminal:

```bash
python manage.py runserver 8024
```

Abre el navegador en:

```
http://127.0.0.1:8024/
```

✅ Si ves tu página de inicio personalizada, ¡el proyecto funciona correctamente!

---

## 🧩 21. Acceder al panel de administración

Entra a:

```
http://127.0.0.1:8024/admin/
```

Inicia sesión con tu superusuario creado anteriormente.
Allí podrás agregar, editar o eliminar usuarios del modelo `Usuario`.

---

## 🏁 22. Fin del procedimiento

Tu proyecto **UIII_Netflix_0624** está totalmente funcional.
Resumen de comandos usados:

```bash
python -m venv .venv
.\\.venv\\Scripts\\Activate.ps1
pip install django
django-admin startproject backend_Netflix .
python manage.py startapp app_Netflix
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver 8024
```

---

¿Quieres que ahora te lo deje en formato **Word o PDF** para entregar (con formato limpio, listas numeradas y comandos resaltados)? Puedo generarlo automáticamente para que solo tengas que subirlo.

