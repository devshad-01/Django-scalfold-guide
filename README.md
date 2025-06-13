# Django REST Framework Integration

A comprehensive guide to integrating Django REST Framework (DRF) with Django to build powerful Web APIs.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Project Setup](#project-setup)
- [DRF Integration Steps](#drf-integration-steps)
- [API Endpoints](#api-endpoints)
- [Testing the API](#testing-the-api)
- [Project Structure](#project-structure)
- [Key Concepts](#key-concepts)
- [Next Steps](#next-steps)

## Overview

Django REST Framework (DRF) is a powerful and flexible toolkit for building Web APIs. It extends Django's capabilities to facilitate the development of RESTful APIs, providing features like:

- **Serialization**: Convert complex data structures (Django models) into JSON/XML formats
- **ViewSets and Routers**: Streamlined API endpoint creation with reduced boilerplate code
- **Authentication and Permissions**: Built-in security features for API access control
- **Browsable API**: Interactive web interface for testing and exploring API endpoints

## Prerequisites

- Python 3.8+
- Django 4.2+
- Basic knowledge of Django models and views

## Project Setup

This project demonstrates a basic Book API using Django REST Framework.

### Initial Django Project Structure

```
my_project/
├── manage.py
├── db.sqlite3
├── my_project/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
└── my_app/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── views.py
    ├── tests.py
    └── migrations/
```

## DRF Integration Steps

### Step 1: Install Django REST Framework

```bash
pip install djangorestframework
```

### Step 2: Update Django Settings

Add `rest_framework` and your app to `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',        # Add DRF
    'my_app',               # Add your app
]
```

### Step 3: Create Your Model

Define your data model in `my_app/models.py`:

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

    def __str__(self):
        return self.title
```

### Step 4: Create a Serializer

Create `my_app/serializers.py`:

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

**What serializers do:**

- Convert model instances to JSON data (serialization)
- Convert JSON data to model instances (deserialization)
- Handle data validation
- Define which fields to include/exclude

### Step 5: Create API Views

Update `my_app/views.py`:

```python
from django.shortcuts import render
from rest_framework import generics
from .models import Book
from .serializers import BookSerializer

class BookListCreateAPIView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**View explanation:**

- `ListCreateAPIView`: Provides GET (list) and POST (create) functionality
- `queryset`: Defines which objects the view can access
- `serializer_class`: Specifies how to serialize/deserialize data

### Step 6: Configure URLs

Create `my_app/urls.py`:

```python
from django.urls import path
from .views import BookListCreateAPIView

urlpatterns = [
    path("api/books/", BookListCreateAPIView.as_view(), name="book_list_create"),
]
```

Update main `my_project/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('my_app.urls')),
]
```

### Step 7: Database Migration

Create and apply migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Step 8: Run the Development Server

```bash
python manage.py runserver
```

Your API will be available at: `http://127.0.0.1:8000/api/books/`

## API Endpoints

| Method | Endpoint      | Description       |
| ------ | ------------- | ----------------- |
| GET    | `/api/books/` | List all books    |
| POST   | `/api/books/` | Create a new book |

### Example API Responses

**GET /api/books/** (List books):

```json
[
  {
    "id": 1,
    "title": "Django for Beginners",
    "author": "William Vincent",
    "published_date": "2022-10-20"
  },
  {
    "id": 2,
    "title": "Python Crash Course",
    "author": "Eric Matthes",
    "published_date": "2019-05-03"
  }
]
```

**POST /api/books/** (Create book):

```json
{
  "title": "New Book Title",
  "author": "Author Name",
  "published_date": "2025-01-01"
}
```

## Testing the API

### 1. Using the Browsable API

Navigate to `http://127.0.0.1:8000/api/books/` in your browser to access DRF's interactive interface.

### 2. Using cURL

**List all books:**

```bash
curl http://127.0.0.1:8000/api/books/
```

**Create a new book:**

```bash
curl -X POST http://127.0.0.1:8000/api/books/ \
  -H "Content-Type: application/json" \
  -d '{"title": "Sample Book", "author": "John Doe", "published_date": "2025-01-01"}'
```

### 3. Using Python requests

```python
import requests
import json

# List books
response = requests.get('http://127.0.0.1:8000/api/books/')
print(response.json())

# Create a book
book_data = {
    "title": "API Testing Book",
    "author": "Test Author",
    "published_date": "2025-06-13"
}
response = requests.post(
    'http://127.0.0.1:8000/api/books/',
    headers={'Content-Type': 'application/json'},
    data=json.dumps(book_data)
)
print(response.json())
```

## Project Structure

After completing the integration, your project structure will look like:

```
my_project/
├── README.md
├── manage.py
├── db.sqlite3
├── my_project/
│   ├── __init__.py
│   ├── settings.py      # Updated with DRF settings
│   ├── urls.py          # Updated to include app URLs
│   ├── wsgi.py
│   └── asgi.py
└── my_app/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py         # Book model
    ├── serializers.py    # NEW: Book serializer
    ├── views.py          # Updated with API views
    ├── urls.py           # NEW: App URL patterns
    ├── tests.py
    └── migrations/
        ├── __init__.py
        └── 0001_initial.py  # Book model migration
```

## Key Concepts

### DRF Architecture Components

1. **Serializers**

   - Handle data conversion between Python objects and JSON
   - Provide data validation
   - Control field inclusion/exclusion

2. **Views/ViewSets**

   - Handle HTTP requests and responses
   - Apply business logic
   - Manage permissions and authentication

3. **URLs/Routers**
   - Map URLs to views
   - Define API endpoint structure

### Generic Views Used

- `ListCreateAPIView`: Combines list and create functionality
- Automatically handles GET (list) and POST (create) requests
- Reduces boilerplate code significantly

### Benefits Achieved

✅ **Clean API Structure**: RESTful endpoints with standard HTTP methods  
✅ **Automatic Serialization**: JSON conversion handled automatically  
✅ **Interactive Documentation**: Browsable API for easy testing  
✅ **Validation**: Built-in data validation through serializers  
✅ **Extensible**: Easy to add authentication, permissions, and more features

## Next Steps

To extend this basic API, consider implementing:

1. **Detail Views**: Add retrieve, update, and delete operations

   ```python
   class BookDetailAPIView(generics.RetrieveUpdateDestroyAPIView):
       queryset = Book.objects.all()
       serializer_class = BookSerializer
   ```

2. **Authentication**: Add user authentication

   ```python
   REST_FRAMEWORK = {
       'DEFAULT_AUTHENTICATION_CLASSES': [
           'rest_framework.authentication.SessionAuthentication',
           'rest_framework.authentication.TokenAuthentication',
       ]
   }
   ```

3. **Permissions**: Control access to endpoints

   ```python
   from rest_framework.permissions import IsAuthenticated

   class BookListCreateAPIView(generics.ListCreateAPIView):
       permission_classes = [IsAuthenticated]
   ```

4. **Filtering and Pagination**: Add search and pagination capabilities

5. **API Versioning**: Implement API versioning for backward compatibility

6. **Testing**: Add comprehensive API tests using DRF's test framework

## Learning Objectives Achieved

✅ **Understanding DRF Purpose**: Learned how DRF extends Django for API development  
✅ **DRF Architecture**: Familiarized with serializers, views, and URL routing  
✅ **API Endpoint Creation**: Successfully created a functional REST API endpoint  
✅ **CRUD Operations**: Implemented Create and Read operations for the Book model  
✅ **API Testing**: Learned multiple methods to test API endpoints

---

**Project Status**: ✅ Complete and Ready for Development

Your Django REST Framework integration is now fully functional and ready for further development!
