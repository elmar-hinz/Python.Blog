======================
Django Setup Checklist
======================

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

* Start Project::

    django-admin startproject Project .

* Setup DB::

    python manage.py migrate
    python manage.py createsuperuser

* Start server::

    python manage.py runserver
    http://127.0.0.1:8000/admin/

Configure Project
-----------------

* Make directory `public/static`
* Register directories::

    vim Project/settings.py

    STATIC_ROOT = os.path.join(BASE_DIR, 'public', 'static')
    STATICFILES_DIRS = [ ]

SECURE PASSWORDS
----------------
* Install bcrypt::

    pip install bcrypt

* Install argon2::

    pip install django[argon2]

* Set password hashers::

    vim Project/settings.py

    # The first is for encryption. All are tried for decryption.
    PASSWORD_HASHERS = [
        'django.contrib.auth.hashers.Argon2PasswordHasher',
        'django.contrib.auth.hashers.PBKDF2PasswordHasher',
        'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
        'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
        'django.contrib.auth.hashers.BCryptPasswordHasher',
    ]

Enable Media Uploads
--------------------

* Install image library::

    pip install pillow

* Add Media template processor::

    vim Project/settings.py

    TEMPLATES = [
        {

        [...]

        'OPTIONS': {
                'context_processors': [
                    "django.template.context_processors.media",

        [...]


* Make directory `public/media`
* Register directories::

    vim Project/settings.py

    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'public', 'media')

* Enable media for development::

    vim Profile/urls.py

    urlpatterns = [
        ...
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

Basic App
---------

* Start app::

    python manage.py startapp BasicApp

* Register app to settings::

    vim Project/settings.py

    INSTALLED_APPS = [
        ...,
        'BasicApp',
    ]

* Make directory `BasicApp/static`
* Make directory `BasicApp/templates/BasicApp`
* Make `BasicApp/templates/BasicApp/base.html`
* Copy Bootstrap Starter Template::
    https://getbootstrap.com/docs/4.1/getting-started/introduction/
* Insert body block::

    {% block body %}
    {% endblock %}

* Make `BasicApp/templates/BasicApp/index.html`::

    {% extends 'BasicApp/base.html' %}
    {% block body %}
        <h1>Homepage</h1>
    {% endblock %}

* Edit `views.py` add index::

    from django.shortcuts import render

    # Create your views here.
    def index(request):
        return render(request, 'BasicApp/index.html')

* Make `BasicApp/urls.py`::

    from django.urls import path, include
    from . import views

    app_name='BaseApp'

    urlpatterns = [
        path('', views.index, name='index')
    ]

* Register app to urls ::

    vim Profile/urls.py

    import BasicApp.urls

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include(BasicApp.urls))
    ]

* Copy Bootstrap navigation::
    https://getbootstrap.com/docs/4.0/components/navbar/
* Edit Navbar to link to index page::

    href="{% url 'BaseApp:index' %}

Login Page
----------


