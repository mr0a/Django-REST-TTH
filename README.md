# Django-REST-TTH

### DRF Basics
- We can create API just using Django although DRF makes it easy to build.
- Django Rest Framework DRF - makes it easy to create a API, manage authentication, turn query sets into json, prevent throttling, etc.

### Installing DRF
- `pip install djangorestframework`
- Add the app in the installed apps in the project settings file.
- Add a dictionary REST_FRAMEWORK in the settings file for DRF settings.
```python3
REST_FRAMEWORK = {
  'DEFAULT_AUTHENTICATION_CLASSES' : ( # Class for authenticating users as User A or B
    'rest_framework.authentication.SessionAuthentication',
  ),
  'DEFAULT_PERMISSION_CLASSES': ( # Class for Permission to modify or create data by specific user (This might not be needed now)
    'rest_framework.permissions.IsAuthenicatedOrReadOnly',
  ),
}
```
- Now we are ready to use the DRF.

### Adding url
- Add url route in urls.py `path('api-auth/', include('rest_framework.urls', namespace='rest_framework')),

### Model Serializers
- Convert model output to json with builtin functions.
- Create a file named `serializers.py` in the project or app you want to create json data.
```python3
from rest_framework import serializers
from . import models

class CourseSerializer(serializers.ModelSerializer):
  class Meta:
      model = models.Model_name
      fields = ['id', 'field1']
 ```
 - Use the created class in django shell to test the serializer.
 ```python3
 from rest_framework.renderers import JSONRenderer # for rendering as json byte string
 from app.models import Model_name
 from app.serializers import CourseSerializer
 
 course = Course.objects.latest('id')
 serializer = CourseSerializer(course) #serializer.data shows json
 JSONRenderer().render(serializer.data) #returns json byte string
 ```
 ### GET Requests with APIViews
 - Create a View class which extends from APIViews class from rest_framework.views
 ```python3
 from rest_framework.views import APIViews
 from rest_framework.response import Response
 from . import serializers
 
 class ListView(APIViews):
  def get(self, request, format=None):
    items = Model.objects.all()
    serializer = serializers.CourseSerializer(courses, many=True)
    return Response(serializer.data)
```

### POSTing to an APIView
- Creating new record using POST data using the same ListView class by adding a post method.
```python3
from rest_framework import status

def post(self, request, format=None):
  serializer = serializers.CourseSerializer(data=request.data) # Creating serializer with the recieved data
  serializer.is_valid(raise_exception=True) # Checking if the serializer is valid else raise exception
  serializer.save() # Save the data in the database and also updates the serializer
  return Response(serializer.data, status=status.HTTP_201_CREATED)
```

### Generic CRUD
- Using `generics` from `rest_framework` to create generic api views
```python3
from rest_framework import generics
from .models import Course
from . import serializers

class ListCreateView(generics.ListCreateAPIView): # Try changing to ListAPIView
  queryset = Course.objects.all()
  serializer_class = serializers.CourseSerializer # Specifying the serializer to use
```
- We can also use these generic views directly from `urls.py`(dont' forget to import the serializers, models and generic view).
```python3
path('api/<int:course_pk>/step/', ListCreateAPIView.as_view(queryset=Step.objects.all(), serializer_class = StepSerializer))
```
- The ListCreateAPIView creates a view of the queryset with option to create new records.

### Overiding generic view methods
- `perform_create` method to validate or enforce some things during creation of new record with post request to the api.
- Using `RetrieveUpdateDestroy` from generics to create a view where we can retrieve single, update or delete a record.
```python3
class RetrieveUpdateDestroyCourse(generics.RetrieveUpdateDestroyAPIView): # You can also try with RetrieveAPIView, etc.
    queryset = Course.objects.all() # Queryset from where a record is retrieved by pk from the request url
    serializer_class = serializers.CourseSerializer
```
- To change the queryset i.e. the list of records accessible through the API create get_queryset method in the view.
```python3
def get_queryset(self):
  return self.queryset.filter(course_id=self.kwargs.get('course_pk')) # This filters the queryset
```

### Viewsets and Routers
