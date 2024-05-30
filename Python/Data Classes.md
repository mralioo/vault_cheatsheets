
Data classes were introduced in Python 3.7 and have undergone further enhancements in Python 3.10. These classes are designed to facilitate the creation of classes primarily meant for storing data rather than implementing complex behaviors. Unlike traditional object-oriented classes that often combine both data storage and methods for manipulating that data, data classes focus solely on efficiently representing structured data.

## Benefits of Data Classes:

1. **Conciseness**: They reduce boilerplate code by automatically generating special methods like `__init__`, `__repr__`, `__eq__`, and `__hash__` based on the class attributes, saving developers from having to write them explicitly.

2. **Readability**: By clearly defining the structure of the data within the class definition, data classes enhance code readability and maintainability, making it easier for developers to understand the purpose and usage of the class.

3. **Immutability**: Data classes can be made immutable by specifying attributes as read-only using type hints, ensuring that once an instance is created, its data cannot be modified, which can help prevent unintended side effects in code.

4. **Integration with Type Checking**: They seamlessly integrate with Python's type hinting system, allowing developers to specify the types of attributes for improved code clarity and enabling type checkers like Mypy to catch potential type-related errors at compile time.

Overall, data classes provide a convenient and intuitive way to define and work with structured data in Python, offering a more declarative and concise approach compared to traditional object-oriented classes for scenarios where the primary focus is on data storage and manipulation rather than behavior implementation.

---

#### Example:

```python
from dataclasses import dataclass
from datetime import datetime

@dataclass
class Payment:
    amount: float
    payer: str
    payment_method: str
    timestamp: datetime

# Creating an instance of Payment
payment_1 = Payment(amount=100.0, payer="John Doe", payment_method="Credit Card", timestamp=datetime.now())

# Accessing attributes
print(payment_1.amount)           # Output: 100.0
print(payment_1.payer)            # Output: John Doe
print(payment_1.payment_method)   # Output: Credit Card
print(payment_1.timestamp)        # Output: Current timestamp](<# Old way without data class
class PaymentOld:
    def __init__(self, amount, payer, payment_method, timestamp):
        self.amount = amount
        self.payer = payer
        self.payment_method = payment_method
        self.timestamp = timestamp

# Using the old way
payment_old = PaymentOld(amount=100.0, payer="John Doe", payment_method="Credit Card", timestamp=datetime.now())

# Accessing attributes
print(payment_old.amount)            # Output: 100.0
print(payment_old.payer)             # Output: John Doe
print(payment_old.payment_method)    # Output: Credit Card
print(payment_old.timestamp)         # Output: Current timestamp

# Using data classes
from dataclasses import dataclass
from datetime import datetime

@dataclass
class PaymentDataClass:
    amount: float
    payer: str
    payment_method: str
    timestamp: datetime

# Creating an instance of PaymentDataClass
payment_data_class = PaymentDataClass(amount=100.0, payer="John Doe", payment_method="Credit Card", timestamp=datetime.now())

# Accessing attributes
print(payment_data_class.amount)            # Output: 100.0
print(payment_data_class.payer)             # Output: John Doe
print(payment_data_class.payment_method)    # Output: Credit Card
print(payment_data_class.timestamp)         # Output: Current timestamp>)
```

In this example, we define a data class `Payment` to represent payment details. It has attributes `amount`, `payer`, `payment_method`, and `timestamp`. We create an instance of `Payment` and access its attributes to retrieve the payment details.













## Resources 

* https://www.youtube.com/watch?v=CvQ7e6yUtnw&ab_channel=ArjanCodes
* 