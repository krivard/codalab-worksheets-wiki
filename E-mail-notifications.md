The CodaLab web site requires the ability to send e-mails when a user forgets her password and requests a password reset. The functionality is only needed for [future scenarios](https://trello.com/b/vHHqHmCH/codalab-scenarios).

We use the [SendGrid add-on to Windows Azure](http://www.windowsazure.com/en-us/store/service/?id=f131eadb-7aa3-401a-a2fb-1c7e71f45c3c) to send e-mails via SMTP.

The [SendGrid](http://sendgrid.com) developer's documentation explains [how to integrate the e-mail service in Django](http://sendgrid.com/docs/Integrate/Frameworks/django.html). In addition, the Django project provides comprehensive documentation: https://docs.djangoproject.com/en/dev/topics/email/. Additional notes are collected below.

### Settings

The key settings which affect how e-mailing works are:

```
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.sendgrid.net'
EMAIL_HOST_USER = '<insert username>'
EMAIL_HOST_PASSWORD = '<insert password>'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = 'info@codalab.org'
SERVER_EMAIL = 'info@codalab.org'

```

The [Site](https://docs.djangoproject.com/en/1.3/ref/contrib/sites/) configuration also plays a role because it defines the domain name associated with the web site. The site domain name and a human-readable name of the web site are defined in the settings by:

```
CODALAB_SITE_DOMAIN = 'codalab.org'
CODALAB_SITE_NAME = 'CodaLab'
```

The `initialize.py` script inserts these names in the database. Recall that `initialize.py` is located in `codalab/scripts` and is used to insert constants into the database. To illustrate how the site info is used, consider the e-mail sent to a user resetting his password:

```
Subject: [CodaLab] Password Reset E-mail
From: info@codalab.org
To: someuser@somewhere.com
Date: Thu, 03 Oct 2013 22:42:55 -0000

You're receiving this e-mail because you or someone else has requested a password 
for your user account at codalab.org. It can be safely ignored if you did not request 
a password reset. Click the link below to reset your password.

http://codalab.org/accounts/password/reset/key/1-3le-6640241efe4e5b77f4e5/

In case you forgot, your username is someuser.

Thanks for using our site!
```

The subject field of the e-mail uses the verbase name of the site, while the body of the e-mail includes several references to `codalab.org` which is given by the site's domain name.


### Testing without sending e-mails

As noted in the [Django documentation](https://docs.djangoproject.com/en/dev/topics/email/), one can avoid sending e-mails during development but still see them in the console by setting:
 
```
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

This is the default configuration for the base `Dev` configuration.


