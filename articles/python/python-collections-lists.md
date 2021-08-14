Collections are a way of grouping stuff in a container for later use. They provide different methods to access the stored items and also to manipulate these items. Some of these collection structures are mutable, meaning that they can be changed in place, or immutable if they are fixed and won't allow changes to the contents.

This is the first part of a series of articles on python collections. We will uncover a very common type of collection in Python, called **list**.

### What to spect from this article

The following list of topics will be covered:

* What are **lists**
* How to create a **list**?
* How to add items to the **list**?
* How to access items in the **list**?
* List concatenation and replication
* Removing items from a list
* Looping over a list's items
* Sorting the list


### What are lists?

Lists are a collection of any type of varibles, it can be an int, a string, a bool or anything you'd like. The main characteristics of lists are:

* Lists are mutable: Can be modified in place.
* Lists are ordered: Each item has an index inside the list.
* Lists are iterable: can be loop through

### How are lists created?

There are two ways to create a list, either with comma-separated items between `[...]` or by using the `list()` class. See code snippet below:

```python
my_first_list = ['tomatoes', 'oranges', 'apples']
# ['tomatoes', 'oranges', 'apples']

my_first_list2 = list('apples') 
# ['a', 'p', 'p', 'l', 'e', 's']
```

The fisrt list is created using the square braquets and we can add multiple items at ones. This is the prefered way to create a list. 

The second method uses the `list()` class to create a list passing an iterable as the first argument.


### How to add items to a list?

As lists are mutable, we can continue adding stuff into them and modifying the existing items. There are several methods to add items to a list. Lets see them in action:

```python
my_shopping_list = ['beer', 'apples', 'milk']

# append(item) method -> single items
my_shopping_list.append('snacks')

# extend(iterable) method -> mutiple items
my_shopping_list.extend(['eggs', 'butter'])

# insert(index, item) method -> custom position
my_shopping_list.insert(0, 'coffee')
```

The first method `append()`, is used to add single items to the end of the list. The second mehtod `extend()`, is used to add multiple items contained in an iterable directly to the list. The last method `ìnsert()`, is mostly used when we desire to add the item in certain position.

These are the main ways of adding items to a list. Later we will see how to concatenate two or more lists.


### How to access items in a list?

As we say before, lists are ordered collections, meaning that each item has an identifier (index) inside the list. To access a particular item we need to know the index of that item and use the square braquets notation: See below:

```python
print(my_shopping_list[0])
# coffee
```
We could also modify a particular item knowing its index.

```python
my_shopping_list[0] = 'colombian coffee'
print(my_shopping_list[0])
# colombian coffee
```

Python won't mind because lists are mutable.

### List concatenation and replication

Just like strings, lists can be concatenated with other strings using the `+` and `+=` operator. 

```python
my_shopping_list + ['onions', 'bread']
print(my_shopping_list)
# ['colombian coffee', 'beer', 'apples', 'milk', 'snacks', 'eggs', 'butter']
```
However, if we don't use the `+=` operator, our list won't be updated.

```python
my_shopping_list += ['onions', 'bread']
print(my_shopping_list)
# ['colombian coffee', 'beer', 'apples', 'milk', 'snacks', 'eggs', 'butter', 'onions', 'bread']
```
Much better!

We can also replicate the contents of a list as many times as we want with the `*` operator.

```python
a_list = ['a'] * 3
print(a_list)
# ['a', 'a', 'a']
```

### Removing items from a list

What if we want to eliminate items from the list? 

Don't worry there are 3 different methods to do it:

```python
my_shopping_list = ['eggs', 'butter', 'onions', 'bread']

# 'del' keyword
del my_shopping_list[2] # removed 'onions'

# remove(item) method -> removes the first item that matches
my_shopping_list.remove('butter')

# pop(index) -> remove item at index and returns the deleted item
removed_item = my_shopping_list.pop(0)
print(removed_item)
# 'eggs'
```

I personally find the last method very handy `pop()`.

### Looping over a list's items

Lists are iterables, meaning we can loop through them. We use the `for...in` syntax.

```python
my_shopping_list = ['eggs', 'butter', 'onions', 'bread']

for index in my_shopping_list:
    print(my_shopping_list[index])
    
# eggs
# butter
# onions
# bread
``` 

Pretty easy right?


### Sorting items

They wouldn't be called ordered lists if they hadn't a method to sort it's contents right? As you can imagine there is a method for doing that.

```python
my_num_list = [4, 6, 1, 3.9, 0.1]
print(my_num_list.sort())
# [0.1, 1, 3.9, 4, 6]

print(my_num_list.sort(reverse=True))
# [6, 4, 3.9, 1, 0.1]

my_letter_list = ['c', 'a', 'f', 'b']
print(my_letter_list.sort())
# ['a', 'b', 'c', 'f']
```

Great it works with numbers and strings as well! 

However, if you try to sort a list mixed with numbers and strings, you will get a TypeError. 


### Conclusion

This was just a quick overview of lists in Python. I encourage you to play around with them, they will be very useful in your day to day programing. If you want to learn more about lists, make sure you read the [documentation](https://docs.python.org/3.6/tutorial/datastructures.html). I recommend a book called [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/), is one of my favorites and it's FREE online!! 

---

You made it until the end!

These articles are meant to be an exercise in my learning journey but if they help you as well, that is a happy coincidence!

If you have some comment or suggestion please contact me, I’ll be happy to see some feedback or suggestion topics for my next articles.

Thanks a lot for your time! You are the best!