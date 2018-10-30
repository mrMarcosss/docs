[ccbv](http://ccbv.co.uk/) - Detailed descriptions, with full methods and attributes, for each of [Django](http://djangoproject.com/)'s class-based generic views.

## General rules for CBV
1. The base view classes provided by Django always go to the right.
2. Mixins go to the left of the base view.
3. Mixins should inherit from Python’s built-in object type. Keep your inheritance chain simple!

```python
from django.views.generic import TemplateView

class FreshFruitMixin:
	def get_context_data(self, **kwargs):
		context = super(FreshFruitMixin,self).get_context_data(**kwargs)
		context["has_fresh_fruit"] = True
		return context

class FruityFlavorView(FreshFruitMixin, TemplateView):
	template_name = "fruity_flavor.html"
```

> Since TemplateView is the base view class provided by Django, it goes on the far right (rule 1),
and to its left we place the FreshFruitMixin (rule 2). This way we know that our methods and
properties will execute correctly.
Finally, FreshFruitMixin inherits from object (rule 3).

## Which GCBV Used for What Task?
Which Django GCBV Should Be Used for What Task?

|Name|Purpose|Two Scoops Example|
|--|--|--|
|`View`|Base view or handy view that can be used for anything.|-|
|`RedirectView`|Redirect user to another URL|Send users who visit ‘/log-in/’ to ‘/login/’.|
|`TemplateView`|Display a Django HTML template.|The ‘/about/’ page of our site.|
|`ListView`|List objects|List of ice cream flavors.|
|`DetailView`|Display an object|Details on an ice cream flavor.|
|`FormView`|Submit a form|The site’s contact or email form.|
|`CreateView`|Create an object|Create a new ice cream flavor.|
|`UpdateView`|Update an object|Update an existing ice cream flavor.|
|`DeleteView`|Delete an object|Delete an unpleasant ice cream flavor like Vanilla Steak.|
|`Generic date views`|For display of objects that occur over a range of time.|Blogs are a common reason to use them. For Two Scoops, we could create a public history of when flavors have been added to the database.|


```python
# flavors/views.py

from django.contrib import messages
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import CreateView, DetailView, UpdateView
from .models import Flavor

class FlavorActionMixin:
	fields = ['title', 'slug', 'scoops_remaining']
	
	@property
	def success_msg(self):
		return NotImplemented
	
	def form_valid(self, form):
		messages.info(self.request, self.success_msg)
		return super(FlavorActionMixin, self).form_valid(form)


class FlavorCreateView(LoginRequiredMixin, FlavorActionMixin,CreateView):
	model = Flavor
	success_msg = "Flavor created!"


class FlavorUpdateView(LoginRequiredMixin, FlavorActionMixin,UpdateView):
	model = Flavor
	success_msg = "Flavor updated!"


class FlavorDetailView(DetailView):
	model = Flavor
```


```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.utils.functional import cached_property
from django.views.generic import UpdateView, TemplateView
from .models import Flavor
from .tasks import update_user_who_favorited


class FavoriteMixin:
	@cached_property
	def likes_and_favorites(self):
		"""Returns a dictionary of likes and favorites"""
		likes = self.object.likes()
		favorites = self.object.favorites()
		return {
			"likes": likes,
			"favorites": favorites,
			"favorites_count": favorites.count(),
		}


class FlavorUpdateView(LoginRequiredMixin, FavoriteMixin, UpdateView):
	model = Flavor
	fields = ['title', 'slug', 'scoops_remaining']
	
	def form_valid(self, form):
		update_user_who_favorited(
			instance=self.object,
			favorites=self.likes_and_favorites['favorites']
		)
		return super(FlavorUpdateView, self).form_valid(form)


class FlavorDetailView(LoginRequiredMixin, FavoriteMixin, TemplateView):
	model = Flavor
```