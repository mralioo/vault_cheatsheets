# Except statement


```python
try:
   print(1/0)
except ZeroDivisionError:
   print("You cannot divide a value with zero")
except:
   print("Something else went wrong")
```


```python
try:
   with open('data.csv') as file:
       read_data = file.read()
except FileNotFoundError as fnf_error:
   print(fnf_error)
   print("Explanation: We cannot load the 'data.csv' file")

```


### try with else Clause

![](../figures/Pasted%20image%2020230817132949.png)

```python
try:
   result = 1/3
except ZeroDivisionError as err:
   print(err)
else:
   print(f"Your answer is {result}")
```
### finally

![](../figures/Pasted%20image%2020230817133147.png)
```python
def divide(x,y):
    try:
        result = x/y
    except ZeroDivisionError:
        print("Please change 'y' argument to non-zero value")
    except:
        print("Something went wrong")
    else:
        print(f"Your answer is {result}")
    finally:
        print("\033[92m Code by DataCamp\033[00m")

```

### Nested Exception Handling

```python
def file_editor(path,text):
    try:
      data = open(path)

      try:
        data.write(text)

      except:
        print("Unable to write the data. Please add an append: 'a' or write: 'w' parameter to the open() function.")

      finally:
        data.close()

    except:
      print(f"{path} file is not found!!")
```



## Raising Exceptions

To throw an exception, we have to use the `raise` keyword followed by an exception name.

```python
value = 2_000
if value > 1_000:   
    # raise the ValueError
    raise ValueError("Please add a value lower than 1,000")
else:
    print("Congratulations! You are the winner!!")
```


### Handling raised exception

 Instead of throwing the exception and terminating the program, it will display the error message that we have provided.

```python
value = 2_000
try:
    if value > 1_000:
          
        # raise the ValueError
        raise ValueError("Please add a value lower than 1,000")
    else:
        print("Congratulations! You are the winner!!")
              
# if false then raise the value error
except ValueError as e:
        print(e)
```

## [Following Best Practices When Raising Exceptions](https://realpython.com/python-raise-exception/#following-best-practices-when-raising-exceptions "Permanent link")

When it comes to raising exceptions in Python, you can follow a few practices and recommendations that will make your life more pleasant. Here’s a summary of some of these practices and recommendations:

- **Favor specific exceptions over generic ones**: You should raise the most specific exception that suits your needs. This practice will help you track down and fix problems and errors.
    
- **Provide informative error messages and avoid exceptions with no message**: You should write descriptive and explicit error messages for all your exceptions. This practice will provide a context for those debugging the code.
    
- **Favor built-in exceptions over custom exceptions**: You should try to find an appropriate built-in exception for every error in your code before writing your own exception. This practice will ensure consistency with the rest of the Python ecosystem. Most experienced Python developers will be familiar with the most common built-in exceptions, making it easier for them to understand and work with your code.
    
- **Avoid raising the `AssertionError` exception**: You should avoid raising the `AssertionError` in your code. This exception is specifically for the [`assert`](https://realpython.com/python-assert-statement/) statement, and it’s not appropriate in other contexts.
    
- **Raise exceptions as soon as possible**: You should check error conditions and exceptional situations early in your code. This practice will make your code more efficient by avoiding unnecessary processing that a delayed error check could throw away. This practice fits the [fail-fast](https://en.wikipedia.org/wiki/Fail-fast) design.
    
- **Explain the raised exceptions in your code’s documentation**: You should explicitly list and explain all the exceptions that a given piece of code could raise. This practice helps other developers understand which exceptions they should expect and how they can handle them appropriately.

## References 

* [Datacamp](https://www.datacamp.com/tutorial/exception-handling-python?utm_source=google&utm_medium=paid_search&utm_campaignid=19589720818&utm_adgroupid=143216588777&utm_device=c&utm_keyword=&utm_matchtype=&utm_network=g&utm_adpostion=&utm_creative=661628555465&utm_targetid=dsa-1947282172981&utm_loc_interest_ms=&utm_loc_physical_ms=9043154&utm_content=dsa~page~community-tuto&utm_campaign=230119_1-sea~dsa~tutorials_2-b2c_3-n-eu_4-prc_5-na_6-na_7-le_8-pdsh-go_9-na_10-na_11-na&gclid=CjwKCAjwivemBhBhEiwAJxNWNw3wxd5S-n2rD9cUhruH7i3f_uJW4G5fZbN0QxNxMhkB8Z5TPcP-bRoC9ZoQAvD_BwE)
* [Real Python](https://realpython.com/python-raise-exception/)
