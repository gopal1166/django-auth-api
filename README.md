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

>   Note: This project depends on `django-rest-framework` library, so install it if you haven’t done yet. Make sure also you have installed `rest_framework` and `rest_framework.authtoken` apps
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
    endpoint: `/dj-rest-auth/login/ (POST)`
    body: json
    ```
    {
        email: "user email",
        password: "user password"
    }
    ```
    sample output:
    ```
    {
        key: "token string",
        user:
    }
    ```



    

















