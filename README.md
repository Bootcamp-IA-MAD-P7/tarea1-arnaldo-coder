# tarea1-arnaldo-coder
Tarea: Investigación y Desarrollo de un CRUD con Django

# 📚 Django, CRUD y Arquitecturas Web

> Guía de referencia sobre conceptos fundamentales de desarrollo web con Django.

---

## Tabla de contenidos

1. [¿Qué es un CRUD?](#1-qué-es-un-crud)
2. [Patrones de arquitectura](#2-patrones-de-arquitectura-en-desarrollo-de-software)
3. [Estructura de un proyecto Django](#3-estructura-de-un-proyecto-django)
4. [Flujo de datos: formulario → base de datos](#4-flujo-de-datos-formulario-html--base-de-datos-en-django)
5. [Herramientas y comandos de Django](#5-herramientas-y-comandos-de-django-para-crud)
6. [El Admin de Django](#6-cómo-funciona-el-admin-de-django)
7. [Django y REST](#7-django-usa-rest-qué-es-django-rest-framework)

---

## 1. ¿Qué es un CRUD?

**CRUD** es el acrónimo de las cuatro operaciones básicas sobre datos persistentes:

| Letra | Operación | Método HTTP |
|-------|-----------|-------------|
| **C** | Create (Crear) | `POST` |
| **R** | Read (Leer) | `GET` |
| **U** | Update (Actualizar) | `PUT` / `PATCH` |
| **D** | Delete (Eliminar) | `DELETE` |

Su propósito es proporcionar una interfaz completa para gestionar datos en una aplicación. Casi cualquier sistema que maneje información necesita estas cuatro operaciones; son la base sobre la que se construye la lógica de negocio.

### Ejemplo: Aplicación de gestión de tareas (To-Do List)

| Operación | Acción en la app |
|-----------|-----------------|
| Create | Crear una nueva tarea |
| Read | Ver la lista de tareas |
| Update | Editar el texto o estado de una tarea |
| Delete | Eliminar una tarea |

---

## 2. Patrones de arquitectura en desarrollo de software

Los **patrones de arquitectura** son soluciones reutilizables y probadas para organizar la estructura general de un sistema de software. Definen cómo se dividen las responsabilidades entre los distintos componentes, facilitando el mantenimiento, la escalabilidad y el trabajo en equipo.

### MVC — Modelo, Vista, Controlador

Separa la aplicación en tres capas:

- **Modelo:** gestiona los datos y la lógica de negocio (base de datos).
- **Vista:** es lo que el usuario ve (interfaz visual).
- **Controlador:** intermediario que recibe la entrada del usuario, interactúa con el Modelo y decide qué Vista mostrar.

```
Usuario → Controlador → Modelo
                ↓
             Vista → Usuario
```

### MVT — Modelo, Vista, Template

Variación de MVC utilizada por Django:

- **Modelo:** igual que en MVC, gestiona los datos.
- **Vista:** actúa como el *controlador*; contiene la lógica y decide qué datos enviar.
- **Template:** capa de presentación (el HTML que ve el usuario), equivalente a la *Vista* de MVC.

```
Usuario → Vista (lógica) → Modelo
               ↓
           Template → Usuario
```

### Diferencias entre MVC y MVT

| Aspecto | MVC | MVT (Django) |
|---------|-----|--------------|
| Intermediario lógico | Controlador | Vista |
| Capa de presentación | Vista | Template |
| Manejo de URLs | El Controlador | Django lo hace automáticamente con `urls.py` |
| Frameworks que lo usan | Rails, Laravel, ASP.NET | Django |

### ¿Cuál usa Django?

Django implementa el patrón **MVT**. La diferencia clave es que Django gestiona internamente el enrutamiento de URLs, por lo que no se necesita un "Controlador" explícito como en MVC clásico.

---

## 3. Estructura de un proyecto Django

```
mi_proyecto/
│
├── manage.py               # CLI para gestionar el proyecto
├── mi_proyecto/
│   ├── settings.py         # Configuración global
│   ├── urls.py             # Rutas principales
│   └── wsgi.py
│
└── mi_app/
    ├── models.py           # Modelos (datos)
    ├── views.py            # Vistas (lógica)
    ├── urls.py             # Rutas de la app
    ├── forms.py            # Formularios
    ├── admin.py            # Registro en el admin
    └── templates/          # HTML (templates)
```

### Rol de cada componente

| Componente | Archivo | Responsabilidad |
|------------|---------|-----------------|
| **Modelos** | `models.py` | Definen la estructura de la base de datos mediante clases Python. Cada clase representa una tabla. |
| **Vistas** | `views.py` | Contienen la lógica. Reciben una petición HTTP, consultan el modelo si es necesario y retornan una respuesta. |
| **Templates** | `templates/` | Archivos HTML con sintaxis especial de Django para mostrar datos dinámicos. |
| **URLs** | `urls.py` | Mapean rutas web (ej. `/tareas/`) a funciones de vista específicas. |

### ¿Para qué se usa `{% %}` en los templates?

Las etiquetas `{% %}` son **template tags** y sirven para incluir **lógica** dentro del HTML.

```django
{# Bucle #}
{% for tarea in tareas %}
    <li>{{ tarea.nombre }}</li>
{% endfor %}

{# Condicional #}
{% if usuario.is_authenticated %}
    <p>Bienvenido</p>
{% endif %}

{# Generar una URL #}
{% url 'nombre_ruta' %}

{# Seguridad en formularios #}
{% csrf_token %}
```

> **Nota:** `{{ }}` (dobles llaves) muestra **variables**, mientras que `{% %}` ejecuta **lógica y control de flujo**.

---

## 4. Flujo de datos: formulario HTML → base de datos en Django

```
1. Usuario llena el formulario HTML
        ↓
2. El navegador envía una petición POST a una URL
        ↓
3. urls.py enruta la petición a una Vista
        ↓
4. La Vista recibe request.POST con los datos
        ↓
5. Se validan los datos con un ModelForm (form.is_valid())
        ↓
6. Si es válido → form.save() → Django ORM
        ↓
7. El ORM ejecuta el INSERT/UPDATE en la base de datos
        ↓
8. La Vista redirige o responde al usuario
```

### Ejemplo en código

```python
# views.py
def crear_tarea(request):
    if request.method == 'POST':
        form = TareaForm(request.POST)
        if form.is_valid():
            form.save()  # guarda en la BD
            return redirect('lista_tareas')
    else:
        form = TareaForm()
    return render(request, 'crear.html', {'form': form})
```

---

## 5. Herramientas y comandos de Django para CRUD

| Herramienta / Comando | ¿Para qué sirve? |
|-----------------------|-----------------|
| `django-admin startproject` | Crea la estructura inicial del proyecto |
| `python manage.py startapp` | Crea una nueva aplicación dentro del proyecto |
| `python manage.py makemigrations` | Genera los archivos de migración a partir de los cambios en los modelos |
| `python manage.py migrate` | Aplica las migraciones y crea/modifica las tablas en la BD |
| `python manage.py runserver` | Inicia el servidor de desarrollo local |
| `python manage.py createsuperuser` | Crea un usuario administrador |
| `python manage.py shell` | Abre una consola Python con el contexto del proyecto cargado |
| **`ModelForm`** | Genera formularios HTML automáticamente a partir de un modelo |
| **`Django Admin`** | Interfaz web automática para gestionar todos los modelos con CRUD incluido |
| **`Django ORM`** | Permite interactuar con la BD usando Python en lugar de SQL puro |

---

## 6. ¿Cómo funciona el Admin de Django?

El **Admin de Django** es una interfaz web de administración que se genera **automáticamente**. Con solo registrar un modelo, Django crea un panel completo con CRUD incluido.

### Pasos para usarlo

**1. Registrar el modelo en `admin.py`:**

```python
from django.contrib import admin
from .models import Tarea

admin.site.register(Tarea)
```

**2. Crear un superusuario:**

```bash
python manage.py createsuperuser
```

**3. Acceder al panel:**

```
http://127.0.0.1:8000/admin/
```

### ¿Qué ofrece el Admin automáticamente?

- Listado de todos los objetos del modelo
- Formularios para crear, editar y eliminar registros
- Búsqueda, filtros y paginación automáticos
- Gestión de usuarios y permisos

Se puede personalizar con `ModelAdmin` para controlar campos visibles, filtros laterales, acciones masivas, y más.

---

## 7. ¿Django usa REST? ¿Qué es Django Rest Framework?

Django por sí solo **no usa arquitectura REST** de forma nativa. Está diseñado para generar respuestas HTML completas (páginas web tradicionales con templates).

**REST (Representational State Transfer)** es un estilo arquitectónico para APIs donde los recursos se exponen mediante URLs y se manipulan con métodos HTTP, respondiendo normalmente con **JSON** en lugar de HTML.

### Django REST Framework (DRF)

Es una librería adicional que extiende Django para construir **APIs RESTful**.

| Componente DRF | Función |
|----------------|---------|
| **Serializers** | Convierten modelos Django a JSON (y viceversa). Equivalente a los `ModelForm`. |
| **ViewSets** | Vistas que agrupan toda la lógica CRUD de un recurso en una sola clase. |
| **Routers** | Generan las URLs de la API automáticamente. |
| **Authentication** | Soporte integrado para Token, JWT y Session auth. |
| **Browsable API** | Interfaz web para explorar y probar la API desde el navegador. |

### Comparativa de flujos

```
# Django solo (web tradicional)
Petición HTTP → Vista → Template HTML → Navegador

# Django + DRF (API)
Petición HTTP → ViewSet → Serializer → JSON → Cliente (React, móvil, etc.)
```

DRF es ideal cuando el **frontend está separado del backend** (React, Vue, apps móviles). Django puro es ideal para aplicaciones web donde el servidor genera el HTML directamente.

---

*Guía elaborada como referencia rápida para el desarrollo web con Django.*
