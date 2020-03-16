# Learn Django 3.0
Following the [Guide](https://docs.djangoproject.com/en/3.0/)

### Setup Env

Prerequisite

* pipenv
* asdf

```
$ virtualenv python_env
```

```
$ source env_python/bin/activate
```

### Install Django

```
$ python -m pip install Django
```

```
$ python -m django --version
3.0.4
```

### Tutorial 01

[Creating a project](https://docs.djangoproject.com/en/3.0/intro/tutorial01/)

```
$ django-admin startproject mysite
```

Run server on a specific port

```
$ python manage.py runserver 8080
```

Migrate the database
```
$ python manage.py migrate
```

### Polls app

[Creating the polls app](https://docs.djangoproject.com/en/3.0/intro/tutorial01/#creating-the-polls-app)

```
$ python manage.py startapp polls
```

* Add a view [polls/views.py](/mysite/polls/views.py)
* Add url to [polls/urls.py](/mysite/polls/urls.py)
* Add app polls to mysite [mysite/urls.py](/mysite/mysite/urls.py)
* Add app polls to mysite [mysite/urls.py](/mysite/mysite/urls.py)

Verify
```
$ python manage.py runserver
```

Visit [http://localhost:8000/polls/](http://localhost:8000/polls/)
