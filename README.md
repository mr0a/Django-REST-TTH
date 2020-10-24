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
  'DEFAULT_PERMISSION_CLASSES': ( # Class for Permission to modify or create data by specific user
    'rest_framework.permissions.IsAuthenicatedOrReadOnly',
  ),
}
```
- Now we are ready to use the DRF.

### Adding url
- Add url route in urls.py `path('api-auth/', include('rest_framework.urls', namespace='rest_framework')),
