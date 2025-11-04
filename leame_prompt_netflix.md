Perfecto ✅ Veo que necesitas **todos los scripts y pasos completos** para crear el **proyecto “Netflix” en Django**, incluyendo la estructura, modelos, vistas, plantillas, URLs, y configuraciones.

A continuación te dejo **la guía paso a paso con los comandos y el código completo**, lista para copiar y ejecutar.

---

## 🧩 **PROYECTO: Netflix**

**Lenguaje:** Python
**Framework:** Django
**Editor:** Visual Studio Code

---

### **1️⃣ Crear carpeta del proyecto**

```bash
mkdir UIII_Netflix_0624
```

---

### **2️⃣ Abrir la carpeta en VS Code**

```bash
cd UIII_Netflix_0624
code .
```

---

### **3️⃣ Abrir terminal en VS Code**

En VS Code:

* Menú superior → **Terminal → New Terminal**

---

### **4️⃣ Crear entorno virtual**

```bash
python -m venv .venv
```

---

### **5️⃣ Activar entorno virtual**

* En Windows:

```bash
.venv\Scripts\activate
```

* En Linux/Mac:

```bash
source .venv/bin/activate
```

---

### **6️⃣ Activar intérprete de Python**

En VS Code → **Ctrl + Shift + P → Python: Select Interpreter → elegir .venv**

---

### **7️⃣ Instalar Django**

```bash
pip install django
```

---

### **8️⃣ Crear proyecto (sin duplicar carpeta)**

```bash
django-admin startproject backend_Netflix .
```

> El punto final evita crear una carpeta adicional.

---

### **9️⃣ Ejecutar servidor en el puerto 8024**

```bash
python manage.py runserver 8024
```

---

### **🔟 Copiar y pegar el link**

En el navegador abre:

```
http://127.0.0.1:8024/
```

---

### **1️⃣1️⃣ Crear aplicación `app_Netflix`**

```bash
python manage.py startapp app_Netflix
```

---

### **1️⃣2️⃣ Crear modelos en `app_Netflix/models.py`**

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

### **12.5️⃣ Migraciones**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### **1️⃣3️⃣ Trabajar solo con el modelo USUARIO por ahora**

---

### **1️⃣4️⃣ Crear funciones en `app_Netflix/views.py`**

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Usuario

def inicio_netflix(request):
    return render(request, "inicio.html")

def agregar_usuario(request):
    if request.method == "POST":
        nombre = request.POST["nombre"]
        apellido = request.POST["apellido"]
        email = request.POST["email"]
        tipo_membresia = request.POST["tipo_membresia"]
        pais = request.POST["pais"]
        Usuario.objects.create(nombre=nombre, apellido=apellido, email=email, tipo_membresia=tipo_membresia, pais=pais)
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

### **1️⃣5️⃣ Crear carpeta `templates` dentro de `app_Netflix`**

```bash
mkdir app_Netflix/templates
mkdir app_Netflix/templates/usuario
```

---

### **1️⃣6️⃣ Archivos HTML**

Crea dentro de `templates` los archivos:

* `base.html`
* `header.html`
* `navbar.html`
* `footer.html`
* `inicio.html`

---

### **1️⃣7️⃣ `base.html` (Bootstrap incluido)**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Netflix Admin{% endblock %}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
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
```

---

### **1️⃣8️⃣ `navbar.html`**

Incluye menús con íconos:

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#"><i class="bi bi-film"></i> Sistema de Administración Netflix</a>
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

### **1️⃣9️⃣ `footer.html`**

```html
<footer class="bg-dark text-white text-center fixed-bottom py-2">
    © <script>document.write(new Date().getFullYear())</script> Creado por Ing. Eliseo Nava, CBTIS 128
</footer>
```

---

### **2️⃣0️⃣ `inicio.html`**

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

### **2️⃣2️⃣ Archivos en `templates/usuario`**

Crea:

* `agregar_usuario.html`
* `ver_usuarios.html`
* `actualizar_usuario.html`
* `borrar_usuario.html`

(Puedo darte el contenido completo de cada uno si lo deseas 🔹).

---

### **2️⃣4️⃣ `urls.py` de `app_Netflix`**

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

### **2️⃣5️⃣ Agregar `app_Netflix` en `settings.py`**

```python
INSTALLED_APPS = [
    ...,
    'app_Netflix',
]
```

---

### **2️⃣6️⃣ Configurar `backend_Netflix/urls.py`**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')),
]
```

---

### **2️⃣7️⃣ Registrar modelos en `admin.py`**

```python
from django.contrib import admin
from .models import Usuario, Pelicula, ListaFavoritos

admin.site.register(Usuario)
admin.site.register(Pelicula)
admin.site.register(ListaFavoritos)
```

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### **3️⃣1️⃣ Ejecutar el servidor**

```bash
python manage.py runserver 8024
```

---

¿Quieres que te deje también los **HTML completos del CRUD de usuarios** (`agregar_usuario.html`, `ver_usuarios.html`, etc.) con los botones y estilos Bootstrap?
Puedo generarlos listos para copiar y pegar.
