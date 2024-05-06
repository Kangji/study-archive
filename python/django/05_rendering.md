# Rendering the Responses

Being a web framework, django provides template layer to generate HTML dynamically.
Meanwhile, rest framework tends to return JSON response.

Both HTML and JSON requires method to acquire model data from request.
HTML uses form, while JSON itself contains model data.

## Template

A Django project can be configured with one or several template engines (or even zero if you don’t use templates).
Django ships built-in backends for its own template system,
creatively called the Django template language (DTL), and for the popular alternative `Jinja2`.
Backends for other template languages may be available from third-parties.
You can also write your own custom backend.

To use template, please see [template overview](https://docs.djangoproject.com/en/5.0/topics/templates/).

## Form



## Serializer

Serialization is not a new concept.
Django Rest Framework's model serializer is in charge of deserializing(and validating) data from request,
and serializing a model into JSON response.