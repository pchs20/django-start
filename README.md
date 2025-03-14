# Introduction to Django
This guide is designed to help [Web Applications and Services](https://www.fib.upc.edu/en/studies/bachelors-degrees/bachelor-degree-informatics-engineering/curriculum/syllabus/ASW)
students begin their journey in creating their server-side web application project using
Django. It covers the core concepts and provides a simple example to get them started.


# What is Django?

Django is a high-level Python web framework that enables developers to build powerful web 
applications quickly and with minimal code.

## Components
Django follows the MVC (Model-View-Controller) architectural pattern, adapted as MTV 
(Model-Template-View) in Django. Here's a breakdown of each component:

- **Model**: Represents the data structure of your application. In Django, models are Python 
classes that map to database tables. They handle the interaction with databases, usually 
using relational databases like PostgreSQL, MySQL, or SQLite.

- **Template** (view in MVC): Defines how the data is presented to the user. Templates in 
Django use HTML and CSS with embedded Django Template Language (DTL) to render dynamic 
content.

- **View** (controller in MVC): Contains the business logic of the application. Views handle 
incoming HTTP requests, interact with models, and pass data to templates for rendering.

In addition to the core MVC components, Django provides:

- **URLs** (router): The URL configuration maps different URLs of your web application to 
corresponding views. This acts as a routing mechanism, determining which code should be 
executed based on the URL accessed by the user.

- **Migrations**: Django's system for managing database changes over time. When you
modify your models, migrations keep the database schema up-to-date.

- **Admin** interface: Django comes with a built-in admin interface that allows you to 
manage your application's data models without writing additional code. This powerful
feature lets you add, modify, and delete data directly from a user-friendly interface.

## Interaction among components

Here is a simple breakdown of how Django works:
1. The user navigates through the application to a certain URL. This is received for the
router (**URLs**).
2. The router calls the **view** that matches de URL.
3. The view checks for relevant **models**, retrieve the needed information or updates
them.
4. The view then sends the data to a specified **template**.
5. The template is rendered with its HTML, CSS and Django tags as a final HTML page, 
which is sent back to the browser and displayed to the user.

![Django MTC pattern structure](https://espifreelancer.com/images/Django_mtv.webp)


# Let's create an example project!

## Initial configurations
### Install Python and pip
- Install Python ~3.10 from [here](https://www.python.org/downloads/).
Verify the installation by doing:
  ```shell
  python3 --version  
  ```
- Install pip from [here](https://pip.pypa.io/en/stable/installation/).
Verify the installation by executing:
  ```shell
  pip --version  
  ```

### Python virtual environment
Virtual environments are isolated spaces where you can install and run specific versions 
of Python and its libraries, without affecting the global or system-wide Python 
installation. For this reason, it is very recommended to use them when developing in 
Python. So we will :)
- Navigate to the directory where you want to create the virtual environment (i.e. the
source root of your project).
- Once there, execute the following command to create a virtualenv named `.env`:
  ```shell
  python3 -m venv .env
  ```
  **Note**: Remember to add `.env` directory to the `.gitignore` file!


- Activate the virtual environment (if macOS or Linux):
  ```shell
  source .env/bin/activate
  ```
- Activate the virtual environment (if Windows):
  ```shell
  .env\Scripts\activate
  ```
From now on, when installing new packages or executing your project, make sure **you have
the virtual environment activated**!

### Install Django
- Execute:
  ```shell
  pip install django 
  ```
- Verify the installation with:
  ```shell
  python -m django --version
  ```

### Requirements file
To manage your project dependencies easily, use a requirements.txt file. This file lists 
all the Python packages your project needs. Therefore, **when installing a new package
or external library** (like we have just done with Django), execute:
```shell
pip freeze > requirements.txt
```
This lists all the dependencies inside the virtual environment into a `requirements.txt`
file. This makes it easy for other developers (such as your teammates) to update their
virtual environment by running:
```shell
pip install -r requirements.txt
```
Which installs all the requirements listed in the file to your activated virtual 
environment.


## Project start-up
Let's create a project named **library** that we will use as an example.
### Create project
Execute the following (note the `.` at the end of the command):
```shell
django-admin startproject library .
```

We should now have the following directory structure:
```
django-example
├── library
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── .env
├── manage.py
└── requirements.txt
```

### Create application
Django is very modular, so its projects are structured in 
applications. For example, we will define an application named `books`:
```shell
python manage.py startapp books 
```

At this point, we should have the following directory structure:
```
django-example
├── library
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── books
│   ├── migrations
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── .env
├── manage.py
└── requirements.txt
```

Let's break it down:
- `books` is one (of the many we could have) application or module
of our project. There, we can locate the different components we have explained [earlier](#components).
- On the other hand, `library` serves as the main project folder, that stores 
configurations. Specifically, it has the `settings.py` file, where all the applications 
are defined. We should define there our new application `books`. Go to the file and 
modify the `INSTALLED_APPS` to have:
  ```python
  INSTALLED_APPS = [
      'books',
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
  ]
  ```

### Run the project
Now, we can run our project by executing:
```shell
python manage.py runserver
```
You can now visit `http://127.0.0.1:8000/` on your browser now.

### First migration
We have to create the schema for the database of our project. To do so, we will use the 
migration system mentioned earlier.
- First, generate the migration file:
  ```shell
  python manage.py makemigrations
  ```
  See how this has generated a file: `books/migrations/0001_initial.py`.


- Apply this migration to the database:
  ```shell
  python manage.py migrate
  ```

### Admin interface
To be able to access the admin interface, we have to create a superuser. We can do so by 
executing the following command:
```shell
python manage.py createsuperuser
```

Now we can access the admin interface from `http://127.0.0.1:8000/admin/`. You can log in 
with your superuser credentials.

See how Django provides built-in user classes, that may be useful for your project :)

## First model, view and template
Let's go!

### Define a model
In `books/models.py` we will define a simple model to represent a book of our library:
```python
from django.db import models


class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

    def __str__(self):
        return self.title
```

### Migrate the database
As we have modified a `models.py` file (in this case, to create a new model), we have to
update the schema of the database. We have to execute the same commands as before. It is 
always the same:
- First, generate the migration file:
  ```shell
  python manage.py makemigrations
  ```
  See how this has generated a file: `books/migrations/0001_initial.py`.


- Apply this migration to the database:
  ```shell
  python manage.py migrate
  ```

### Add model to the admin page
We can add the model to the admin page by registering the new model in the 
`books/admin.py` file:
```python
from django.contrib import admin
from .models import Book


@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass
```
Now, if you visit again `http://127.0.0.1:8000/admin/` you will see the new model. You
may want to try and **create a pair of books** from this interface. Useful, right?

### First view
Let's create the first view to control logic of our application. The aim of this view is 
to be able, at the end, to get a list of books from the library.
We can add the following code inside `books/views.py`:
```python
from django.views.generic import ListView
from .models import Book


class BookListView(ListView):
    model = Book
    template_name = 'book_list.html'
    context_object_name = 'books'
```
See how:
- `model = Book`: Tells the view to use the Book model.
- `template_name = 'book_list.html'`: Specifies the template to render.
- `context_object_name = 'books'`: This name will be used in the template to reference 
the list of books.

### First template
We now have to create the `book_list.html` we mentioned on the view! Let's do it:
- Create the directory: `books/templates`.
- Create the file: `books/templates/book_list.py` with the following content:
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Library - Book List</title>
      <style>
          body { font-family: Arial, sans-serif; }
      </style>
  </head>
  <body>
      <h1>Book List</h1>
      <ul>
          {% for book in books %}
              <li>{{ book.title }} by {{ book.author }} (Published: {{ book.published_date }})</li>
          {% empty %}
              <li>No books available.</li>
          {% endfor %}
      </ul>
  </body>
  </html>
  ```
  This template loops through the books context variable and displays each book’s title, 
  author, and published date in an unordered list. If no books are found, it displays a 
  fallback message: “No books available.”.

### Define the route
- Create a file `books/urls.py` and add the following code to define the URL for the 
book list:
  ```python
  from django.urls import path
  from .views import BookListView
  
  
  urlpatterns = [
      path('', BookListView.as_view(), name='book-list'),
  ]
  ```
- In the `library/urls.py` include the `books` application's URLs:
  ```python
  from django.contrib import admin
  from django.urls import path, include
  
  
  urlpatterns = [
      path('admin/', admin.site.urls),
      path('books/', include('books.urls')),
  ]
  ```

### Validate everything works
Visit `http://127.0.0.1:8000/books/`. You should see a list with the books you have 
created earlier using the admin interface.

We are done! :)
