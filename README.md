# Learn Django 3.0

Following the [Guide](https://docs.djangoproject.com/en/3.0/)

## Other Resources

* [Django Packages](https://djangopackages.org/) aka Rails Gems / Engines

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

### Tutorial 02

[Creating models](https://docs.djangoproject.com/en/3.0/intro/tutorial02/#creating-models)

> Django follows the DRY Principle. The goal is to define your data model in one place and automatically derive things from it.

> This includes the migrations - unlike in Ruby On Rails, for example, migrations are entirely derived from your models file,
  and are essentially a history that Django can roll through to update your database schema to match your current models.

Create Migration of the polls model

```
$ python manage.py makemigrations polls
```

See SQL generated by migration

```
$ python manage.py sqlmigrate polls 0001
```

Migrate the model

```
python manage.py migrate
```
> The migrate command takes all the migrations that haven’t been applied (Django tracks which ones are applied using a special table
  in your database called django_migrations) and runs them against your database - essentially, synchronizing the changes you made
  to your models with the schema in the database.

### Playing with the api

[Playing with the api](https://docs.djangoproject.com/en/3.0/intro/tutorial02/#playing-with-the-api)

```
$ python manage.py shell
```

```
>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id

# Access model field values via Python attributes.
>>> q.question_text
>>> q.pub_date

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
```

### __str__()

> Wait a minute. <Question: Question object (1)> isn’t a helpful representation of this object.

```
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

> adding a __str__() method to both Question and Choice

```
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

### Custom method to model

In [polls/models](mysite/polls/models.py) add `was_published_recently`

```
import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

Starting `python manage.py shell`

```
# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)

>>> Question.objects.filter(question_text__startswith='What')

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
[# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
>>> q.choice_set.create(choice_text='The sky', votes=0)
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
>>> q.choice_set.count()

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```

### Django Admin

[Introducing Django Admin](https://docs.djangoproject.com/en/3.0/intro/tutorial02/#introducing-the-django-admin)

Creating an admin user

```
$ python manage.py createsuperuser
```

Start the development server

```
$ python manage.py runserver
```

Visit [Django Administration](http://127.0.0.1:8000/admin/)

### Make the poll app modifiable in the admin

[Make the poll app modifiable in the admin](https://docs.djangoproject.com/en/3.0/intro/tutorial02/#make-the-poll-app-modifiable-in-the-admin)

### Tutorial 03

[Writing more views](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#writing-more-views)

[Write Views that actually do something](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#write-views-that-actually-do-something)

> By convention DjangoTemplates looks for a “templates” subdirectory in each of the INSTALLED_APPS.

> [...] template should be at polls/templates/polls/index.html.

[A shortcut: render()](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#a-shortcut-render)

> The render() function takes the request object as its first argument, a template name as
  its second argument and a dictionary as its optional third argument.
  It returns an HttpResponse object of the given template rendered with the given context.

[Raising a 404 error](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#raising-a-404-error)

```
# polls/views.py
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

[A shortcut: get_object_or_404()](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#a-shortcut-get-object-or-404)

```
# polls/views.py
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```
> There’s also a get_list_or_404() function, which works just as get_object_or_404() – except using filter() instead of get().
  It raises Http404 if the list is empty.

[Use the template system](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#use-the-template-system)

[Removing hardcoded URLs in templates](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#removing-hardcoded-urls-in-templates)

Hardcoded URLs
```
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

Using `url` helper
```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

URL definition as specified in the polls.urls module in `polls/urls.py`
```
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/', views.detail, name='detail'),
```

[Namespacing URL names](https://docs.djangoproject.com/en/3.0/intro/tutorial03/#namespacing-url-names)

> For example, the polls app has a detail view, and so might an app on the same project that is for a blog.

> The answer is to add namespaces to your URLconf. [...] Add an app_name to set the application namespace

`polls/urls.py`

```
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    # ...
]
```

In `polls/templates/polls/index.html` change

```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

to
```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

### Tutorial 04

[Writing a minimal form](https://docs.djangoproject.com/en/3.0/intro/tutorial04/#write-a-minimal-form)

In `polls/templates/pools/detail.html`

* forloop.counter indicates how many times the for tag has gone through its loop

* all POST forms that are targeted at internal URLs should use the {% csrf_token %} template tag.

In `polls/views.py`

* request.POST['choice'] returns the ID

* request.POST values are always strings

* request.GET for accessing GET data in the same way

* request.POST['choice'] will raise KeyError if choice wasn’t provided in POST data

* HttpResponseRedirect takes a single argument: the URL to which the user will be redirected

* always return an HttpResponseRedirect after successfully dealing with POST data.

* using the reverse() function in the HttpResponseRedirect constructor. This function helps avoid having to hardcode a URL in the view function.
  reverse() call will return a string like '/polls/3/results/'

* [Avoid race-conditions using F()](https://docs.djangoproject.com/en/3.0/ref/models/expressions/#f-expressions)

[Use generic views: Less code is better](https://docs.djangoproject.com/en/3.0/intro/tutorial04/#use-generic-views-less-code-is-better)

* Each generic view needs to know what model it will be acting upon. This is provided using the model attribute.

* By default, the DetailView generic view uses a template called <app name>/<model name>_detail.html.

* The template_name attribute is used to tell Django to use a specific template name instead of the autogenerated default template name.

* Similarly, the ListView generic view uses a default template called <app name>/<model name>_list.html;

* For DetailView the question variable is provided automatically – since we’re using a Django model (Question), Django is able to determine an appropriate name for the context variable.

* However, for ListView, the automatically generated context variable is question_list. To override this we provide the context_object_name attribute, specifying that we want to use latest_question_list instead.

### Tutorial 05

[Introducing automated testing](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#introducing-automated-testing)

[Why you need to create tests](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#why-you-need-to-create-tests)

* [Tests will saveyou time](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#tests-will-save-you-time)

* [Tests don’t just identify problems, they prevent them](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#tests-don-t-just-identify-problems-they-prevent-them)

* [Tests make your code more attractive](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#tests-make-your-code-more-attractive)

* [Tests help teams work together](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#tests-help-teams-work-together)

[Writing our first test](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#writing-our-first-test)

Since things in the future are not ‘recent’, this is clearly wrong.

```
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
```

* the testing system will automatically find tests in any file whose name begins with test.

[Create a test to expose the bug](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#create-a-test-to-expose-the-bug)

```
$ python manage.py test polls
```

[Fixing the bug](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#fixing-the-bug)

[More comprehensive tests](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#more-comprehensive-tests)

### The Django test client

[Test a view](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#test-a-view)

```
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

* setup_test_environment() installs a template renderer which will allow us to examine some additional attributes on responses such as response.context that otherwise wouldn’t be available.

```
>>> from django.test import Client
>>> # create an instance of the client for our use
>>> client = Client()
```

```
>>> # get a response from '/'
>>> response = client.get('/')
Not Found: /
>>> # we should expect a 404 from that address; if you instead see an
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably
>>> # omitted the setup_test_environment() call described earlier.
>>> response.status_code
404
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#x27;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>
```

### Improving our view

[Improving our view](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#improving-our-view)


### Testing our new view

[Testing our new view](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#testing-our-new-view)

### Testing the DetailView

[Testing the DetailView](https://docs.djangoproject.com/en/3.0/intro/tutorial05/#testing-the-detailview)

### Tutorial 06

[Customize your app’s look and feel](https://docs.djangoproject.com/en/3.0/intro/tutorial06/#customize-your-app-s-look-and-feel)

* django.contrib.staticfiles -  it collects static files from each of your applications (and any other places you specify) into a single location

* STATICFILES_FINDERS setting contains a list of finders that know how to discover static files from various sources

* AppDirectoriesFinder which looks for a “static” subdirectory in each of the INSTALLED_APPS,

Create `polls/static/polls/style.css`

Edit `polls/templates/polls/index.html`

```
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

The {% static %} template tag generates the absolute URL of static files.

### Tutorial 07

[Customize the admin form](https://docs.djangoproject.com/en/3.0/intro/tutorial07/#customize-the-admin-form)

Edit `polls/admin.py` to make the “Publication date” come before the “Question” field

```
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

Using fieldsets

```
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]
```

[Adding related objects](https://docs.djangoproject.com/en/3.0/intro/tutorial07/#adding-related-objects)

Edit `polls/admin.py`

```
from django.contrib import admin

from .models import Choice, Question


class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

* With that TabularInline (instead of StackedInline), the related objects are displayed in a more compact, table-based format:

```
class ChoiceInline(admin.TabularInline):
    #...
```

[Customize the admin change list](https://docs.djangoproject.com/en/3.0/intro/tutorial07/#customize-the-admin-change-list)

Edit `polls/admin.py`

```
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

* Sorting, except the was_published_recently header, because sorting by the output of an arbitrary method is not supported.

Edit `polls/models.py`

```
class Question(models.Model):
    # ...
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

Edit `polls/admin.py` to display filters using the list_filter.

```
# QuestionAdmin
list_filter = ['pub_date']
```

Adding search capability

```
search_fields = ['question_text']
```

[Customize the admin look and feel](https://docs.djangoproject.com/en/3.0/intro/tutorial07/#customize-the-admin-look-and-feel)

Edit `mysite/settings.py`

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
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
```

Finding Django source files

```
$ python -c "import django; print(django.__path__)"
```

* Template path is `[os.path.join(BASE_DIR, 'templates')]`

* Now create a directory called admin inside templates, and copy the template admin/base_site.html
  from within the default Django admin template directory in the source code of
  Django itself (django/contrib/admin/templates) into that directory.


## Advanced tutorial: How to write reusable apps

[Advanced tutorial: How to write reusable apps](https://docs.djangoproject.com/en/3.0/intro/reusable-apps/#advanced-tutorial-how-to-write-reusable-apps)

Install [setuptools](https://pypi.org/project/setuptools/)

```
pip install setuptools
```

Create these files in the root of the git repository:

```
$ mkdir -p django-polls
$ touch django-polls/README.rst django-polls/LICENSE django-polls/setup.cfg django-polls/setup.py
$ cp -r mysite/polls django-polls/
```

Edit `django-polls/setup.cfg`

```
[metadata]
name = django-polls
version = 0.1
description = A Django app to conduct Web-based polls.
long_description = file: README.rst
url = https://www.example.com/
author = Your Name
author_email = yourname@example.com
license = BSD-3-Clause  # Example license
classifiers =
    Environment :: Web Environment
    Framework :: Django
    Framework :: Django :: X.Y  # Replace "X.Y" as appropriate
    Intended Audience :: Developers
    License :: OSI Approved :: BSD License
    Operating System :: OS Independent
    Programming Language :: Python
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3 :: Only
    Programming Language :: Python :: 3.6
    Programming Language :: Python :: 3.7
    Programming Language :: Python :: 3.8
    Topic :: Internet :: WWW/HTTP
    Topic :: Internet :: WWW/HTTP :: Dynamic Content

[options]
include_package_data = true
packages = find:
```

Edit `django-polls/setup.py`

```
from setuptools import setup

setup()
```

* Only Python modules and packages are included in the package by default.

* To include additional files, we’ll need to create a MANIFEST.in file

Edit `django-polls/MANIFEST.in`

```
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
recursive-include docs *
```

* Recommended :+1: To include detailed documentation with your app.

```
$ mkdir -p django-polls/docs
$ echo 'recursive-include docs *' >> django-polls/MANIFEST.in
```

Building a package, run from inside django-polls).

```
$ cd django-polls && python setup.py sdist && cd -
```

[Using your own package](https://docs.djangoproject.com/en/3.0/intro/reusable-apps/#using-your-own-package)

Install the package

```
# inside `pipenv shell`
$ python -m pip install --user django-polls/dist/django-polls-0.1.tar.gz
```
