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

