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
### Registration api:
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
    
