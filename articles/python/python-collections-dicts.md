This is the third article on Python Collections. In the first one we talked about [Lists](/blog/python-collections-lists/), the mutable, ordered collection. In the second article we talked about [Tuples](/blog/python-collections-tuples/) the immutable list-like collection.

In this article we will talk about a new type of collection called a **Dictionaries** or **dicts**. These are a bit different from what we have seen so far, they do not use indexes to order items. **Dictionaries** hold key-value pairs, where keys can be of any type. 

## What to expect from this article

In this article, we will discover the following about **dicts**:

* Anatomy of **dicts**
* Managing keys
* Packing and unpacking **dicts**
* Looping over **dicts**

## Anatomy of dicts

With a **dict** we can store any kind of data in the form of key-value pairs enclosed between curly brackets. Keys can be anything and any type but it is common to use meaningful strings. See code snippet below:

```python
anfelo = {
    "name": "Andres Osorio",
    "age": 27,
    "hobbies": ["puzzles", "sports", "coffee"]
}
```

We can store any type of data inside a **dict** even more dicts. 

## Managing keys

To access values from a dictionary is very similar to lists, however, instead of using indexes, we specify a key in the square braquests.

```python
color = chili["color"]
print(color)
# -> "red"
```

However, if the key does not exist, python will raise a KeyError just as lists would do. 

Dictionaries are mutable so we can change any key if existent:

```python
anfelo["hobbies"].append("reading books")
print(anfelo["hobbies"])
# -> ["puzzles", "sports", "coffee", "reading books"]
```

Again, if the key does not exist, we would get a KeyError.

There are two handy methods to avoid the KeyError raising. The first is the `get()` method which takes two parameters, the key we want to look for and a default value if the key does not exist. 

```python
print(anfelo.get("age", 0))
# -> 27
print(anfelo.get("favorite_book", "The Hobbit"))
# ->"The Hobbit"
```  

The key `"favorite_book"` does not exist in the dict `anfelo` but we didn't get KeyError because we passed a default value. However, that key was not created.

The second handy method is `setdefault()`, which takes two parameters as well, the key we want to set and a default value to set the key. If the key already exists, `setdefault()` returns the value, if not sets the key to the default value.

```python
anfelo.setdefault("favorite_book", "The Hobbit")
print(anfelo["favorite_book"])
# -> "The Hobbit"
``` 

That last one is one of my favorite **dict** methods.

Deleting items from a **dict** works just how you would expect, with the `del` keyword:

```python
del anfelo["age"]
print(anfelo.get("age", 0))
# -> 0
``` 

## Packing and unpacking dicts

Packing and unpacking is a concept used mostly with functions, 

There is a special way to pack keyword arguments of a function into a **dicts**, which is by specifying `**kwargs` as the receiving argument of a function. See code snippet below:

```python
def packer(**kwargs):
    print(kwargs)  # {"name": "Andres", "age": 27}

packer(name="Andres", age=27)
```

See how the arguments passed to the function got packed into the dict `kwargs`? This can be used with any number of arguments.

We can also reverse this last example to create a function that unpacks a **dict**. We do so by adding the `**` before the **dict** in the function call.

```python
def unpacker(name=None, age=None):
    print(name, age)  # "Andres", 27

unpacker(**{"name": "Andres", "age": 27})
```

The `**` tells python to unpack the **dict**.


## Looping over dicts

As lists or tuples, **dicts** offer a way to loop through its items. Actually we can use several methods to loop through a **dict's** keys and values.

The first one is with a normal `for in` loop, that iterates over the **dict's** keys:

```python
anfelo = {
    "name": "Andres Osorio",
    "age": 27,
    "hobbies": ["puzzles", "sports", "coffee"]
}

for key in anfelo:
    print(anfelo[key])

# "Andres Osorio"
# 27
# ["puzzles", "sports", "coffee"]
```

Another way to loop through the keys of a **dict** is with the `keys()` method, which return a list-like object that can be iterated. This is a better performant way to loop through keys.

```python
for key in anfelo.keys():
    print(key)

# "name"
# "age"
# "hobbies"
```

We can loop through only the values of the **dict** with the `values()` method.

```python
for value in anfelo.values():
    print(value)

# "Andres Osorio"
# 27
# ["puzzles", "sports", "coffee"]
```

If we needed for some reason the keys, we do not have access to them with the `values()` method.

The last way is a very handy one. We can have access to both keys and values at the same time! The `items()` method returns a list-like of tuples that we can unpack ([See tuple unpacking](/blog/python-collections-tuples/)). 

```python
for key, value in anfelo.items():
    print("{}: {}".format(key, value))
   
# "name": "Andres Osorio"
# "age": 27
# "hobbies": ["puzzles", "sports", "coffee"]
```

I love how all collection types can be iterated using the same method!


## Conclusion

Dictionaries are a great data structure that can be used when you don't need a specific order on the items. 

This is what we covered in this article:

* Dictionaries consist of key-value pairs contained between curly brackets
* We can access values with the square bracket notation and passing the key between the brackets
* `get` and `setdefault` are handy methods to get and set keys without getting a KeyError
* With `**` we can pack or unpack **dicts** using function
* There are three handy methods to loop through a **dict** `keys()`, `values()`and `items()`

Also check my article on [lists](/blog/python-collections-lists/) and [tuples](/blog/python-collections-tuples/)

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!
