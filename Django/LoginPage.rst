==========
Login Page
==========


Secure passwords
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


Login page
----------
