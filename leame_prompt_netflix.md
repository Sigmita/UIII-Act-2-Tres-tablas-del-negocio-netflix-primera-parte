Perfecto 👌 aquí tienes **TODO el proyecto completo junto**, listo para copiar, pegar y ejecutar en tu máquina o subir a **GitHub**.
Incluye los pasos, estructura de carpetas, código fuente de todos los archivos y las configuraciones necesarias.

---

# 🎬 **Proyecto Django – Netflix (Primera Parte)**

**Lenguaje:** Python
**Framework:** Django
**Editor:** VS Code

---

## 🧱 **PASOS PARA CREAR EL PROYECTO**

```bash
# 1️⃣ Crear carpeta del proyecto
mkdir UIII_Netflix_0624
cd UIII_Netflix_0624

# 2️⃣ Abrir en VS Code
code .

# 3️⃣ Abrir terminal en VS Code
# (Menú superior → Terminal → New Terminal)

# 4️⃣ Crear entorno virtual
python -m venv .venv

# 5️⃣ Activar entorno virtual
# Windows:
.venv\Scripts\activate
# Linux/Mac:
source .venv/bin/activate

# 6️⃣ Seleccionar intérprete en VS Code
# (Ctrl + Shift + P → "Python: Select Interpreter" → elegir .venv)

# 7️⃣ Instalar Django
pip install django

# 8️⃣ Crear proyecto SIN duplicar carpeta
django-admin startproject backend_Netflix .

# 9️⃣ Ejecutar servidor en puerto 8024
python manage.py runserver 8024

# 10️⃣ Abrir en el navegador
# http://127.0.0.1:8024/

# 11️⃣ Crear aplicación
python manage.py startapp app_Netflix
```

---

## 📁 **ESTRUCTURA FINAL DEL PROYECTO**

```
UIII_Netflix_0624/
│
├── .venv/
│
├── backend_Netflix/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
│
├── app_Netflix/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   ├── urls.py
│   ├── migrations/
│   │   └── __init__.py
│   └── templates/
│       ├── base.html
│       ├── navbar.html
│       ├── footer.html
│       ├── inicio.html
│       └── usuario/
│           ├── agregar_usuario.html
│           ├── ver_usuarios.html
│           ├── actualizar_usuario.html
│           └── borrar_usuario.html
│
├── db.sqlite3
├── manage.py
├── .gitignore
└── README.md
```

---

## ⚙️ **ARCHIVOS PRINCIPALES**

### `app_Netflix/models.py`

```python
from django.db import models

# ==============================
# MODELO: USUARIO
# ==============================
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


# ==============================
# MODELO: PELICULA
# ==============================
class Pelicula(models.Model):
    id_pelicula = models.AutoField(primary_key=True)
    titulo = models.CharField(max_length=255)
    ano_lanzamiento = models.IntegerField()
    genero = models.CharField(max_length=100)
    clasificacion_edad = models.CharField(max_length=50)
    duracion_minutos = models.IntegerField()
    director = models.CharField(max_length=255)
    idioma_original = models.CharField(max_length=50)

    def __str__(self):
        return f"{self.titulo} ({self.ano_lanzamiento})"


# ==============================
# MODELO: LISTA_FAVORITOS
# ==============================
class ListaFavoritos(models.Model):
    categoria_lista = models.IntegerField()
    usuario = models.ForeignKey(Usuario, on_delete=models.CASCADE, related_name='listas_favoritos')
    pelicula = models.ForeignKey(Pelicula, on_delete=models.CASCADE, related_name='en_listas_favoritos')
    fecha_adicion = models.DateField(auto_now_add=True)
    orden_en_lista = models.IntegerField()
    visto_en_lista = models.BooleanField(default=False)
    notas_personales = models.TextField(blank=True, null=True)

    class Meta:
        unique_together = ('usuario', 'pelicula')

    def __str__(self):
        return f"Lista de {self.usuario.nombre} - {self.pelicula.titulo}"
```

---

### `app_Netflix/views.py`

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Usuario

def inicio_netflix(request):
    return render(request, "inicio.html")

def agregar_usuario(request):
    if request.method == "POST":
        Usuario.objects.create(
            nombre=request.POST["nombre"],
            apellido=request.POST["apellido"],
            email=request.POST["email"],
            tipo_membresia=request.POST["tipo_membresia"],
            pais=request.POST["pais"]
        )
        return redirect("ver_usuarios")
    return render(request, "usuario/agregar_usuario.html")

def ver_usuarios(request):
    usuarios = Usuario.objects.all()
    return render(request, "usuario/ver_usuarios.html", {"usuarios": usuarios})

def actualizar_usuario(request, id):
    usuario = get_object_or_404(Usuario, id_usuario=id)
    return render(request, "usuario/actualizar_usuario.html", {"usuario": usuario})

def realizar_actualizacion_usuario(request, id):
    usuario = get_object_or_404(Usuario, id_usuario=id)
    if request.method == "POST":
        usuario.nombre = request.POST["nombre"]
        usuario.apellido = request.POST["apellido"]
        usuario.tipo_membresia = request.POST["tipo_membresia"]
        usuario.pais = request.POST["pais"]
        usuario.save()
        return redirect("ver_usuarios")

def borrar_usuario(request, id):
    usuario = get_object_or_404(Usuario, id_usuario=id)
    if request.method == "POST":
        usuario.delete()
        return redirect("ver_usuarios")
    return render(request, "usuario/borrar_usuario.html", {"usuario": usuario})
```

---

### `app_Netflix/urls.py`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_netflix, name='inicio_netflix'),
    path('agregar/', views.agregar_usuario, name='agregar_usuario'),
    path('usuarios/', views.ver_usuarios, name='ver_usuarios'),
    path('actualizar/<int:id>/', views.actualizar_usuario, name='actualizar_usuario'),
    path('realizar_actualizacion/<int:id>/', views.realizar_actualizacion_usuario, name='realizar_actualizacion_usuario'),
    path('borrar/<int:id>/', views.borrar_usuario, name='borrar_usuario'),
]
```

---

### `backend_Netflix/settings.py`

Agrega tu app en la lista de `INSTALLED_APPS`:

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

### `backend_Netflix/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')),
]
```

---

### `app_Netflix/admin.py`

```python
from django.contrib import admin
from .models import Usuario, Pelicula, ListaFavoritos

admin.site.register(Usuario)
admin.site.register(Pelicula)
admin.site.register(ListaFavoritos)
```

---

## 🎨 **PLANTILLAS HTML**

### `templates/base.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}Netflix{% endblock %}</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
  {% include 'navbar.html' %}
  <div class="container mt-4">{% block content %}{% endblock %}</div>
  {% include 'footer.html' %}
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

### `templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🎬 Sistema de Administración Netflix</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li><a class="nav-link" href="/">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Usuarios</a>
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

### `templates/footer.html`

```html
<footer class="bg-dark text-white text-center py-2 fixed-bottom">
  © <script>document.write(new Date().getFullYear())</script> Creado por Ing. Eliseo Nava, CBTIS 128
</footer>
```

---

### `templates/inicio.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
  <h1>Bienvenido al Sistema Netflix</h1>
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" width="300">
</div>
{% endblock %}
```

---

### `templates/usuario/agregar_usuario.html`

```html
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Usuario</h2>
<form method="POST">
  {% csrf_token %}
  <input type="text" name="nombre" class="form-control mb-2" placeholder="Nombre">
  <input type="text" name="apellido" class="form-control mb-2" placeholder="Apellido">
  <input type="email" name="email" class="form-control mb-2" placeholder="Correo electrónico">
  <input type="text" name="tipo_membresia" class="form-control mb-2" placeholder="Tipo de membresía">
  <input type="text" name="pais" class="form-control mb-2" placeholder="País">
  <button class="btn btn-primary">Guardar</button>
</form>
{% endblock %}
```

---

### `templates/usuario/ver_usuarios.html`

```html
{% extends 'base.html' %}
{% block content %}
<h2>Lista de Usuarios</h2>
<table class="table table-striped">
  <thead>
    <tr><th>ID</th><th>Nombre</th><th>Email</th><th>Acciones</th></tr>
  </thead>
  <tbody>
    {% for u in usuarios %}
    <tr>
      <td>{{ u.id_usuario }}</td>
      <td>{{ u.nombre }} {{ u.apellido }}</td>
      <td>{{ u.email }}</td>
      <td>
        <a href="{% url 'actualizar_usuario' u.id_usuario %}" class="btn btn-warning btn-sm">Editar</a>
        <a href="{% url 'borrar_usuario' u.id_usuario %}" class="btn btn-danger btn-sm">Borrar</a>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

---

### `templates/usuario/actualizar_usuario.html`

```html
{% extends 'base.html' %}
{% block content %}
<h2>Actualizar Usuario</h2>
<form method="POST" action="{% url 'realizar_actualizacion_usuario' usuario.id_usuario %}">
  {% csrf_token %}
  <input type="text" name="nombre" class="form-control mb-2" value="{{ usuario.nombre }}">
  <input type="text" name="apellido" class="form-control mb-2" value="{{ usuario.apellido }}">
  <input type="text" name="tipo_membresia" class="form-control mb-2" value="{{ usuario.tipo_membresia }}">
  <input type="text" name="pais" class="form-control mb-2" value="{{ usuario.pais }}">
  <button class="btn btn-success">Actualizar</button>
</form>
{% endblock %}
```

---

### `templates/usuario/borrar_usuario.html`

```html
{% extends 'base.html' %}
{% block content %}
<h2>¿Eliminar usuario {{ usuario.nombre }} {{ usuario.apellido }}?</h2>
<form method="POST">
  {% csrf_token %}
  <button class="btn btn-danger">Eliminar</button>
  <a href="{% url 'ver_usuarios' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

---

## ⚙️ **Migraciones y ejecución final**

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py runserver 8024
```

📍 Abre en tu navegador:

```
http://127.0.0.1:8024/
```

---

¿Deseas que te genere este mismo contenido en un **ZIP con toda la estructura y archivos listos para abrir en VS Code**?
Puedo prepararlo para que solo lo descargues y ejecutes directamente.

