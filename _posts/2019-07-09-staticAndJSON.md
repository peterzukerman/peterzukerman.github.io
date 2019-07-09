# 
- - - -
layout: post
title: Static Methods/Variables and JSON, a quick Python Primer
- - - -
#blog
In this post I want to give a quick background on the use cases of static methods and variables, and touch on JSON parsing in Python.

## Static Variables
Simply put, static variables are variables that have a lifetime as long as the program. Non-static variables are allocated on the stack, and are destroyed after the method that created them is finished. But static variables continue to function.

```
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
```

It’s clear from this example - one variable stays with the instance, and changes whenever the instance is accessed. The static variable stays with the class, and has a larger scope.

## Static Methods
Static methods are similar to static variables, since they’re also tied to the class, and not an instance. They are particularly useful when you need to know information about all instances that you create. For example, if you have a car factory and need to count how many cars you have produced, it would make sense to use a static method to perform this calculation. 

In Python, you mark a static method with the tag

{ % highlight python % }
@staticmethod
{ % endhighlight % }

So, a similar example to above

``` python
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
```

As you can see, it is very similar to static variables. Keep in mind that static methods do not require an argument.

## JSON