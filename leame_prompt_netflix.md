PROYECTO DJANGO: “Netflix”

Lenguaje: Python
Framework: Django
Editor: Visual Studio Code
Puerto: 8024

📁 ESTRUCTURA DEL PROYECTO
UIII_Netflix_0624/
│
├── .venv/                          ← Entorno virtual (no se sube a GitHub)
├── manage.py
├── db.sqlite3
│
├── backend_Netflix/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
│
└── app_Netflix/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── tests.py
    ├── views.py
    ├── urls.py
    └── templates/
        ├── base.html
        ├── header.html
        ├── navbar.html
        ├── footer.html
        ├── inicio.html
        └── usuario/
            ├── agregar_usuario.html
            ├── ver_usuarios.html
            ├── actualizar_usuario.html
            └── borrar_usuario.html

⚙️ PASOS DE CREACIÓN
# 1. Crear carpeta
mkdir UIII_Netflix_0624
cd UIII_Netflix_0624

# 2. Crear entorno virtual
python -m venv .venv

# 3. Activar entorno virtual
.venv\Scripts\activate  # (Windows)
# source .venv/bin/activate  # (Mac/Linux)

# 4. Instalar Django
pip install django

# 5. Crear proyecto sin duplicar carpeta
django-admin startproject backend_Netflix .

# 6. Crear app
python manage.py startapp app_Netflix

# 7. Migraciones iniciales
python manage.py makemigrations
python manage.py migrate

# 8. Ejecutar servidor
python manage.py runserver 8024

🧠 ARCHIVOS DEL PROYECTO
🔹 backend_Netflix/settings.py
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = 'django-insecure-cambia-esto-por-uno-real'
DEBUG = True
ALLOWED_HOSTS = []

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Netflix',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'backend_Netflix.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'backend_Netflix.wsgi.application'

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

LANGUAGE_CODE = 'es-es'
TIME_ZONE = 'America/Mexico_City'
USE_I18N = True
USE_TZ = True

STATIC_URL = 'static/'
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'

🔹 backend_Netflix/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')),
]

🔹 app_Netflix/models.py
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

🔹 app_Netflix/admin.py
from django.contrib import admin
from .models import Usuario, Pelicula, ListaFavoritos

admin.site.register(Usuario)
admin.site.register(Pelicula)
admin.site.register(ListaFavoritos)

🔹 app_Netflix/views.py
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

🔹 app_Netflix/urls.py
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

🎨 TEMPLATES
🔹 base.html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}Netflix Admin{% endblock %}</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">
</head>
<body class="bg-light">
  {% include 'navbar.html' %}
  <div class="container mt-4">
    {% block content %}{% endblock %}
  </div>
  {% include 'footer.html' %}
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

🔹 navbar.html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#"><i class="bi bi-film"></i> Sistema Netflix</a>
    <button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div id="navbarNav" class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a href="/" class="nav-link">Inicio</a></li>
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

🔹 footer.html
<footer class="bg-dark text-white text-center py-2 fixed-bottom">
  © <script>document.write(new Date().getFullYear())</script> Creado por Ing. Eliseo Nava, CBTIS 128
</footer>

🔹 inicio.html
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
  <h1 class="mb-4">Bienvenido al Sistema Netflix</h1>
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" width="300">
</div>
{% endblock %}

📄 CRUD DE USUARIOS
🔹 usuario/agregar_usuario.html
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Usuario</h2>
<form method="post">{% csrf_token %}
  <input type="text" name="nombre" class="form-control mb-2" placeholder="Nombre">
  <input type="text" name="apellido" class="form-control mb-2" placeholder="Apellido">
  <input type="email" name="email" class="form-control mb-2" placeholder="Correo">
  <input type="text" name="tipo_membresia" class="form-control mb-2" placeholder="Membresía">
  <input type="text" name="pais" class="form-control mb-2" placeholder="País">
  <button type="submit" class="btn btn-success">Guardar</button>
</form>
{% endblock %}

🔹 usuario/ver_usuarios.html
{% extends 'base.html' %}
{% block content %}
<h2>Usuarios Registrados</h2>
<table class="table table-striped">
  <thead><tr><th>Nombre</th><th>Email</th><th>País</th><th>Acciones</th></tr></thead>
  <tbody>
  {% for u in usuarios %}
    <tr>
      <td>{{ u.nombre }} {{ u.apellido }}</td>
      <td>{{ u.email }}</td>
      <td>{{ u.pais }}</td>
      <td>
        <a href="{% url 'actualizar_usuario' u.id_usuario %}" class="btn btn-warning btn-sm">Editar</a>
        <a href="{% url 'borrar_usuario' u.id_usuario %}" class="btn btn-danger btn-sm">Borrar</a>
      </td>
    </tr>
  {% endfor %}
  </tbody>
</table>
{% endblock %}

🔹 usuario/actualizar_usuario.html
{% extends 'base.html' %}
{% block content %}
<h2>Actualizar Usuario</h2>
<form method="post" action="{% url 'realizar_actualizacion_usuario' usuario.id_usuario %}">{% csrf_token %}
  <input type="text" name="nombre" class="form-control mb-2" value="{{ usuario.nombre }}">
  <input type="text" name="apellido" class="form-control mb-2" value="{{ usuario.apellido }}">
  <input type="text" name="tipo_membresia" class="form-control mb-2" value="{{ usuario.tipo_membresia }}">
  <input type="text" name="pais" class="form-control mb-2" value="{{ usuario.pais }}">
  <button type="submit" class="btn btn-primary">Guardar cambios</button>
</form>
{% endblock %}

🔹 usuario/borrar_usuario.html
{% extends 'base.html' %}
{% block content %}
<h2>Eliminar Usuario</h2>
<p>¿Seguro que deseas eliminar a <strong>{{ usuario.nombre }} {{ usuario.apellido }}</strong>?</p>
<form method="post">{% csrf_token %}
  <button type="submit" class="btn btn-danger">Sí, eliminar</button>
  <a href="{% url 'ver_usuarios' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
