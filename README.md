# Python Custom Strings

* Python's [f-strings](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) are great
* Django's [conditional_escape()](https://docs.djangoproject.com/en/3.2/ref/utils/#django.utils.html.conditional_escape) is great.

Python Custom Strings is a proposal to enhance Python, to give developers new ways to create strings.

The Python Custom Strings proposal is not related to Django or HTML. This text uses HTML and Django as an example. 

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

I would like to shorted above code to something similar to this:

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

# Customization

To make this customaziable, the user needs to define the methods which should get used.

In above example the `h` prefix of this snippet:

```
html = h'''
html = h'''
 <h1>Hi {username}</h1>
 Your messages: {messages}'''

Should be executed like

html = mark_safe('''
 <h1>Hi {username}</h1>
 Your messages: {messages}''', 
  username=conditional_escape(username),
  messages=conditional_escape(message))
```

Python does not know about these methods `mark_safe()` and `conditional_escape()`.

So we need a way to define that.

# Defining custom strings

```
__h__ = (pre_return, pre_insert)
```

With `pre_return()` and `pre_insert()` being methods which get executed to create the custom string.

In above example you would use this:

```
__h__ = (mark_safe, conditional_escape)
```

(This is just a first idea. I guess there are better ways to define both methods)

