```
# settings配置
INSTALLED_APPS = [
	'social_django',
]
# sentry-SDK
import sentry_sdk
from sentry_sdk.integrations.django import DjangoIntegration

sentry_sdk.init(
    dsn="http://c8ffd3998ba64e66903350f7b1e6c1d1@172.16.65.8:9000/2",
    integrations=[DjangoIntegration()],

    # If you wish to associate users to errors (assuming you are using
    # django.contrib.auth) you may enable sending PII data.
    send_default_pii=True
)
```