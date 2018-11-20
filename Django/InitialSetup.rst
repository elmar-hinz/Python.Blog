====================
Django Initial Setup
====================

General philosophy
------------------

Keep the actual configuration slim. Put the basic assets of the project into an own
application package. The project can be managed and deployed like any Python package and
is neatly bundled together.

What goes into this basic app? CSS, HTML content, templates that overwrite other packages,
the basic routing setup.

It's a valid alternative to put this staff into the root directory of the project as
the project needs to be managed by GIT anyway. Both approaches are reasonable. This
checklist describes the setup with a basic application package.

The name of this package will be `base`.


Getting started
---------------

* Create project directory
* Change into project directory
* Create venv::

    python3 -m venv venv

* Source venv::

    source venv/bin/activate

* Upgrade pip::

    pip install --upgrade pip

* Install Django::

    pip install django

* Start Project:

    ::

        django-admin startproject project .

    The `project/` directory stores the main configuration files.
    You can give it any name. The name `project/` is my standard.

* Setup DB::

    python manage.py migrate
    python manage.py createsuperuser

* Start server::

    python manage.py runserver
    http://127.0.0.1:8000/admin/


Configure project
-----------------

* Setup Git:

    Either create it locally or on Github. Github offers to create a `.gitignore` file
    and a licence file. Don't create a README.

* Create README::

    touch README.rst

* Create or edit `.gitignore`::

    .idea
    venv
    ENV.py
    db.sqlite3
    *.pyc

* Create a secret local settings file `ENV.py`:

    ::

        DEBUG = True
        SECRET_KEY = 'TODO'
        ALLOWED_HOSTS = ['127.0.0.1']

    This are a few informations that shall not go into the Git Repository.

* Create a local SECRET_KEY on the Python Console::

    >>> import os
    >>> os.urandom(24)

* Load the ENV in `project/settings.py`:

    ::

        from ENV import SECRET_KEY, DEBUG, ALLOWED_HOSTS

    Replace any pre generated veriables of this types.


* Make directories:

    ::

        mkdir -p public/static/
        mkdir -p public/media/
        mkdir -p public/docs/
        mkdir -p private/

    The media directory serves assets like CSS or javascript.
    The static directory serves static HTML files if any.
    The optional docs direcotry may serve static Sphinx publications.
    The private directory stores private content to render like Sphinx repos.

* Register directories in `project/settings.py`:

    ::

        STATIC_URL = '/static/'
        STATIC_ROOT = os.path.join(BASE_DIR, 'public', 'static')

        MEDIA_URL = '/media/'
        MEDIA_ROOT = os.path.join(BASE_DIR, 'public', 'media')

        DOCS_URL = '/docs/'
        DOCS_ROOT = os.path.join(BASE_DIR, 'public', 'docs')

    These variables will be used in the file `urls.py` files for routing.

Base app
--------

* Start app::

    python manage.py startapp base

* Register app to settings:

    ::

        vim Project/settings.py

        INSTALLED_APPS = [
            ...,
            'base',
            'an overwritten app',
            ...
        ]

    Register `base` before the apps whose templates it is overwriting!!!

* Make directory `base/static/`:
    Files located here will be copied to `public/static/` upon deployment.
* Make directory `base/media/`:
    Files located here will be copied to `public/media/` upon deployment.
* Make directory `base/templates/base/`:
    Files in a `templates/base/` directory relative to the project root, can
    overwrite files in this directory. This is a common pattern in Django.
    It allows packages to overwrite the templates of each other and a final
    overwrite on the level of the project.
* Make `base/templates/base/base.html`:
    This will be the base template of all other templates.
* Copy Bootstrap Starter Template:
    https://getbootstrap.com/docs/4.1/getting-started/introduction/
* Insert main block::

    {% block main %}
    {% endblock %}

* Make `base/templates/base/index.html`::

    {% extends 'base/base.html' %}
    {% block main %}
        <h1>Homepage</h1>
    {% endblock %}

* Edit `base/views.py` add index::

    from django.shortcuts import render

    # Create your views here.
    def index(request):
        return render(request, 'base/index.html')

* Make `base/urls.py`:

    ::

        from django.contrib import admin
        from django.urls import path, include

        import base.views


        app_name='base'

        urlpatterns = [
                path('admin/', admin.site.urls),
                path('', base.views.index, name='index'),
        ]

* Register app to urls `project/urls.py`:

    ::

        from django.conf.urls.static import static
        from django.urls import path, include
        import base.urls
        from project import settings

        urlpatterns = [
            path('', include(base.urls))
        ] \
        + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) \
        + static(settings.DOCS_URL, document_root=settings.DOCS_ROOT) \
        + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

* Copy Bootstrap navigation::

    https://getbootstrap.com/docs/4.0/components/navbar/

* Edit Navbar to link to index page::

    href="{% url 'base:index' %}


Enable media uploads
--------------------

* Install image library::

    pip install pillow


* Add Media template processor::

    vim project/settings.py

    TEMPLATES = [
        {

        [...]

        'OPTIONS': {
                'context_processors': [
                    "django.template.context_processors.media",

        [...]

* Make sure following was created. See above!
    * MEDIA_URL variable
    * MEDIA_ROOT variable
    * `public/media/` directory
    * Media entries in `project/urls.py`

