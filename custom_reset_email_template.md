## Send custom email template on password/reset/ api call
---

package: `dj-rest-auth`  
Reference:  
[My docs: dj-rest-auth endpoints](https://github.com/gopal1166/django-auth-api)
[PasswordResetSerializer](https://github.com/Tivix/django-rest-auth/blob/master/rest_auth/serializers.py)  ,  
[PasswordResetForm](https://docs.djangoproject.com/en/1.8/_modules/django/contrib/auth/forms/)  

> Note: Must add the `path('', include('django.contrib.auth.urls')),` in urlpatterns in urls.py

### 1. Password Reset:
---
1.  Create custom serializer from PasswordResetSerializer in serializers.py in root ,
    should override `save()` method and  
    add `'html_email_template_name', 'domain_override'` in opts in that.
    ```
    from dj_rest_auth.serializers import PasswordResetSerializer  
    from rest_framework import serializers  
    from django.contrib.auth.forms import PasswordResetForm  
    
    class CustomPasswordResetSerializer(PasswordResetSerializer):
    """
    Serializer for requesting a password reset e-mail.
    """

    def save(self):
        request = self.context.get('request')
        # Set some values to trigger the send_email method.
        opts = {
            'use_https': request.is_secure(),
            'from_email': getattr(settings, 'DEFAULT_FROM_EMAIL'),
            'html_email_template_name': 'reset_email.html',
            'domain_override': "localhost:3000",
            'request': request,
        }

        opts.update(self.get_email_options())
        self.reset_form.save(**opts)
    ```

   
2.  Register this in settings.py
    ```
    REST_AUTH_SERIALIZERS = {
      'PASSWORD_RESET_SERIALIZER': 'serializers.CustomPasswordResetSerializer'
    }
    ```
3.  Create reset_email.html in project_root/templates with below content  
    ```
    Dear {{ user.get_username }},<br><br>
    
    Please go to the following page and choose a new password: <br>
    {% block reset_link %}
    {{ protocol }}://{{ domain }}/password{% url 'password_reset_confirm' uidb64=uid token=token %}
    {% endblock %}
    
    <br>
    Thanks for using our site!<br><br>
    
    The {{ site_name }} team
    ```
4.  Wire up templates folder to 'DIRS' in TEMPLATES in settings.py
    ```
    TEMPLATES = [
      {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        ...,
      }
    ]
    ```

##### Testing the api:  
---  
  api endpoint: `/dj-rest-auth/password/reset/`    (POST)  
  body:  `{ email: "registered user email" }`  

  On api call succeess, email be sent with below content.  
  ```
  Dear Gopal,

  Please go to the following page and choose a new password:
  http://localhost:3000/password/reset/Mw/anif7y-67ad436d7c271955ebce4e18b8d9d98e/
  Thanks for using our site!
  
  The localhost:3000 team
  ```

  On click of the link this email, **frontend application should handle this.**  
  Need to extract uid, token from the url and   
  make password reset confirm api call, using below api and payload  
  
&nbsp;
### 2. Password Reset confirm:
---
Api endpoint: `/dj-rest-auth/password/reset/confirm/` (POST)  
body: 
```
{
  uid: "",
  token: "",
  new_password1: "",
  new_password2: ""
}
```

sample output:  
```
{
  "detail": "Password has been reset with the new password."
}
```

 

