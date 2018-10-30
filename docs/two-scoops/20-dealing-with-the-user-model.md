**Use `settings.AUTH_USER_MODEL` for Foreign Keys to User**

```python
from django.conf import settings
from django.db import models

class IceCreamStore(models.Model):
	owner = models.OneToOneField(settings.AUTH_USER_MODEL)
	title = models.CharField(max_length=255)
```

**custom user should inherit from** 
`from django.contrib.auth.models import AbstractUser`
