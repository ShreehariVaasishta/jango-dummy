# Create Project

`django-admin startproject learn_jango`


Structure of your project:

```python
learn_jango
├── learn_jango
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

You can run server by command:

`python manage.py runserver`


After that, you can navigate to `localhost:8000/` and `localhost:8000/admin`

# Create an app:

`python manage.py startapp blog`

Structure of your project:

```python
learn_jango
├── blog
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── learn_jango
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

Just after creating an app, always add app in `learn_jango/settings.py`'s `INSTALLED_APPS` variable by adding `'blog.apps.BlogConfig'`.

# Django URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/

Examples:

**Function views** 
	1. Add an import:  from my_app import views
	2. Add a URL to urlpatterns:  path('', views.home, name='home')


# Templates

All of your `html` files will go to `/learn_jango/blog/templates` directory. Inside `/blog`, create directory `/templates`, and inside `/templates`, create `/blog` directory.

All of your `css`, `js` or any static files will go to `/learn_jango/blog/static` directory. Inside `/blog`, create directory `/static`, and inside `/static`, create `/blog` directory.

```
.
├── blog
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── static
│   │   └── blog
│   ├── templates
│   │   └── blog
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── learn_jango
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

# Template syntax

Inside `templates/blog`, create `base.html` and `home.html`.

1. `{% load static %}` inside `base.html` will allow it to load static files exists in `static/blog` directory. 

2. `{% static 'blog/main.css' %}` inside `base.html` will load static files. For example 
	`<link rel="stylesheet" type="text/css" href="{% static 'blog/main.css' %}">`

3.  if-else in template:
	```python
	{% if title %}
        <title>Django Blog - {{ title }}</title>
    {% else %}
        <title>Django Blog</title>
    {% endif %}
    ```

4. `{% block content %}{% endblock %}` inside `base.html` allows other `html` pages to override this line. You can have multiple blocks with different names.

5. `{% extends "blog/base.html" %}` inside `home.html` will extends the `base.html`
	Note: We can extend `blog`'s `'base.html'` in another app like `'users/'`.

6. This will override the `content` "block" of `base.html`:
	```python
	{% block content %}
	    <h1>Home Page</h1>
	{% endblock content %}
	```

7. `{% url 'blog-about' %}` will return absolute URL path corresponding to name of `path` mentioned in `blog.urls.urlpatterns` variable. Output: `/blog/`

8. Formating Templates:
	`{{ post.date_posted|date:"F d, Y" }`
	More filters: [here](https://docs.djangoproject.com/en/2.1/ref/templates/builtins/#date)

9. `{% csrf_token %}` is a token, which is Django's hidden tag, must be include within `<form></form>` tag. It adds security and protect our form from certain attacks. CSRF stands for `Cross-site request forgery`

10. `{{ form }}` will render form within `<form></form>` tag. 
	`{{ form.as_p }}` can be used to render as paragraph `<p>`.

# Views

In `blog/views.py` you can create views, which contains logic when user hits particular url.
Create two views: `home` and `about`, by:

```python
def home(request):
	return render(request, 'blog/home.html', context)
```
Note that:
- The parameter of views should have `request`.
- `context` is a dictionary.
- You can use`key` as name of variable in templates.
- The `value` of the `key` is the value of variable.
- Variable can be iterable, for example `posts`.
- To loop over iterable `post`, which is `list` of `dictionary`:
	```python
	{% for post in posts %}
		<a href="#">{{ post.author }}</a>
		<small>{{ post.date_posted }}</small>
		<a href="#">{{ post.title }}</a>
		<p>{{ post.content }}</p>
	{% endfor %}
	```


# Admin 

- Before creating admin user, first migrate the database because `db.sqllite3` is empty now;so `auth_user` table not exist to store admin credentials.
`python manage.py migrate`

- Create SuperUser as:
`python manage.py createsuperuser`
and enter reqired credentials.

- A user have 3 kind of permissions:
	- Active
	- Staff status (login as admin, `<read>`)
	- Superuser status (login as admin, `<write, update>`)

# Database

- Django has it's own ORM `'Django ORM'`. With this, we can represent our databases as `classes`, these `classes` are called `models`. 

- A model class inherits the Django's `models.Model` class.

- The parameter in DateTimeField 
	- `auto_now=True` will set current time every-time when post is updated.
	- `auto_now_set=True` will set time only once when post is created, but it is un-updatetable.
	- `default=timezone.now` is updatable.

- One-to-many: One user, many post: for this we use `ForeignKey` as:
	```python
	author = models.ForeignKey(User, on_delete=models.CASCADE)
	```
	This will delete Posts on delete User.

- After creating models, make-migrations:
	`python manage.py makemigrations`

	Note: `makemigrations` only updates the `/blog/migrations/xxxx_yy.py` file, while `migrate` command runs this file.

- Before run `migrate` command, you can see SQL code which will be fired as:
	`python manage.py sqlmigrate blog xxxx`

# Django Python Shell

- `python manage.py shell` will allow to work with these models line by line.

```python
>>> from blog.models import Post
>>> from django.contrib.auth.models import User

>>> User.objects.all()
<QuerySet [<User: raghav>, <User: Virus>, <User: Virus_1>]>
>>> User.objects.first()
<User: raghav>
>>> User.objects.filter(username='Virus')
<QuerySet [<User: Virus>]>

>>> user = User.objects.filter(username='raghav').first()
>>> user.id
1
>>> user.pk # primarykey=id here
1
>>> user = User.objects.get(id=2)
>>> user
<User: Virus>

>>> Post.objects.all()
<QuerySet []>
>>> post_1 = Post(title='Blog 1', content='first content', author=user) # Virus' post
>>> Post.objects.all()
<QuerySet []>
>>> post_1.save()
>>> Post.objects.all()
<QuerySet [<Post: Post object (1)>]>

>>> # now defined __str__ method in models.py
>>> Post.objects.all()	# redable posts
<QuerySet [<Post: Blog 1>]>

>>> user = User.objects.get(id=2)

>>> post_2 = Post(title='Blog 2', content='Second Content', author_id=user.id)
>>> post_2.save()
>>> Post.objects.all()
<QuerySet [<Post: Blog 1>, <Post: Blog 2>]>

>>> post = Post.objects.first()
>>> post.date_posted
datetime.datetime(2018, 12, 20, 12, 35, 51, 32371, tzinfo=<UTC>)
>>> post.author
<User: Virus>
>>> post.author.email
'virus.mail@coma.com'

>>> user.post_set.all() # get all posts of 'Virus'
<QuerySet [<Post: Blog 1>, <Post: Blog 2>]>

>>> user.post_set.create(title='Blog 3', content='Content 3') # Another way to ctrate post, no need to specify author.
<Post: Blog 3>
>>> user.post_set.all()
<QuerySet [<Post: Blog 1>, <Post: Blog 2>, <Post: Blog 3>]>

```

# Register Models in `admin.py`

- In the `blog/admin.py`, you need to register your `Post` model, so that it can be dispalyed in the `localhost:8000/admin` GUI.
	```python
	from .models import Post
	admin.site.register(Post)
	```

- Now, you can edit the fields as well in `admin` GUI.

# Django Messages

- Django provides `messages` in the `django.contrib` module

- Kind of messages:
	```python
	messages.debug()
	messages.info()
	messages.success()
	messages.warning()
	messages.error()
	```

- Usage:
	```python
	messages.success(request, 'Success Message')
	return return redirect('blog-home')
	```

- The `messages.success` line before redirect will display message, but only once(not after refresh).

- Message Display in template:
	```python
    {% if messages %}
      {% for message in messages %}
        <div class='alert alert-{{ message.tags }}'>
          {{ message }}
        </div>
      {% endfor %}
    {% endif %}
	```

# Django Redirects

- `rdirect` avilible in `django.shortcut` module

- We can specify either absolute path or name of path as:
	```python
	return redirect('blog/')
	# OR
	return redirect('blog-home')
	```

# Django Forms

- Django provides forms in `django.contrib.auth.forms`, which provides classes such as `UserCreationForm` 

- We need to pass `UserCreationForm()` as a context to the registeration page.

- Check method and validity of form:
	```python
	if requst.method == 'POST':
		form = UserCreationForm(request.POST)
		if form.is_valid():
			message.success(request, 'Success Message')
			return redirect('blog-home')
	```

- Accesing values of forms(in POST method):
	```python
	username = form.cleaned_data.get('username')
	```
	Here, `form.cleaned_data` returns a `dict`.

- **Adding Custom Fields in `UserCreationForm`  class**:
	- create `forms.py` under `users/` app
	- make few imports in it:
	```python
	from django import forms
	from django.contrib.auth.models import User    # User Model
	from django.contrib.auth.forms import UserCreationForm  # custom form will inherit it
	```
	- Create custom `UserRegisterForm`:
	```python
	class UserRegisterForm(UserCreationForm):
		# additional fields goes here
		email = forms.EmailField()
		#Meta Class
		class Meta:
			model = User
			fields = ['username', 'email', 'password1', 'password2']
	```
	- `Meta` class gives nested namespace for configuration, and keeps configuration at one place. Here are the configurations:
		- `model = User` tells custom form to save credentials to `User` model, when we `form.save`.
		- `fields = []`. `fields` contains `list` we want in out custom form, and tells order of fields.

- Get rid of ugly default form: Curruntly our `{{ form }}` is rendered ugly:
	![ugly-form.png](https://github.com/raghav18gupta/raghav18gupta.github.io/raw/master/tuts/rawstaticimgs/Screenshot%20from%202018-12-20%2021-21-33.png)

	- `django-crispy-forms` is a third party tool to do this. Install it via pip: `pip install django-crispy-forms`

	- Tell django to add `cryspy-form` in project by adding line `'crispy_form'` in `learn_jango/`'s `settings.INSTALLED_APPS`

	- In the same file `settings.py`, add a variable `CRISPY_TEMPLATE_PACK = 'bootstrap4''`

	- In `users/template/users/`'s `registration.html`, `{% load crispy_forms_tags %}`

	- In same file, replace `{{ from.as_p }}` with filtered `{{ form|crispy }}`

	This steps are enough to get better forms:
	![better.png](https://github.com/raghav18gupta/raghav18gupta.github.io/raw/master/tuts/rawstaticimgs/Screenshot%20from%202018-12-20%2021-57-21.png)

	Not only form looks better, errors like 'invalid password' gives better feedback.