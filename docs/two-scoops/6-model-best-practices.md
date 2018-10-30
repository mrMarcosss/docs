## Tips for Creating Migrations
* As soon as a new app or model is created, take that extra minute to create the initial `django.db.migrations` for that new model. All we do is type `python manage.py makemigrations`.

* Examine the generated migration code before you run it, especially when complex changes are
involved. Also review the `SQL` that will be used with the sqlmigrate command.

* Use the `MIGRATION_MODULES` setting to manage writing migrations for third-party apps that
don’t have their own` django.db.migrations-style` migrations.

* Don’t worry about how many migrations are created. If the number of migrations becomes unwieldy, use `squashmigrations` to bring them to heel.

* Always back up your data before running a migration.

**Take the time to make sure that no model should contain data already stored in another model.**

## Model Managers
```python
from django.db import models
from django.utils import timezone

class PublishedManager(models.Manager):
	use_for_related_fields = True
	
	def published(self, **kwargs):
		return self.filter(pub_date__lte=timezone.now(), **kwargs)
		
class FlavorReview(models.Model):
	review = models.CharField(max_length=255)
	pub_date = models.DateTimeField()
	
	# add our custom model manager
	objects = PublishedManager()


FlavorReview.objects.published()
```