## Send custom email template on password/reset/ api call

1.  create custom serializer below
    ```
    def validate_email(self, value):
        # Create PasswordResetForm with the serializer  
        self.reset_form = self.password_reset_form_class(data=self.initial_data)  
        if not self.reset_form.is_valid():  
            raise serializers.ValidationError(self.reset_form.errors)

        return value
    ```  
    
    ```
       def save(self):
        request = self.context.get('request')
        print('request: ', request)
        # Set some values to trigger the send_email method.
        opts = {
            'use_https': request.is_secure(),
            'from_email': getattr(settings, 'DEFAULT_FROM_EMAIL'),
            'html_email_template_name': 'reset_email.html',
            'request': request,

        }

        opts.update(self.get_email_options())
        self.reset_form.save(**opts)

    ```
2.  dd
3.  dd
