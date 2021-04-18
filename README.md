# Python Custom Strings

* Python's [f-strings](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) are great
* Django's [conditional_escape()](https://docs.djangoproject.com/en/3.2/ref/utils/#django.utils.html.conditional_escape) is great.

Python Custom Strings is a proposal to enhance Python, to give developers new ways to create strings.

In this text we use HTML and Django as example. The Python Custom Strings proposal is not related to Django or HTML.

# Motivation

I could create HTML in Python with the help of [format_html()](https://docs.djangoproject.com/en/3.2/ref/utils/#django.utils.html.format_html) like this:

```
html = format_html('''
 <h1>Hi {username}</h1>
 Your messages: {messages}''',
     username=username,
     messages=messages
     )
 ```
 
 With "username" being a simple string which gets quoted. For example "Mary & Bob" will get "Mary &amp; Bob".
 
 with "messages" being a previously escaped string. For example "<ul><li>line1</li><li>line2</li></ul>". It does not get escaped, since it is
 already escaped.
 
This "magic" detection wheter escaping should be done or not gets handled by `conditional_escape()`. 

# Goal

I would like to shorted above code to:

```
html = h'''
 <h1>Hi {username}</h1>
 Your messages: {messages}'''
```

The developer should not need to type these lines:
```
     username=username,
     messages=messages
```
These lines are meaningless and distracting.
