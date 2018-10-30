# Don’t Use Sequential Keys as Public Identifiers

>Sequential keys, such as what Django provides as a default as model primary keys, can be a security concern if used publicly. 
>In our example, we’re going to use the model’s **UUID** rather than the model’s primary key to look up our records. 
>We always try to avoid using sequential numbers for lookups.

```python
class Flavor(models.Model):
	title = models.CharField(max_length=255)
	slug = models.SlugField(unique=True) # Used to find the web URL
	uuid = models.UUIDField( # Used by the API to look up the record
		db_index=True,
		default=uuid_lib.uuid4,
		editable=False
	)
```
```python
class FlavorListCreateAPIView(ListCreateAPIView):
	queryset = Flavor.objects.all()
	permission_classes = (IsAuthenticated, )
	serializer_class = FlavorSerializer
	lookup_field = 'uuid' # Don't use Flavor.id!

class FlavorRetrieveUpdateDestroyAPIView(RetrieveUpdateDestroyAPIView):
	queryset = Flavor.objects.all()
	permission_classes = (IsAuthenticated, )
	serializer_class = FlavorSerializer
	lookup_field = 'uuid' # Don't use Flavor.id!
```

[www.cdrf.co](http://www.cdrf.co/)


`/api/cones/ # GET, POST`
`/api/cones/:uuid/ # PUT, DELETE`
**`/api/cones/:uuid/scoops/ # GET, POST`
`/api/cones/:uuid/scoops/:uuid/ # PUT, DELETE`**
`/api/scoops/ # GET, POST`
`/api/scoops/:uuid/ # PUT, DELETE`

---
## API structure
```
flavors/
├── api/
│ ├── __init__.py
│ ├── authentication.py
│ ├── parsers.py
│ ├── permissions.py
│ ├── renderers.py
│ ├── serializers.py
│ ├── validators.py
│ ├── views.py
│ ├── viewsets.py
```

**IsAdminUser as the Constant Default Permission Class**

>We like to lock down our projects, especially our `REST API`s. There’s no better way to this then to have **`rest_framework.permissions.IsAdminUser`** **as the default** permission class. This we can override on a per-view basis. This makes our `API views` very secure by default, something worth more than having to add a few extra lines of code in our `API views`.

```python
REST_FRAMEWORK = {
	'DEFAULT_PERMISSION_CLASSES': (
		'rest_framework.permissions.IsAdminUser',
	),
}
```
