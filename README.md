## Django authentication end points
1. register
2. login
2. logout
3. password reset
4. password reset confirm

package: `dj-rest-auth`
[Official Website](https://dj-rest-auth.readthedocs.io/en/latest/)

### Installation:
---
1. install package: `$ pip install dj-rest-auth`
2. Add `dj_rest_auth` app to INSTALLED_APPS in your django settings.py:
    ```
    INSTALLED_APPS = (
        ...,
        'rest_framework',
        'rest_framework.authtoken',
        ...,
        'dj_rest_auth'
    )
    ```

>   Note: This project depends on `django-rest-framework` library,  
so install it if you haven’t done yet.  
Make sure also you have installed `rest_framework` and `rest_framework.authtoken` apps
3. Add dj_rest_auth urls:
    ```
    urlpatterns = [
        ...,
        path('dj-rest-auth/', include('dj_rest_auth.urls'))
    ]   
    ```
4. Migrate your database: ```$ python manage.py migrate```
5. d
6. d
### 1. Registration api:
---
1. install `django-allauth` using below and verify:
`pip install 'dj-rest-auth[with_social]'`
2. in settings.py
    ```
    INSTALLED_APPS = (
        ...,
        'django.contrib.sites',
        'allauth',
        'allauth.account',
        'allauth.socialaccount',
        'dj_rest_auth.registration',
    )
    
    SITE_ID = 1
    ```
3. Add `dj_rest_auth.registration` urls:
    ```
    urlpatterns = [
        ...,
        path('dj-rest-auth/', include('dj_rest_auth.urls')),
        path('dj-rest-auth/registration/', include('dj_rest_auth.registration.urls'))
    ]
    ```
    #### Testing:
    endpoint: `/dj-rest-auth/registration/ (POST)`
    body: json
    ```
    {
        username: ""
        email: ""
        password1: ""
        password2: ""
    }
    ```
    Sample output: 
    `{key: 'token string'}`
    
### 2. Login api:
---
To get the user info along with token, follow these

1.  in settings.py:
    ```
    # To use token authentication
    REST_FRAMEWORK = {
        'DEFAULT_AUTHENTICATION_CLASSES': [
            'rest_framework.authentication.TokenAuthentication',
        ],
    }
    
    # all-auth settings to use email instead of username for login
    ACCOUNT_AUTHENTICATION_METHOD = 'email'
    ACCOUNT_EMAIL_REQUIRED = True
    ACCOUNT_USERNAME_REQUIRED = False

    AUTHENTICATION_BACKENDS = [
      # Needed to login by username in Django admin, regardless of allauth
      'django.contrib.auth.backends.ModelBackend',
  
      # allauth specific authentication methods, such as login by e-mail
      'allauth.account.auth_backends.AuthenticationBackend',
    ]
    ```
2.  Define custom Token Serializer 
example path file path: `project/app/serializers.py`
    ```
    from rest_framework import serializers
    from django.contrib.auth import get_user_model
    from rest_framework.authtoken.models import Token
    
    class UserSerializer(serializers.ModelSerializer):
        class Meta:
            model = get_user_model()
            fields = ('id', 'username', 'email')
    
    
    # serializers.ModelSerializer
    class MyCustomTokenSerializer(serializers.ModelSerializer):
        user = UserSerializer(read_only=True)
    
        class Meta:
            model = Token
            fields = ('key', 'user')
    ```
3.  wire it to `TOKEN_SERIALIZER` in settings.py
    ```
    REST_AUTH_SERIALIZERS = {
        'TOKEN_SERIALIZER': 'accounts_app.serializers.MyCustomTokenSerializer'
    }
    ```
    ### Testing login api
    **endpoint:** `/dj-rest-auth/login/ (POST)`
    body: json
    ```
    {
        email: "user email",
        password: "user password"
    }
    ```
    **sample output:**
    ```
    {
      "key": "b78a86dfebfca796230f095167ba2c769407efcc",
      "user": {
          "id": 3,
          "username": "Gopal",
          "email": "vgopal1166@gmail.com"
      }
    }
    ```

### 3. logout:
---
  Api endpoint: `/dj-rest-auth/logout/ (POST)`
  Body: json
  > Note(Not Recommended): in settings.py, can set `ACCOUNT_LOGOUT_ON_GET = True` 
  to use GET instead of POST

### 4. Password reset:
---
  #### Testing:
  Api endpoint: `/dj-rest-auth/password/reset/ (POST)`
  Body (json): `{ email: "user email" }`
  Sample output: `{ "detail": "Password reset e-mail has been sent." }`
  
  On success api call, an email will be sent with reset url like below `protocol://domain/reset/uid/token/`.
  ```
  Dear Gopal,

  Please go to the following page and choose a new password:
  http://localhost:3000/reset/Mw/ani3u8-197abc2c67ea7bb1b01cdef90e96017a/
  Thanks for using our site!
  
  The localhost:3000 team
  ```

  On click of the link this email, **`frontend application should handle this`**.
  Need to extract uid, token from the url andmake an api call  
  to password reset confirm using below api and payload.
  
### 5. Password reset confirm
---

  Api endpoint: `/dj-rest-auth/password/reset/confirm/ (POST)`
  Body: 
  ```
  {
    uid: "",
    token: "",
    new_password1: "",
    new_password2: ""
  }
  ```
  
  Sample output: 
  ```
  {
    "detail": "Password has been reset with the new password."
  }
  ```
  
  


    

















