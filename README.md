# Python Custom Strings

Python Custom Strings want to combine two great things:

* Python's [f-strings](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) are great
* Django's [conditional_escape()](https://docs.djangoproject.com/en/3.2/ref/utils/#django.utils.html.conditional_escape) is great.

Python Custom Strings is a proposal to enhance Python, to give developers new ways to create strings.

The Python Custom Strings proposal is not related to Django or HTML. This text uses HTML and Django just as an example. 

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
 
 With "username" being a simple string that gets quoted. For example `Mary & Bob` will get `Mary &amp; Bob`.
 
 with "messages" being a previously escaped string. For example `<ul><li>line1</li><li>line2</li></ul>`. It does not get escaped, since it is
 already escaped.
 
This "magic" detection whether escaping should be done or not gets handled by `conditional_escape()`. 

# Goal

I would like to shorten the above code to something similar to this:

```
html = h'''
 <h1>Hi {username}</h1>
 Your messages: {messages}'''
```

The developer should not need to type these line:
```
     username=username,
     messages=messages
```
These lines are meaningless and distracting. Avoiding them reduces the cognitive load, 
especially for the reader of the code.

# Customization

To make this customizable, the user needs to define the methods which should get used.

In the above example the `h` prefix of this snippet:

```
html = h'''
 <h1>Hi {username}</h1>
 Your messages: {messages}'''
```

Should be executed like

```
html = mark_safe('''
 <h1>Hi {username}</h1>
 Your messages: {messages}''', 
  username=conditional_escape(username),
  messages=conditional_escape(message))
```

Python does not know about these methods `mark_safe()` and `conditional_escape()`.

So we need a way to define that.



# Defining custom strings

Per file, there must be a definition of the custom strings which get used in this file.

The syntax could look like this:

```
__h__ = (pre_return, pre_insert)
```

With `pre_return()` and `pre_insert()` being methods which get executed to create the custom string.

In the above example you would use this:

```
__h__ = (mark_safe, conditional_escape)
```

(This is just a first idea. I guess there are better ways to define both methods)

The letter `h` is just an example. The developer should be able to choose his prefered
character. Replacing the already used letteres like `r`, `b`, `u` is not possible and 
results in an exception.

# What is inside curly braces?

In the above example, we just use a variable name. But having more is better.

Custom strings are meant for developers, so arbitrary code execution is fine.

Arbitrary code in curly braces should be allowed:

```
html = h'''...{my_object.my_method(some_arg)}...'''
```

# Why not just this hack?

Instead of `h'...'` you could use this hack to get the desired implementation:

```
def h(html):
    """
    Django's format_html() on steroids
    """
    def replacer(match):
        call_frame = sys._getframe(3)
        return conditional_escape(
            eval(match.group(1), call_frame.f_globals, call_frame.f_locals))
    return mark_safe(re.sub(r'{(.*?)}', replacer, html))
```

The above implementation has one big drawback:

IDEs and linters don't know that variables get used inside the custom string.

This means IDEs and linters think variables (or imports) don't get used and
act accordingly.

But it makes no sense to type `myvar=mvar` again and again, just to make IDEs/linters happy.
    
# Background

If you use the html-fragments-over-the-wire approach to web development (for example with [htmx](//htmx.org)),
then you create many small methods returning small html snippets.

If you have small methods, then keeping the HTML in one file near your Python logic simplifies the development process.

I guess a lot of people won't like this. Nevertheless some people like to mix Python and HTML. Those people
who don't like this, still can create HTML in the way they like it.

# PEP?

Do you think this could make it to a PEP? Please let me know: Just create an issue here at Github.

