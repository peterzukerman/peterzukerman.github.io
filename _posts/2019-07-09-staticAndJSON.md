---
layout: post
title: Static Methods/Variables and JSON, a quick Python Primer
excerpt: What are static method/variables, and why do we use them? How do I work with JSON in Python?
tags:       [programming, python, english]
--- 
In this post I want to give a quick background on the use cases of static methods and variables, and touch on JSON parsing in Python.
## Static Variables
Simply put, static variables are variables that have a lifetime as long as the program. Non-static variables are allocated on the stack, and are destroyed after the method that created them is finished. But static variables continue to function.

`​`` python
class example:
    staticVariable = 5

print(Example.staticVariable) # prints 5

# Access through an instance
instance = Example()
print(instance.staticVariable) # still 5

# Change within an instance
instance.staticVariable = 6
print(instance.staticVariable) # 6
print(Example.staticVariable) # 5

# Change through
class Example.staticVariable = 7
print(instance.staticVariable) # still 6
print(Example.staticVariable) # now 7
`​``

It’s clear from this example - one variable stays with the instance, and changes whenever the instance is accessed. The static variable stays with the class, and has a larger scope.

## Static Methods
Static methods are similar to static variables, since they’re also tied to the class, and not an instance. They are particularly useful when you need to know information about all instances that you create. For example, if you have a car factory and need to count how many cars you have produced, it would make sense to use a static method to perform this calculation. 

In Python, you mark a static method with the tag

`​`` python
@staticmethod
`​``

So, a similar example to above

`​`` python
class Example:
    name = "Example"

    @staticmethod
    def static():
        print("{}s static() called").format(Example.name)

class Offspring1(Example):
    name = "Offspring1"

class Offspring2(Example):
    name = "Offspring2"

    @staticmethod
    def static():
        print ("{0}s static() called").format(Offspring2.name)

Example.static() # prints Example
Offspring1.static() # prints Example
Offspring2.static() # prints Offspring2
`​``
As you can see, it is very similar to static variables. Keep in mind that static methods do not require an argument.
## JSON
JSON files are a simple way to organize data. They are similar to dictionaries. Python has JSON support through the json library:

`​`` python
import json
`​``

Let's make a JSON string and parse it into a JSON object.

`​`` python 
json_string = '{"first_name": "Bob", "last_name": "Smith"}'
parsed_json = json.loads(json_string)
`​``

You can also do it like this:

`​`` python
d = {
    'first_name': 'Bob',
    'second_name': 'Smith',
    'titles': ['President', 'Janitor'],
}

print(json.dumps(d)) #'{"first_name": "Bob", "last_name": "Smith", "titles": ["President", "Janitor"]}'
`​``

Now we can work with this data as if it were a string or a dictionary. If we want to write JSON into a file, we can do this:

`​`` python
with open('test.json', 'w') as file:
    json.dump(myFile, file)
`​``

And if we want to read the file we just created:

`​`` python
with open('test.json', 'r') as myFile:
    json_data = json.load(myFile)
    print(json_data)
`​``

Keep in mind that json.load and json.loads are not the same. json.load is used with a file, whereas json.loads is used with a string.

Also, json.dump is used when we want to dump JSON into a file, whereas json.dumps is used when we need to dump the JSON data as a string.

## Loading JSON into Pandas

Sometimes, we need to more control over our data for various data science tasks. We might want to load a JSON file using Pandas, and then use the various Pandas methods for more control.

`​`` python
import pandas as pd
data = pd.read_json("https://api.github.com/users")
df = pd.DataFrame(data)
`​``

And now we have a pandas data object we can work with.

Hopefully you learned something new! Thank you for reading.