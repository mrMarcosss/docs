 ## Readable code
 `Make your code as readable as possible`:
 - Avoid abbreviating variable names.
 - Write out your function argument names.
 - Document your classes and methods.
 - Comment your code.
 - Refactor repeated lines of code into reusable functions or methods.
 - Keep functions and methods short. A good rule of thumb is that scrolling should not be nec-
essary to read an entire function or method.

[Use Flake8 for Checking Code Quality](http://flake8.pycqa.org/en/latest/)

## Imports
```python
# Stdlib imports
from math import sqrt
from os.path import abspath

# Core Django imports
from django.db import models
from django.utils.translation import ugettext_lazy as _

# Third-party app imports
from django_extensions.db.models import TimeStampedModel

# Imports from your apps
from splits.models import BananaSplit
```

```python
# DON'T DO THIS!
from cones.models import WaffleCone
from cones.forms import WaffleConeForm

# DO THIS!
from .models import WaffleCone
from .forms import WaffleConeForm
```

| Code | Import Type | Usage |
|:-|-|:-:|
|`from core.views import FoodMixin`|absolute import|Use when importing from outside the current app
|`from .models import WaffleCone`|explicit relative|Use when importing from another module in the current app|
|`from models import WaffleCone`|implicit relative|Often used when importing from another module in the current app, but not a good idea


> Use Underscores `('_')` in **Template Block and URL Names** Rather Than Dashes `('-')`

[HTML Style guide](http://codeguide.co/)

[Stylelint is a coding style formatter for CSS.](https://stylelint.io/)

