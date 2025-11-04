¡Claro! Aquí tienes la adaptación de tu proyecto para Netflix, incluyendo los pasos y el código necesarios.

Proyecto: Netflix.
Lenguaje: Python.
Framework: Django.
Editor: VS Code.

1. Procedimiento para crear carpeta del Proyecto: UIII_Netflix_0624

Abre tu terminal o símbolo del sistema y ejecuta:

code
Bash
download
content_copy
expand_less
mkdir UIII_Netflix_0624
cd UIII_Netflix_0624

2. Procedimiento para abrir VS Code sobre la carpeta UIII_Netflix_0624

Desde la terminal, estando dentro de la carpeta UIII_Netflix_0624, ejecuta:

code
Bash
download
content_copy
expand_less
code .

Esto abrirá VS Code con la carpeta del proyecto ya abierta.

3. Procedimiento para abrir terminal en VS Code

Dentro de VS Code, puedes ir a Terminal > New Terminal o usar el atajo Ctrl + Shift + (en Windows/Linux) o Cmd + Shift + (en macOS).

4. Procedimiento para crear carpeta entorno virtual ".venv" desde terminal de VS Code

En la terminal de VS Code, ejecuta:

code
Bash
download
content_copy
expand_less
python -m venv .venv

5. Procedimiento para activar el entorno virtual.

En Windows:

code
Bash
download
content_copy
expand_less
.venv\Scripts\activate

En macOS/Linux:

code
Bash
download
content_copy
expand_less
source .venv/bin/activate

Verás (.venv) al inicio de tu prompt, indicando que el entorno virtual está activo.

6. Procedimiento para activar intérprete de python.

Cuando el entorno virtual está activo (paso 5), el intérprete de Python dentro de ese entorno se activa automáticamente. VS Code suele detectarlo también. Si no, puedes seleccionarlo manualmente en la parte inferior de la ventana de VS Code.

7. Procedimiento para instalar Django

Con el entorno virtual activado, ejecuta:

code
Bash
download
content_copy
expand_less
pip install django

8. Procedimiento para crear proyecto backend_Netflix sin duplicar carpeta.

En la raíz de tu proyecto (asegúrate de que la terminal está en UIII_Netflix_0624 y el entorno virtual activado), ejecuta:

code
Bash
download
content_copy
expand_less
django-admin startproject backend_Netflix .

El . al final indica que quieres crear el proyecto en el directorio actual.

9. Procedimiento para ejecutar servidor en el puerto 8024

Para ejecutar el servidor, asegúrate de estar en la misma carpeta que manage.py (es decir, UIII_Netflix_0624). Luego, ejecuta:

code
Bash
download
content_copy
expand_less
python manage.py runserver 8024

10. Procedimiento para copiar y pegar el link en el navegador.

Una vez que el servidor esté corriendo, verás un mensaje como este en la terminal:

code
Code
download
content_copy
expand_less
Starting development server at http://127.0.0.1:8024/
Quit the server with CONTROL-C.

Copia http://127.0.0.1:8024/ y pégalo en tu navegador.

11. Procedimiento para crear aplicacion app_Netflix

Con el servidor detenido (Ctrl+C en la terminal) y el entorno virtual activo, ejecuta:

code
Bash
download
content_copy
expand_less
python manage.py startapp app_Netflix

12. Aquí el modelo models.py

Modifica el archivo app_Netflix/models.py para que contenga tus tablas:

code
Python
download
content_copy
expand_less
from django.db import models

# ==========================================
# MODELO: USUARIO
# ==========================================
class Usuario(models.Model):
    id_usuario = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=255)
    apellido = models.CharField(max_length=255)
    email = models.CharField(max_length=255, unique=True)
    fecha_registro = models.DateField(auto_now_add=True) # Usamos auto_now_add para la fecha de registro
    tipo_membresia = models.CharField(max_length=100)
    ultimo_acceso = models.DateTimeField(auto_now=True) # Usamos auto_now para la fecha de la última modificación
    pais = models.CharField(max_length=100)

    def __str__(self):
        return f"{self.nombre} {self.apellido} ({self.email})"

# ==========================================
# MODELO: PELICULA
# ==========================================
class Pelicula(models.Model):
    id_pelicula = models.AutoField(primary_key=True)
    titulo = models.CharField(max_length=255)
    ano_lanzamiento = models.IntegerField()
    genero = models.CharField(max_length=100)
    clasificacion_edad = models.CharField(max_length=50)
    duracion_minutos = models.IntegerField()
    director = models.CharField(max_length=255)
    idioma_original = models.CharField(max_length=50) # Nota: el PK en Django se maneja por 'id' por defecto. Si quieres este como PK, deberías usar 'primary_key=True'. Lo dejaremos como un campo normal y Django usará 'id_pelicula' como PK.

    def __str__(self):
        return f"{self.titulo} ({self.ano_lanzamiento})"

# ==========================================
# MODELO: LISTA_FAVORITOS
# ==========================================
class ListaFavoritos(models.Model):
    # Django creará un 'id' autoincremental por defecto para esta tabla, que será su PK.
    # No es necesario definir 'categoria_lista' como PK si ya tenemos 'id_usuario' y 'id_pelicula' como parte de un PK compuesto (implícitamente con Django).
    # La categoría_lista se puede manejar como un campo si es una clasificación dentro de la lista de favoritos.
    categoria_lista = models.IntegerField()
    usuario = models.ForeignKey(Usuario, on_delete=models.CASCADE, related_name='listas_favoritos')
    pelicula = models.ForeignKey(Pelicula, on_delete=models.CASCADE, related_name='en_listas_favoritos')
    fecha_adicion = models.DateField(auto_now_add=True)
    orden_en_lista = models.IntegerField()
    visto_en_lista = models.BooleanField(default=False)
    notas_personales = models.TextField(blank=True, null=True)

    class Meta:
        # Esto crea una clave primaria compuesta usando id_usuario y id_pelicula,
        # lo que asegura que un usuario no pueda tener la misma película más de una vez en su lista.
        unique_together = ('usuario', 'pelicula')

    def __str__(self):
        return f"Lista de {self.usuario.nombre} - {self.pelicula.titulo}"

12.5. Procedimiento para realizar las migraciones (makemigrations y migrate).

Asegúrate de tener el entorno virtual activo y de estar en el directorio que contiene manage.py.

code
Bash
download
content_copy
expand_less
python manage.py makemigrations app_Netflix
python manage.py migrate

13. Primero trabajamos con el MODELO: USUARIO

Dejaremos pendiente Pelicula y ListaFavoritos por ahora, como indicaste.

14. En view de app_Netflix crear las funciones con sus códigos correspondientes (inicio_netflix, agregar_usuario, actualizar_usuario, realizar_actualizacion_usuario, borrar_usuario)

Edita el archivo app_Netflix/views.py:

code
Python
download
content_copy
expand_less
from django.shortcuts import render, redirect, get_object_or_404
from .models import Usuario # Importa el modelo Usuario

# Función de inicio para Netflix
def inicio_netflix(request):
    return render(request, 'app_Netflix/inicio.html')

# Función para agregar un nuevo usuario
def agregar_usuario(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        apellido = request.POST['apellido']
        email = request.POST['email']
        # La fecha de registro y último acceso se autogeneran en el modelo
        tipo_membresia = request.POST['tipo_membresia']
        pais = request.POST['pais']

        Usuario.objects.create(
            nombre=nombre,
            apellido=apellido,
            email=email,
            tipo_membresia=tipo_membresia,
            pais=pais
        )
        return redirect('ver_usuarios') # Redirige a la lista de usuarios después de agregar
    return render(request, 'app_Netflix/usuario/agregar_usuario.html')

# Función para ver todos los usuarios
def ver_usuarios(request):
    usuarios = Usuario.objects.all()
    return render(request, 'app_Netflix/usuario/ver_usuarios.html', {'usuarios': usuarios})

# Función para actualizar un usuario (muestra el formulario)
def actualizar_usuario(request, pk):
    usuario = get_object_or_404(Usuario, pk=pk)
    return render(request, 'app_Netflix/usuario/actualizar_usuario.html', {'usuario': usuario})

# Función para realizar la actualización del usuario (maneja el POST)
def realizar_actualizacion_usuario(request, pk):
    if request.method == 'POST':
        usuario = get_object_or_404(Usuario, pk=pk)
        usuario.nombre = request.POST['nombre']
        usuario.apellido = request.POST['apellido']
        usuario.email = request.POST['email']
        usuario.tipo_membresia = request.POST['tipo_membresia']
        usuario.pais = request.POST['pais']
        usuario.save()
        return redirect('ver_usuarios')
    return redirect('ver_usuarios') # En caso de que se acceda directamente sin POST

# Función para borrar un usuario
def borrar_usuario(request, pk):
    usuario = get_object_or_404(Usuario, pk=pk)
    if request.method == 'POST':
        usuario.delete()
        return redirect('ver_usuarios')
    return render(request, 'app_Netflix/usuario/borrar_usuario.html', {'usuario': usuario})

15. Crear la carpeta "templates" dentro de "app_Netflix".

Dentro de app_Netflix, crea una carpeta llamada templates.

16. En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html).

app_Netflix/templates/base.html

app_Netflix/templates/header.html

app_Netflix/templates/navbar.html

app_Netflix/templates/footer.html

app_Netflix/templates/inicio.html

17. En el archivo base.html agregar bootstrap para css y js.

app_Netflix/templates/base.html:

code
Html
play_circle
download
content_copy
expand_less
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block titulo %}Netflix Admin{% endblock %}</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjMj13f6+6mYb+o+JAEptzAD+N6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous">
    <!-- Font Awesome para iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            background-color: #141414; /* Fondo oscuro tipo Netflix */
            color: #E5E5E5; /* Texto claro */
        }
        .content {
            flex: 1;
            padding-bottom: 70px; /* Espacio para el footer fijo */
        }
        .footer {
            position: fixed;
            bottom: 0;
            width: 100%;
            background-color: #0d0d0d; /* Fondo más oscuro para el footer */
            color: #777;
            padding: 15px 0;
            text-align: center;
            font-size: 0.9em;
        }
        .navbar {
            background-color: #1A1A1A; /* Navbar oscuro */
            border-bottom: 1px solid #333;
        }
        .navbar-brand, .nav-link, .dropdown-item {
            color: #E5E5E5 !important;
        }
        .navbar-brand:hover, .nav-link:hover, .dropdown-item:hover {
            color: #fff !important;
        }
        .dropdown-menu {
            background-color: #222;
            border: 1px solid #444;
        }
        .dropdown-item {
            color: #E5E5E5;
        }
        .dropdown-item:hover {
            background-color: #333;
        }
        h1, h2, h3, h4, h5, h6 {
            color: #E5E5E5;
        }
        .btn-primary {
            background-color: #E50914; /* Rojo Netflix */
            border-color: #E50914;
        }
        .btn-primary:hover {
            background-color: #F40612;
            border-color: #F40612;
        }
        .table {
            background-color: #222;
            color: #E5E5E5;
        }
        .table th {
            color: #fff;
            background-color: #333;
            border-color: #555;
        }
        .table td {
            border-color: #555;
        }
    </style>
</head>
<body>
    {% include 'app_Netflix/header.html' %}
    {% include 'app_Netflix/navbar.html' %}

    <div class="container mt-4 content">
        {% block contenido %}
        {% endblock %}
    </div>

    {% include 'app_Netflix/footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>

18. En el archivo navbar.html incluir las opciones (...)

app_Netflix/templates/navbar.html:

code
Html
play_circle
download
content_copy
expand_less
<nav class="navbar navbar-expand-lg">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_netflix' %}">
            <i class="fas fa-video"></i> Sistema de Administración Netflix
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="{% url 'inicio_netflix' %}">
                        <i class="fas fa-home"></i> Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownUsuarios" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-user"></i> Usuarios
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownUsuarios">
                        <li><a class="dropdown-item" href="{% url 'agregar_usuario' %}">Agregar Usuario</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_usuarios' %}">Ver Usuarios</a></li>
                        <!-- Opciones de actualizar y borrar se manejan desde ver_usuarios -->
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownPeliculas" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-film"></i> Películas
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownPeliculas">
                        <li><a class="dropdown-item" href="#">Agregar Película (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Ver Películas (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Película (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Película (Pendiente)</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownListas" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-list"></i> Listas de Favoritos
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownListas">
                        <li><a class="dropdown-item" href="#">Agregar a Lista (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Ver Listas (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Lista (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Borrar de Lista (Pendiente)</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>

19. En el archivo footer.html incluir derechos de autor, fecha del sistema y "Creado por Ing. Eliseo Nava, Cbtis 128" y mantenerla fija al final de la página.

app_Netflix/templates/footer.html:

code
Html
download
content_copy
expand_less
<footer class="footer">
    <div class="container">
        <span>&copy; 2024 Netflix Admin. Todos los derechos reservados. | Fecha del Sistema: <span id="currentDate"></span></span>
        <br>
        <span>Creado por Ing. Eliseo Nava, Cbtis 128</span>
    </div>
    <script>
        document.getElementById('currentDate').innerText = new Date().toLocaleDateString('es-ES', {
            year: 'numeric',
            month: 'long',
            day: 'numeric'
        });
    </script>
</footer>

20. En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre Netflix.

app_Netflix/templates/inicio.html:

code
Html
play_circle
download
content_copy
expand_less
{% extends 'app_Netflix/base.html' %}

{% block titulo %}Inicio - Netflix Admin{% endblock %}

{% block contenido %}
<div class="jumbotron text-center bg-dark p-5 rounded">
    <h1 class="display-4 text-danger">Bienvenido al Sistema de Administración de Netflix</h1>
    <p class="lead text-light">Gestiona tus usuarios, películas y listas de favoritos de forma eficiente.</p>
    <hr class="my-4 bg-secondary">
    <p class="text-secondary">Esta es una plataforma interna para la gestión y el control de los contenidos y usuarios de Netflix.</p>
    <p class="text-secondary">Utiliza el menú de navegación para acceder a las diferentes secciones.</p>
</div>

<div class="text-center mt-5">
    
</div>
{% endblock %}

Aquí te dejo una imagen que puedes usar:
`

21. Crear la subcarpeta carpeta usuario dentro de app_Netflix\templates.

Dentro de app_Netflix/templates, crea una carpeta llamada usuario.

22. Crear los archivos html con su codigo correspondientes de (agregar_usuario.html, ver_usuarios.html, actualizar_usuario.html, borrar_usuario.html) dentro de app_Netflix\templates\usuario.

app_Netflix/templates/usuario/agregar_usuario.html:

code
Html
play_circle
download
content_copy
expand_less
{% extends 'app_Netflix/base.html' %}

{% block titulo %}Agregar Usuario{% endblock %}

{% block contenido %}
<div class="card bg-dark text-light border-secondary">
    <div class="card-header bg-danger text-white">
        <h2 class="mb-0">Agregar Nuevo Usuario</h2>
    </div>
    <div class="card-body">
        <form method="post">
            {% csrf_token %}
            <div class="mb-3">
                <label for="nombre" class="form-label">Nombre:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="nombre" name="nombre" required>
            </div>
            <div class="mb-3">
                <label for="apellido" class="form-label">Apellido:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="apellido" name="apellido" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">Email:</label>
                <input type="email" class="form-control bg-secondary text-light border-dark" id="email" name="email" required>
            </div>
            <div class="mb-3">
                <label for="tipo_membresia" class="form-label">Tipo de Membresía:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="tipo_membresia" name="tipo_membresia" required>
            </div>
            <div class="mb-3">
                <label for="pais" class="form-label">País:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="pais" name="pais" required>
            </div>
            <button type="submit" class="btn btn-primary">Guardar Usuario</button>
            <a href="{% url 'ver_usuarios' %}" class="btn btn-outline-light">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}

app_Netflix/templates/usuario/ver_usuarios.html:

code
Html
play_circle
download
content_copy
expand_less
{% extends 'app_Netflix/base.html' %}

{% block titulo %}Ver Usuarios{% endblock %}

{% block contenido %}
<div class="d-flex justify-content-between align-items-center mb-4">
    <h2 class="mb-0">Lista de Usuarios</h2>
    <a href="{% url 'agregar_usuario' %}" class="btn btn-primary"><i class="fas fa-plus-circle"></i> Agregar Nuevo Usuario</a>
</div>

<div class="table-responsive">
    <table class="table table-dark table-striped table-hover">
        <thead>
            <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Apellido</th>
                <th>Email</th>
                <th>Membresía</th>
                <th>País</th>
                <th>Registro</th>
                <th>Último Acceso</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody>
            {% for usuario in usuarios %}
            <tr>
                <td>{{ usuario.id_usuario }}</td>
                <td>{{ usuario.nombre }}</td>
                <td>{{ usuario.apellido }}</td>
                <td>{{ usuario.email }}</td>
                <td>{{ usuario.tipo_membresia }}</td>
                <td>{{ usuario.pais }}</td>
                <td>{{ usuario.fecha_registro|date:"d M Y" }}</td>
                <td>{{ usuario.ultimo_acceso|date:"d M Y H:i" }}</td>
                <td>
                    <a href="{% url 'actualizar_usuario' usuario.id_usuario %}" class="btn btn-sm btn-info me-2" title="Editar"><i class="fas fa-edit"></i></a>
                    <a href="{% url 'borrar_usuario' usuario.id_usuario %}" class="btn btn-sm btn-danger" title="Borrar"><i class="fas fa-trash-alt"></i></a>
                </td>
            </tr>
            {% empty %}
            <tr>
                <td colspan="9" class="text-center">No hay usuarios registrados.</td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
</div>
{% endblock %}

app_Netflix/templates/usuario/actualizar_usuario.html:

code
Html
play_circle
download
content_copy
expand_less
{% extends 'app_Netflix/base.html' %}

{% block titulo %}Actualizar Usuario{% endblock %}

{% block contenido %}
<div class="card bg-dark text-light border-secondary">
    <div class="card-header bg-danger text-white">
        <h2 class="mb-0">Actualizar Usuario: {{ usuario.nombre }} {{ usuario.apellido }}</h2>
    </div>
    <div class="card-body">
        <form method="post" action="{% url 'realizar_actualizacion_usuario' usuario.id_usuario %}">
            {% csrf_token %}
            <div class="mb-3">
                <label for="nombre" class="form-label">Nombre:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="nombre" name="nombre" value="{{ usuario.nombre }}" required>
            </div>
            <div class="mb-3">
                <label for="apellido" class="form-label">Apellido:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="apellido" name="apellido" value="{{ usuario.apellido }}" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">Email:</label>
                <input type="email" class="form-control bg-secondary text-light border-dark" id="email" name="email" value="{{ usuario.email }}" required>
            </div>
            <div class="mb-3">
                <label for="tipo_membresia" class="form-label">Tipo de Membresía:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="tipo_membresia" name="tipo_membresia" value="{{ usuario.tipo_membresia }}" required>
            </div>
            <div class="mb-3">
                <label for="pais" class="form-label">País:</label>
                <input type="text" class="form-control bg-secondary text-light border-dark" id="pais" name="pais" value="{{ usuario.pais }}" required>
            </div>
            <button type="submit" class="btn btn-primary">Guardar Cambios</button>
            <a href="{% url 'ver_usuarios' %}" class="btn btn-outline-light">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}

app_Netflix/templates/usuario/borrar_usuario.html:

code
Html
play_circle
download
content_copy
expand_less
{% extends 'app_Netflix/base.html' %}

{% block titulo %}Borrar Usuario{% endblock %}

{% block contenido %}
<div class="card bg-dark text-light border-secondary">
    <div class="card-header bg-danger text-white">
        <h2 class="mb-0">Confirmar Borrado de Usuario</h2>
    </div>
    <div class="card-body">
        <p class="lead">¿Estás seguro de que quieres borrar al usuario <strong>{{ usuario.nombre }} {{ usuario.apellido }} ({{ usuario.email }})</strong>?</p>
        <p class="text-danger"><i class="fas fa-exclamation-triangle"></i> Esta acción no se puede deshacer.</p>

        <form method="post">
            {% csrf_token %}
            <button type="submit" class="btn btn-danger"><i class="fas fa-trash-alt"></i> Confirmar Borrado</button>
            <a href="{% url 'ver_usuarios' %}" class="btn btn-outline-light">Cancelar</a>
        </form>
    </div>
</div>
{% endblock %}

23. No utilizar forms.py.

Hemos implementado los formularios directamente en las plantillas HTML y manejado los datos POST en views.py.

24. Procedimiento para crear el archivo urls.py en app_Netflix con el código correspondiente para acceder a las funciones de views.py para operaciones de crud en usuarios.

Crea un archivo app_Netflix/urls.py y añade:

code
Python
download
content_copy
expand_less
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_netflix, name='inicio_netflix'),
    path('usuarios/agregar/', views.agregar_usuario, name='agregar_usuario'),
    path('usuarios/', views.ver_usuarios, name='ver_usuarios'),
    path('usuarios/actualizar/<int:pk>/', views.actualizar_usuario, name='actualizar_usuario'),
    path('usuarios/actualizar/realizar/<int:pk>/', views.realizar_actualizacion_usuario, name='realizar_actualizacion_usuario'),
    path('usuarios/borrar/<int:pk>/', views.borrar_usuario, name='borrar_usuario'),
]

25. Procedimiento para agregar app_Netflix en settings.py de backend_Netflix

Abre backend_Netflix/settings.py y busca la lista INSTALLED_APPS. Agrega 'app_Netflix' a la lista:

code
Python
download
content_copy
expand_less
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Netflix', # ¡Añade esta línea!
]

26. Realizar las configuraciones correspondiente a urls.py de backend_Netflix para enlazar con app_Netflix

Abre backend_Netflix/urls.py y modifica el archivo para incluir las URLs de tu aplicación:

code
Python
download
content_copy
expand_less
from django.contrib import admin
from django.urls import path, include # Importa include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Netflix.urls')), # Incluye las URLs de tu aplicación
]

27. Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones.

Edita app_Netflix/admin.py:

code
Python
download
content_copy
expand_less
from django.contrib import admin
from .models import Usuario, Pelicula, ListaFavoritos # Importa todos tus modelos

# Registra tus modelos aquí.
admin.site.register(Usuario)
admin.site.register(Pelicula)
admin.site.register(ListaFavoritos)

Después de registrar los modelos, realiza las migraciones de nuevo (solo makemigrations es estrictamente necesario si models.py no cambió desde la última makemigrations, pero es buena práctica):

code
Bash
download
content_copy
expand_less
python manage.py makemigrations
python manage.py migrate

27 (segundo). Por lo pronto solo trabajar con "usuario" dejar pendiente # MODELO: PELÍCULA y # MODELO: LISTA_FAVORITOS

Esto ya lo hemos implementado. Solo Usuario tiene vistas y URLs funcionales por ahora.

28. Utilizar colores suaves, atractivos y modernos, el código de las páginas web sencillas.

Los estilos de Bootstrap y los ajustes CSS en base.html están pensados para dar un aspecto oscuro, similar a Netflix, con colores rojo y grises.

28 (segundo). No validar entrada de datos.

No se han implementado validaciones de formularios en el lado del servidor o cliente, como solicitaste.

29. Al inicio crear la estructura completa de carpetas y archivos.

Hemos seguido un proceso paso a paso para crear la estructura. Si quieres una vista previa, debería ser algo así:

code
Code
download
content_copy
expand_less
UIII_Netflix_0624/
├── .venv/
├── backend_Netflix/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Netflix/
│   ├── migrations/
│   │   └── __init__.py
│   ├── templates/
│   │   ├── app_Netflix/
│   │   │   ├── base.html
│   │   │   ├── footer.html
│   │   │   ├── header.html
│   │   │   ├── inicio.html
│   │   │   └── navbar.html
│   │   │   └── usuario/
│   │   │       ├── agregar_usuario.html
│   │   │       ├── actualizar_usuario.html
│   │   │       ├── borrar_usuario.html
│   │   │       └── ver_usuarios.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
└── manage.py

30. Proyecto totalmente funcional.

Las funcionalidades CRUD para Usuario están completas y operativas. Las secciones de Películas y Listas de Favoritos están en el navbar pero como pendientes.

31. Finalmente ejecutar servidor en el puerto 8024.

Para ejecutar el servidor:

code
Bash
download
content_copy
expand_less
python manage.py runserver 8024

¡Espero que esto te sea de gran ayuda para tu proyecto de Netflix!
