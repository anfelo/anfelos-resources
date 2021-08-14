This is the second article on Python Collections. In the first one we talked about [Lists](/blog/python-collections-lists/), the mutable, ordered collection. 

In this article we will talk about a very similar collection called a **Tuple**. These are just like lists but they are immutable, non-changing lists. 

### What to expect from this article

This is a short article where we will get more comfortable with:

* Creating tuples
* Understanding the immutability and its exceptions
* Tuple swaping 
* Packing and unpacking tuples
* Returning multiple values with functions


### How to create a Tuple?

Tuples are composed by multiple values separated by commas and most of the time organized between parenthesis. However, what really makes a tuple are the commas. Let us see an example.

```python
my_first_tuple = (1, 2, 3, 4)

another_tuple = 'a', 'b', 'c'

one_value_tuple = (5,)
```

As you can see, the use of parenthesis is optional, commas are what really defines a tuple. Note that, in order to create a tuple with one value, we need to add a comma after the value or Python will interpret it as a single value.


### Understanding the immutability and its exceptions

As mentioned before, tuples are immutable, meaning they can't be modified once created. However, we can modify items that are mutable inside of a tuple (like lists!). Lets see this in action.

```python
mixed_tuple = ([1, 2, 3], 'a', True)

mixed_tuple[0][1] = 8
print(mixed_tuple)
# ([1, 8, 3], 'a', True)
```

Python won't complain if try to modify mutable items inside a tuple.
This is something to understant so we don't run into surprises in future programs.

### Tuple swaping

Tuples are known for various cool things in Python, the first one and very useful one is tuple swaping of variable swaping. Imagine we have two variables `a` and `b` and for some reason we want to swap their values. Normally we would have to create a thrid varible `c`  to store the value of one of the variable to safely make the swap.

```python 
a = 1
b = 2
c = b
b = a # b = 1
a = c # a = 2
```

But what if I tell you there is a better way to do this using tuples.

```python 
a = 1
b = 2
a, b = b, a # Voilà!
```

By creating a tuple at the right side of the expresion and assigning it to multiple variables at the left side of the expresion we can accomplish the swap successfully. No need for a thrid varible! Python rules! 

This is also called tuple unpacking. We 'unpack' a tuple into multiple variables.

### Packing tuples

We can also pack tuples. It is mostly used when passing multiple parameters to functions. 

```python
def add(*nums):
    total = 0
    for num in nums:
        total += num
    return total


print(add(1, 5, 8, 10))
# 24
```

The function add can receive any amount of numbers as arguments because it uses the `*` to pack them all into the `nums` variable. This is also a very powerful tool used when we do not know the exact number of arguments a function will receive.

### Multiple return values

This is one of my favorite features of tuples. Have you ever seen a function that returns multiple values? 

No?

You are about to see one in action:

```python
def get_coordinates():
    longitude = '-74.063644'
    latitude = '4.624335'
    return latitude, longitude


lat, lon = get_coordinates()
```

By making our funtion `get_coordinates` return a tuple we can then unpack this tuple into multiple variables. This is a very powerful feature as you can see! 

### Conclusion

This was a very brief article about tuples. We discovered the hidden powers of tuples and also its limitations. Now you know how to:

* Create a tuple
* Perform tuple swaping
* Pack and unpack tuples
* Return multiple variables with functions

If you want to learn more about this collection, make sure to read the [documentation](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences).

Also check my article on [lists](/blog/python-collections-lists/) and [dictionaries](/blog/python-collections-dicts/)


---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!