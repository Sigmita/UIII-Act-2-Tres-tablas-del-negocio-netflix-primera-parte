Perfecto ✅ — aquí tienes el **procedimiento completo en Visual Studio Code (VS Code)** **más la estructura final de todos los archivos y carpetas** del proyecto **UIII_Netflix_0624 (Netflix)**.

Todo está paso a paso desde cero hasta tener un proyecto Django funcional, incluyendo cómo debe quedar **cada archivo con su contenido final**.

---

# 🎬 **PROCEDIMIENTO COMPLETO EN VISUAL STUDIO CODE (VS CODE)**

### Proyecto: **UIII_Netflix_0624 (Netflix)**

**Lenguaje:** Python
**Framework:** Django
**Editor:** Visual Studio Code
**Servidor:** Puerto 8024

---

## 🧩 **1. Crear la carpeta del proyecto**

1. Crea una carpeta llamada:

   ```
   UIII_Netflix_0624
   ```
2. Guárdala en un lugar fácil de ubicar (por ejemplo, en Documentos o Escritorio).

---

## 💻 **2. Abrir la carpeta en VS Code**

1. Abre **Visual Studio Code**.
2. En el menú superior selecciona:

   ```
   Archivo → Abrir carpeta...
   ```
3. Selecciona la carpeta **UIII_Netflix_0624**.
4. Presiona **Seleccionar carpeta**.

---

## 🧠 **3. Abrir la terminal integrada**

En VS Code:

```
Ver → Terminal
```

(Se abrirá una terminal en la parte inferior del editor.)

---

## ⚙️ **4. Crear el entorno virtual `.venv`**

En la terminal escribe:

```bash
python -m venv .venv
```

Esto crea la carpeta `.venv` con el entorno de Python.

---

## ⚡ **5. Activar el entorno virtual**

Según tu sistema operativo:

* **Windows (PowerShell):**

  ```powershell
  .\.venv\Scripts\Activate.ps1
  ```
* **Windows (CMD clásico):**

  ```cmd
  .\.venv\Scripts\activate
  ```
* **macOS/Linux:**

  ```bash
  source .venv/bin/activate
  ```

Deberás ver al inicio de la terminal algo como:

```
(.venv) PS C:\Users\alex\UIII_Netflix_0624>
```

---

## 🐍 **6. Seleccionar intérprete de Python**

Presiona `Ctrl + Shift + P` → busca y elige:

```
Python: Select Interpreter
```

Selecciona el que dice `.venv`.

---

## 📦 **7. Instalar Django**

En la terminal (con el entorno activado):

```bash
pip install django
```

Verifica la instalación:

```bash
django-admin --version
```

---

## 🚀 **8. Crear el proyecto principal**

Desde la carpeta `UIII_Netflix_0624`, ejecuta:

```bash
django-admin startproject backend_Netflix .
```

> 🔹 El punto al final evita que Django cree una subcarpeta extra.

---

## 🧱 **9. Ejecutar el servidor**

```bash
python manage.py runserver 8024
```

Abre en el navegador:

```
http://127.0.0.1:8024/
```

✅ Si ves la página de Django, todo está correcto.

---

## 🎞️ **10. Crear la aplicación app_Netflix**

Detén el servidor (`Ctrl + C`) y ejecuta:

```bash
python manage.py startapp app_Netflix
```

---

## 🧩 **11. Registrar la aplicación**

Abre `backend_Netflix/settings.py`
Busca `INSTALLED_APPS` y agrega `'app_Netflix',`:

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

---

## 👤 **12. Crear el modelo Usuario**

Abre `app_Netflix/models.py` y reemplaza su contenido por:

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

---

## 🧾 **13. Crear migraciones**

Ejecuta:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🔑 **14. Crear superusuario**

```bash
python manage.py createsuperuser
```

Introduce usuario, correo y contraseña.

---

## 🧭 **15. Registrar modelo en el panel admin**

Abre `app_Netflix/admin.py` y agrega:

```python
from django.contrib import admin
from .models import Usuario

admin.site.register(Usuario)
```

---

## 🧱 **16. Crear vistas (views.py)**

Abre `app_Netflix/views.py` y coloca:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Usuario

def inicio_netflix(request):
    return render(request, 'inicio.html')

def agregar_usuario(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        apellido = request.POST['apellido']
        email = request.POST['email']
        tipo_membresia = request.POST['tipo_membresia']
        pais = request.POST['pais']
        Usuario.objects.create(
            nombre=nombre,
            apellido=apellido,
            email=email,
            tipo_membresia=tipo_membresia,
            pais=pais
        )
        return redirect('ver_usuarios')
    return render(request, 'usuario/agregar_usuario.html')

def ver_usuarios(request):
    usuarios = Usuario.objects.all()
    return render(request, 'usuario/ver_usuarios.html', {'usuarios': usuarios})

def actualizar_usuario(request, id_usuario):
    usuario = get_object_or_404(Usuario, id_usuario=id_usuario)
    return render(request, 'usuario/actualizar_usuario.html', {'usuario': usuario})

def realizar_actualizacion_usuario(request, id_usuario):
    usuario = get_object_or_404(Usuario, id_usuario=id_usuario)
    if request.method == 'POST':
        usuario.nombre = request.POST['nombre']
        usuario.apellido = request.POST['apellido']
        usuario.email = request.POST['email']
        usuario.tipo_membresia = request.POST['tipo_membresia']
        usuario.pais = request.POST['pais']
        usuario.save()
        return redirect('ver_usuarios')
    return redirect('ver_usuarios')

def borrar_usuario(request, id_usuario):
    usuario = get_object_or_404(Usuario, id_usuario=id_usuario)
    if request.method == 'POST':
        usuario.delete()
        return redirect('ver_usuarios')
    return render(request, 'usuario/borrar_usuario.html', {'usuario': usuario})
```

---

## 🌐 **17. Crear URLs de la app**

Crea el archivo `app_Netflix/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_netflix, name='inicio'),
    path('usuarios/agregar/', views.agregar_usuario, name='agregar_usuario'),
    path('usuarios/', views.ver_usuarios, name='ver_usuarios'),
    path('usuarios/actualizar/<int:id_usuario>/', views.actualizar_usuario, name='actualizar_usuario'),
    path('usuarios/actualizar/realizar/<int:id_usuario>/', views.realizar_actualizacion_usuario, name='realizar_actualizacion_usuario'),
    path('usuarios/borrar/<int:id_usuario>/', views.borrar_usuario, name='borrar_usuario'),
]
```

Y en `backend_Netflix/urls.py` agrega:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')),
]
```

---

## 🧠 **18. Crear carpeta y archivos de plantillas**

Dentro de `app_Netflix`, crea:

```
templates/
├─ base.html
├─ header.html
├─ navbar.html
├─ footer.html
├─ inicio.html
└─ usuario/
   ├─ agregar_usuario.html
   ├─ ver_usuarios.html
   ├─ actualizar_usuario.html
   └─ borrar_usuario.html
```

---

### 🧩 **Archivo base.html**

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}Netflix{% endblock %}</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-dark text-light">
  {% include 'navbar.html' %}
  <div class="container mt-4">
    {% block content %}{% endblock %}
  </div>
  {% include 'footer.html' %}
</body>
</html>
```

---

### 🧩 **navbar.html**

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-danger">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🎬 Netflix Admin</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav me-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Usuarios</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_usuario' %}">Agregar Usuario</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_usuarios' %}">Ver Usuarios</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

---

### 🧩 **footer.html**

```html
<footer class="bg-secondary text-center text-white p-3 fixed-bottom">
  <small>© {% now 'Y' %} Creado por Ing. Eliseo Nava, CBTis 128</small>
</footer>
```

---

### 🧩 **inicio.html**

```html
{% extends 'base.html' %}
{% block title %}Inicio - Netflix{% endblock %}
{% block content %}
<h1>Bienvenido al sistema Netflix</h1>
<p class="lead">Proyecto Django — CRUD de usuarios</p>
<img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" width="200">
{% endblock %}
```

---

### 🧩 **agregar_usuario.html**

```html
{% extends 'base.html' %}
{% block title %}Agregar Usuario{% endblock %}
{% block content %}
<h2>Agregar Usuario</h2>
<form method="post">{% csrf_token %}
  <input type="text" name="nombre" placeholder="Nombre" class="form-control mb-2" required>
  <input type="text" name="apellido" placeholder="Apellido" class="form-control mb-2" required>
  <input type="email" name="email" placeholder="Correo" class="form-control mb-2" required>
  <input type="text" name="tipo_membresia" placeholder="Membresía" class="form-control mb-2" required>
  <input type="text" name="pais" placeholder="País" class="form-control mb-2" required>
  <button class="btn btn-danger">Guardar</button>
</form>
{% endblock %}
```

---

### 🧩 **ver_usuarios.html**

```html
{% extends 'base.html' %}
{% block title %}Ver Usuarios{% endblock %}
{% block content %}
<h2>Usuarios Registrados</h2>
<table class="table table-dark table-striped">
  <tr>
    <th>ID</th><th>Nombre</th><th>Email</th><th>Membresía</th><th>Acciones</th>
  </tr>
  {% for u in usuarios %}
  <tr>
    <td>{{ u.id_usuario }}</td>
    <td>{{ u.nombre }} {{ u.apellido }}</td>
    <td>{{ u.email }}</td>
    <td>{{ u.tipo_membresia }}</td>
    <td>
      <a href="{% url 'actualizar_usuario' u.id_usuario %}" class="btn btn-warning btn-sm">Editar</a>
      <a href="{% url 'borrar_usuario' u.id_usuario %}" class="btn btn-danger btn-sm">Borrar</a>
    </td>
  </tr>
  {% endfor %}
</table>
{% endblock %}
```

---

### 🧩 **actualizar_usuario.html**

```html
{% extends 'base.html' %}
{% block title %}Actualizar Usuario{% endblock %}
{% block content %}
<h2>Actualizar Usuario</h2>
<form method="post" action="{% url 'realizar_actualizacion_usuario' usuario.id_usuario %}">{% csrf_token %}
  <input type="text" name="nombre" value="{{ usuario.nombre }}" class="form-control mb-2" required>
  <input type="text" name="apellido" value="{{ usuario.apellido }}" class="form-control mb-2" required>
  <input type="email" name="email" value="{{ usuario.email }}" class="form-control mb-2" required>
  <input type="text" name="tipo_membresia" value="{{ usuario.tipo_membresia }}" class="form-control mb-2" required>
  <input type="text" name="pais" value="{{ usuario.pais }}" class="form-control mb-2" required>
  <button class="btn btn-success">Actualizar</button>
</form>
{% endblock %}
```

---

### 🧩 **borrar_usuario.html**

```html
{% extends 'base.html' %}
{% block title %}Borrar Usuario{% endblock %}
{% block content %}
<h2>¿Deseas borrar este usuario?</h2>
<p>{{ usuario.nombre }} {{ usuario.apellido }} - {{ usuario.email }}</p>
<form method="post">{% csrf_token %}
  <button class="btn btn-danger">Sí, borrar</button>
  <a href="{% url 'ver_usuarios' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

---

## 🗂️ **ESTRUCTURA FINAL COMPLETA**

```
UIII_Netflix_0624/
│
├─ .venv/
│
├─ backend_Netflix/
│  ├─ __init__.py
│  ├─ settings.py
│  ├─ urls.py
│  ├─ asgi.py
│  └─ wsgi.py
│
├─ app_Netflix/
│  ├─ migrations/
│  │   └─ __init__.py
│  ├─ templates/
│  │   ├─ base.html
│  │   ├─ navbar.html
│  │   ├─ footer.html
│  │   ├─ inicio.html
│  │   └─ usuario/
│  │       ├─ agregar_usuario.html
│  │       ├─ ver_usuarios.html
│  │       ├─ actualizar_usuario.html
│  │       └─ borrar_usuario.html
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ urls.py
│  └─ views.py
│
├─ db.sqlite3
├─ manage.py
└─ README.md
```

---

## 🏁 **23. Ejecutar el servidor**

```bash
python manage.py runserver 8024
```

Entra en tu navegador:
👉 `http://127.0.0.1:8024/`

✅ Listo. Tu sistema **Netflix (UIII_Netflix_0624)** está completamente funcional con CRUD de usuarios.

---

¿Quieres que te lo entregue ahora en **Word o PDF** con formato de guía (listas, comandos resaltados, código en recuadros)?
Puedo generarlo automáticamente listo para entregar.
