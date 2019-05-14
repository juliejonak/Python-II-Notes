# NOTES

We have a store called ET World that has:

1. Apparel
2. Shoes
3. Accessories
4. Exit

There are three categories of things that are being sold or are in the store. The last item (exit) is an action that can be taken by someone in the store. There are two categories here.

We want to be able to type in a number corresponding to an area of the store, and see a list of products in that area.

Let's make our store class, using the keyword "class" followed by the name (usually capitalized):

```class Store:```


We'll need to define a specific function to create the attributes of this class:

```
class Store:
    def _init_(self):
        # attributes here
```

We always need to use _init_ because it is a constructor for creating classes, that needs to be passed at least one specific parameter: self. There can be following parameters too, but it must always have at least self.

```
class Store:
    def _init_(self, name, categories):
        self.name = name
        self.categories = categories
```

To give the class initial values, we set self.THING to something, like self.name = name. 

When we call init in our program, we're trying to take specific values and assign them to specific Store instances. Like, on this specific store, set the name equal to "Sports Store" or "Cooking". 

Self is like the "this" keyword in JavaScript - it's a reference to THIS specific object in memory that we're trying to initialize or change. It indicates which object. We are setting the "name" value on THIS object, the Sports Store.

It binds attributes to a specific instance.

Let's test this out by creating a store:

```
my_store = Store("The Dugout", ["Running", "Baseball", "Basketball"])
print(my_store)
```

This should print out a new instance of the Store class, but instead we get:

```
<_main_.Store object at 0x1048560b8>
```

What is the random number? It references the location in memory where our Store object is, written in hexidecimal. However, that's not particularly useful to us. We could override the default behaviour of the string method though. If we don't define it, the default behaviour is to print out the memory location.

```
class Store:
    def __init__(self, name, categories):
        self.name = name
        self.categories = categories

    def __repr__(self):
      # Name
      # 1. + categories[0]
      # 2. + categories[1]
```

We can get a string representation of an object in a few ways ( str(), repr() ). Generally, str() is used to output to an end user while repr() is used for debugging and development (output to the dev).  (More info: https://www.pythoncentral.io/what-is-the-difference-between-__str__-and-__repr__-in-python/) 

```
class Store:
    def __init__(self, name, categories):
        self.name = name
        self.categories = categories

    def __repr__(self):
      output = ""
      output += self.name + '\n'
      i = 1
      for c in self.categories:
          output += str(i) + '. ' + c + '\n'
          i += 1
      return output
```

** NOTE: The underscores before and after init, repr and str must be double underscores, not single.


Now the output in the terminal when we print:

```
my_store = Store("The Dugout", ["Running", "Baseball", "Basketball"])
print(my_store)
```

Is:

The Dugout
1. Running
2. Baseball
3. Basketball


Now let's get our user to select a department so they can then see what inventory is there using some if/else logic:

```
selection = input("Select the number of a department")
if int(selection) > 0 and int(selection) <= len(my_store.categories):
    print("The user selected " + str(selection))
else:
    print("Please select a valid number")
```

Now our user can type in any valid number that exists, even if we add categories, and will return an error message if the user inputs something outside of the expected range.

But if we input a letter or any other non-integer, this will break. We need to still handle this type of error.

We can "catch" the specific error happening and run an alternate block of code. The error we're getting is:

```
ValueError: invalid literal for int() with base 10: 'g'
```

So let's add a try/catch block using the try/except syntax:

```
try:
    if int(selection) > 0 and int(selection) <= len(my_store.categories):
        print("The user selected " + str(selection))
    else:
        print("Please select a valid number")
except ValueError:
    print('Please enter your choice as a number.')
```

Now if the user enters a letter instead, a more accurate error message is sent to them. Ideally, we'd also allow them to enter a new input and try again.

That handles a specific ValueError (and there are other specific error messages that we can handle) but we could also create a more general error handling too.

To allow the user to continue to input choices, we would wrap our logic in a while loop. We could do this by tacking on a fourth option that is a hard coded exit:

```
def __repr__(self):
        output = ''
        output += self.name + '\n'
        i = 1
        for c in self.categories:
            output += str(i) + '. ' + c + '\n'
            i += 1
        output += str(i) + '. Exit'
        return output
```

And then we would need to tweak the accepted range and wrap the entire segment in a while loop:

```
selection = 0
while int(selection) != len(my_store.categories)+1:
    selection = input("Select the number of a department ")
    try:
        if int(selection) > 0 and int(selection) <= len(my_store.categories)+1:
            print("The user selected " + str(selection))
        else:
            print("Please select a valid number")
    except ValueError:
        print('Please enter your choice as a number.')
```

However, then it no longer handles a non-integer issue. The better way to handle this is to cast our selection as an integer within our try catch:

```
selection = 0
while selection != len(my_store.categories)+1:
    selection = input("Select the number of a department ")
    try:
        selection = int(selection)
        if selection > 0 and selection <= len(my_store.categories)+1:
            print("The user selected " + selection)
        else:
            print("Please select a valid number")
    except ValueError:
        print('Please enter your choice as a number.')

print('Goodbye!')
```

This allows us to remove our extra str() and int() because selection is cast as we want it to be used throughout.

All of this is kept inside of a store.py file. Let's now create a category.py file to setup a category class:

```
class Category:

    def __init__(self, name):
    # add later: product list parameter
        self.name = name

    def __repr__(self):
        return "No products in " + self.name 
```

Instead of hard coding strings into our store, we could use the category class to create an instance of each category in our store.py file:

```
my_store = Store("The Dugout", [Category("Running"), Category("Baseball"), Category("Basketball")])
print(my_store)
```

But we need to import the category class in order to use it within our file:

```
from category import Category
```

The syntax when we create a class is to "from FILENAME import CLASSNAME"

Now we're running into an error:

```
TypeError: can only concatenate str (not "Category") to str
```

Because before, the Categories were just string literals, it was fine to concat them into a string, but now we need to pull the name out of that instance of category to use when printing, like so:

```
        for c in self.categories:
            output += str(i) + '. ' + c.name + '\n'
            i += 1
```

We have to make sure to call the name of the instance to pull that string, because we only want the name, not the entire instance.

Now how could we print the cateory instead of "the user selected..." when they make a department choice?

```
            print(f"You chose {my_store.categories[selection-1].name}.")
```

This now triggers an error when we try to exit though because so we need to slightly adjust our if/else loop:

```
    try:
        selection = int(selection)
        if selection > 0 and selection < len(my_store.categories)+1:
            print(f"You chose {my_store.categories[selection-1].name}.")
        elif(selection > len(my_store.categories)+1):
            print("Please select a valid number")
```

Or by nesting the if statements:

```
if selection > 0 and selection <= len(my_store.categories)+1:
            if selection == len(my_store.categories)+1:
                exit()
                
            print(f"You chose {my_store.categories[selection-1].name}.")
```