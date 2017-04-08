# django-datetimepicker

Uses xdsoft's datetimepicker to make a datetimepicker widget for django.
This is inspired by the `django-bootstrap3-datetimepicker` application.

![datetimepicker preview](https://cloud.githubusercontent.com/assets/26252388/24758585/1c485514-1ae3-11e7-8907-efe6db0155fd.png)

## Installation

- Run `pip install django-datetimepicker`
- Add `'datetimepicker'` to your `INSTALLED_APPS`

## Basic usage

Here is an example of how to use the widget.
Further examples can be found in the example project.

1. Assign the `DateTimePicker` to a `DateTimeField`, `DateField` or `TimeField`.

```python
from django import forms
from datetimepicker.widgets import DateTimePicker


class SampleForm(forms.Form):

    datetime = forms.DateTimeField(
        widget=DateTimePicker(),
    )
```

This will render the following `html` code:
```html
<div  class=" date input-group" id="datetimepicker_id_datetime">
	<input  class=" form-control" id="id_datetime" name="datetime" type="text" required/>
</div>
<script>(function () {
	$(document).ready(function() {
		$("#id_datetime").datetimepicker();
	});
})(jQuery);
</script>
```

## Passing options to `jQuery.datetimepicker`

You can add `jQuery.datetimepicker` options according to your needs.
[See XDSoft's documentation for a full list of options.](http://xdsoft.net/jqplugins/datetimepicker/ "XDSoft's DateTimePicker documentation.")

```python
    datetime = forms.DateTimeField(
        widget=DateTimePicker(options={
	    'format': '%Y-%m-%d %H:%M',
	    'language': 'en-us',
	}),
    )
```

## Customization with template overloading

`django-datetimepicker` uses Django's own templating engine to render the field itself and the script tag needed to run it.
This allows to overload the templates to adapt the widget and javascript to your needs.

- [`datetimepicker/div.html`](https://github.com/escodebar/django-datetimepicker/blob/master/datetimepicker/templates/datetimepicker/div.html)
- [`datetimepicker/script.html`](https://github.com/escodebar/django-datetimepicker/blob/master/datetimepicker/templates/datetimepicker/div.html)
- [`datetimepicker/loader.js`](https://github.com/escodebar/django-datetimepicker/blob/master/datetimepicker/templates/datetimepicker/loader.js)

## Form javascript media

As seen in the basic example, a `<script>`-tag containing the logic to load the widget is rendered below the input field by default.
This can be prevented by setting the `script_tag` argument of the widget's initialiser.

```python
from django import forms
from datetimepicker.widgets import DateTimePicker


class SampleForm(forms.Form):

    datetimepicker_without_script_tag = forms.DateTimeField(
    	widget=DateTimePicker(script_tag=False),
    )
```

The javascript to initialise the widget can be provided as external file with the form media using the `js_loader_url` helper.
The media is added [dynamically](https://docs.djangoproject.com/en/dev/topics/forms/media/#media-as-a-dynamic-property).

```diff
 from django import forms
+from datetimepicker.helpers import js_loader_url
 from datetimepicker.widgets import DateTimePicker


 class SampleForm(forms.Form):

     datetimepicker_without_script_tag = forms.DateTimeField(
	 widget=DateTimePicker(script_tag=False),
     )

+    @property
+    def media(self):
+       form_media = forms.Media(js=[
+           js_loader_url(
+               fields=self.fields,
+               input_ids=['id_datetimepicker_without_script_tag'],
+           ),
+       ])
+       return super(SampleForm, self).media + form_media
```

`js_loader_url` requires to include the `datetimepicker.urls` to your `urls.py`.

```diff
 urlpatterns = [
     ...
+    url(r'^datetimepicker/', include('datetimepicker.urls')),
     ...
 ]
```
