

F-strings, introduced in Python 3.6, provide a concise and readable way to format strings. They offer several tricks and features to manipulate and format strings effectively.

#### 1. Basic Formatting

```python
name = "Alice"
age = 30
print(f"My name is {name} and I am {age} years old.")
# Output: My name is Alice and I am 30 years old.
```

#### 2. Arithmetic Operations

```python
x = 5
y = 10
print(f"The sum of {x} and {y} is {x + y}.")
# Output: The sum of 5 and 10 is 15.
```

#### 3. Dictionary Access

```python
person = {'name': 'Bob', 'age': 25}
print(f"Name: {person['name']}, Age: {person['age']}")
# Output: Name: Bob, Age: 25
```

#### 4. Format Specification

```python
num = 3.141592653589793
print(f"Pi value: {num:.2f}")
# Output: Pi value: 3.14
```

#### 5. Expression Evaluation

```python
a = 5
b = 3
print(f"The result is {(lambda x, y: x + y)(a, b)}")
# Output: The result is 8
```

#### 6. Call Functions

```python
def greet(name):
    return f"Hello, {name}!"

print(greet("Alice"))
# Output: Hello, Alice!
```

#### 7. Multiline Strings

```python
message = (
    f"This is a multiline "
    f"string with {x} "
    f"interpolated variables."
)
print(message)
# Output: This is a multiline string with 5 interpolated variables.
```

#### 8. Nested F-Strings

```python
name = "Alice"
age = 30
print(f"Hello, {name.upper()}! You are {age} years old.")
# Output: Hello, ALICE! You are 30 years old.
```

F-strings provide a powerful and flexible way to format strings in Python, making code more readable and maintainable.


##### 9. Sugar syntax

```python

a = 5
b = 6
my_var = "Alice"
print(f"{a+b = }")

# Output: a+b = 15


print(f"{my_var = }")

# Output: my_var = "Alice"
```


# Resources 

* https://www.youtube.com/watch?v=EoNOWVYKyo0&ab_channel=Indently