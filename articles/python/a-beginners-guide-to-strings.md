Strings are present in almost all programming languages, and Python is not the exception. A string is an organized sequence of characters inside quotes. They can store any number of characters and usually represent text in programs. 

### What to spect from this article

This is a short article on Python strings. The following topics will be presented:

* How are strings created in Python?
* Concatenating strings with `+`
* Duplicating strings with `*`
* String templates
* Iterable behavior
* Useful string methods


### How are strings created in Python?

 We can create strings in Python in different ways. We insert any sequence of characters inside `'single quotes'`:

```python
name = 'Andres Osorio'
```

We can also use `"double quotes"`:

```python
# greeting_one = 'Hi! I'm a Web Developer' *WRONG*
greeting_two = "Hi! I'm a Web Developer"
```

See how we added a single quote inside both double quotes in the last example? If we had used single quotes, Python would have thrown a **SyntaxError** because it does not know where the string ends. 

What if you want to use single and double quotes inside the string? You can do it in two different ways. The first one is by escaping the single or double quote, `\'` or `\"`.

```python
greeting = 'Hi I\'m a Fullstack Web Developer'
```

The other way we can create a string that can contain single and double quotes, without escaping them, is with triple quotes!

```python
greeting = '''Hi I'm a Fullstack Web Developer'''
# Or
greeting = """Hi I'm a Fullstack Web Developer"""
```

Triple quotes are also great when you want to expand the string in multiple lines:

```python
short_bio = """Hi, my name is Andres Osorio. 
I’m an electronics engineer passionate about web development. 
I’m a coffee addict and chillies lover."""
```

Pretty neet right?

### Concatenating strings

You can generate a new string out of an existing one by adding to it something else. With the `+` operator we can concatenate strings to create new ones.

```python
name = 'Andres'
greeting = 'Hi my name is ' + name
```

We can use `+=` to add to new same string more content:

```python
name = 'Andres'
greeting = 'Hi my name is ' + name
greeting += ' and I\'m a Web Developer.'
```

### Duplicating strings

We can use the `*` operator to create multiple copies of a string and assign them to a new string.

```python
level_of_motivation = 'High! ' * 5 
print(level_of_motivation) #prints: High! High! High! High! High!
```

### String templates

With python we can use a nice feature of strings. We can generate a template adding `{}` as a placeholder for a variable and then using the `format()` method to insert the variables value into the placeholder. 

```python
name = 'Andres'
greeting = 'Hi my name is {}'.format(name)
```

We can add more than one placeholder and pass all the template variables to the `format` method.

```python
name = 'Andres'
loves = 'chillies'
greeting = 'Hi my name is {} and I love {}'.format(name, loves)
```

This is very useful when you want to create strings that are dinamically created.


### Iterable behavior

Strings are just a sequence of characters and much like lists they have indexes, can be sliced and loop through. See code snippet below:

```python
name = 'Andres'
print(name[0]) # prints: 'A'
print(name[2]) # prints: 'd'
print(name[len(name) - 1]) # prints: 's'
print(name[:3]) # prints: 'And'
print(name[2:5]) # prints: 'dre'
```

We can also loop through a strings:

```python
fav_chilli = 'Habanero'
for index in range(len(fav_chilli)):
    print(fav_chilli[index])
    
"""prints:
'H'
'a'
'b'
'a'
'n'
'e'
'r'
'o'"""
``` 

### Useful string methods

Python has lots of built-in methods for strings, I will just show you the ones I use the most. 

#### upper(), lower() and title()

The `upper()` method, turns all the characters of a string into uppercase.

The `lower()` method, turns all the characters of a string into lowercase.

The `title()` method, turns the first letter of each word in a string into uppercase and the rest into lowercase.

```python
bad_format_name = 'AnDrEs osORiO'

upper_name = bad_format_name.upper()
print(upper_name) # prints: 'ANDRES OSORIO'

lower_name = bad_format_name.lower()
print(lower_name) # prints: 'andres osorio'

title_name = bad_format_name.title()
print(title_name) # prints: 'Andres Osorio'
```

These methods are greate when you need to format user input in a certain way.

#### startswith() and endswith()

Both `startswith()` and `endswith()` return `True` or `False`. 

The `startswith()` method checks if a substring is contained at the begining of the string.

The `endswith()` method checks if a substring is contained at the end of the string.

```python
fav_chilli = 'Habanero'

print(fav_chilli.startswith('Hab')) # prints True
print(fav_chilli.endswith('ner')) # prints False
```

#### join() and split()

These are two of the most common ones and are used to convert from  string to list and viceversa.

The `join()` method turns a list into a string and it is called uppon a separator (this can be any string).

```python
list_of_stuff = ['cards', 'apple', 'forks', 'cloud']

str_of_stuff = ', '.join(list_of_stuff)
print(str_of_stuff) # prints: 'cards, apple, forks, cloud'
```

As you can see the `join()` method created a string of items separated by `', '`.

The `split()` method returns a list of items from a string. We can pass a separator string but it is optional, by default the string is separated by a blank space.

```python
greeting = "Hi! I'm a Web Developer"
print(greeting.split()) # prints: ['Hi!', 'I\'m', 'a', 'Web', 'Developer']
```

You can see know why these methods are really powerfull?

There are many more! make sure to chack out the [documentation](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str)

### Conclusion

In this article we discussed about strings in Python. How strings are created and how they can be manipulated to create new strings. These are the principal takeaways:

* Strings can be created using single, double and triple quotes.
* The `+` operator is used to concatenate multiple strings.
* The `*` operator duplicates a string a number of times.
* Strings are a sequence of characters, they are iterable.
* There are many built-in methods to use with strings in order to make out life as programers much easier.

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!