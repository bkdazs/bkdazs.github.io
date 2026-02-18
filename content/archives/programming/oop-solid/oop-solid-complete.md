---
title: "OOP and SOLID Principles"
topic: "OOP-SOLID"
difficulty: "Medium"
tags: ["oop", "solid", "design", "python", "principles"]
status: "complete"
weight: 1
---

# Object-Oriented Programming and SOLID Principles

## 📚 Summary

This guide covers the four pillars of OOP (Encapsulation, Abstraction, Inheritance, Polymorphism) and the five SOLID principles for writing maintainable, extensible code.

---

## 1️⃣ Four Pillars of OOP

### Encapsulation

Bundling data and methods that operate on that data within a single unit (class), and restricting direct access to some components.

```python
class BankAccount:
    """Encapsulation example: internal state is protected."""
    
    def __init__(self, owner: str, initial_balance: float = 0):
        self._owner = owner           # Protected (convention)
        self.__balance = initial_balance  # Private (name mangling)
    
    @property
    def balance(self) -> float:
        """Read-only access to balance."""
        return self.__balance
    
    @property
    def owner(self) -> str:
        return self._owner
    
    def deposit(self, amount: float) -> None:
        """Controlled modification of balance."""
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self.__balance += amount
    
    def withdraw(self, amount: float) -> bool:
        """Controlled modification with validation."""
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive")
        if amount > self.__balance:
            return False
        self.__balance -= amount
        return True


# Usage
account = BankAccount("Alice", 1000)
account.deposit(500)
print(account.balance)  # 1500 (via property)
# account.__balance = 999999  # Won't work - name mangling
```

### Abstraction

Hiding complex implementation details and exposing only necessary interfaces.

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    """Abstract base class defining payment interface."""
    
    @abstractmethod
    def process_payment(self, amount: float) -> bool:
        """Process a payment. Must be implemented by subclasses."""
        pass
    
    @abstractmethod
    def refund(self, transaction_id: str) -> bool:
        """Refund a transaction. Must be implemented by subclasses."""
        pass


class StripeProcessor(PaymentProcessor):
    """Concrete implementation hiding Stripe API complexity."""
    
    def __init__(self, api_key: str):
        self._api_key = api_key
    
    def process_payment(self, amount: float) -> bool:
        # Complex Stripe API interaction hidden here
        print(f"Processing ${amount} via Stripe...")
        return True
    
    def refund(self, transaction_id: str) -> bool:
        print(f"Refunding transaction {transaction_id} via Stripe...")
        return True


class PayPalProcessor(PaymentProcessor):
    """Concrete implementation for PayPal."""
    
    def process_payment(self, amount: float) -> bool:
        print(f"Processing ${amount} via PayPal...")
        return True
    
    def refund(self, transaction_id: str) -> bool:
        print(f"Refunding {transaction_id} via PayPal...")
        return True


# Client code doesn't need to know implementation details
def checkout(processor: PaymentProcessor, amount: float) -> None:
    if processor.process_payment(amount):
        print("Payment successful!")
```

### Inheritance

Creating new classes based on existing classes, inheriting their attributes and methods.

```python
class Animal:
    """Base class for all animals."""
    
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age
    
    def speak(self) -> str:
        return "Some sound"
    
    def info(self) -> str:
        return f"{self.name} is {self.age} years old"


class Dog(Animal):
    """Dog inherits from Animal."""
    
    def __init__(self, name: str, age: int, breed: str):
        super().__init__(name, age)  # Call parent constructor
        self.breed = breed
    
    def speak(self) -> str:  # Override parent method
        return "Woof!"
    
    def fetch(self) -> str:  # Dog-specific method
        return f"{self.name} is fetching!"


class Cat(Animal):
    """Cat inherits from Animal."""
    
    def speak(self) -> str:
        return "Meow!"
    
    def scratch(self) -> str:
        return f"{self.name} is scratching!"


# Usage
dog = Dog("Buddy", 3, "Golden Retriever")
print(dog.speak())  # "Woof!"
print(dog.info())   # "Buddy is 3 years old" (inherited)
```

### Polymorphism

Objects of different types can be treated uniformly through a common interface.

```python
from typing import List

class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass
    
    @abstractmethod
    def perimeter(self) -> float:
        pass


class Rectangle(Shape):
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    
    def area(self) -> float:
        return self.width * self.height
    
    def perimeter(self) -> float:
        return 2 * (self.width + self.height)


class Circle(Shape):
    def __init__(self, radius: float):
        self.radius = radius
    
    def area(self) -> float:
        import math
        return math.pi * self.radius ** 2
    
    def perimeter(self) -> float:
        import math
        return 2 * math.pi * self.radius


# Polymorphism in action
def total_area(shapes: List[Shape]) -> float:
    """Works with any Shape subclass."""
    return sum(shape.area() for shape in shapes)


shapes = [Rectangle(4, 5), Circle(3), Rectangle(2, 3)]
print(total_area(shapes))  # Works regardless of specific shape types
```

---

## 2️⃣ SOLID Principles

### S - Single Responsibility Principle (SRP)

**A class should have only one reason to change.**

```python
# ❌ BAD: Class has multiple responsibilities
class User:
    def __init__(self, name: str, email: str):
        self.name = name
        self.email = email
    
    def save_to_database(self):
        # Database logic
        pass
    
    def send_email(self, message: str):
        # Email logic
        pass
    
    def generate_report(self):
        # Report generation logic
        pass


# ✅ GOOD: Separate responsibilities into different classes
class User:
    def __init__(self, name: str, email: str):
        self.name = name
        self.email = email


class UserRepository:
    def save(self, user: User) -> None:
        # Database logic only
        pass
    
    def find_by_email(self, email: str) -> User:
        pass


class EmailService:
    def send(self, to: str, subject: str, body: str) -> None:
        # Email logic only
        pass


class UserReportGenerator:
    def generate(self, user: User) -> str:
        # Report logic only
        pass
```

### O - Open/Closed Principle (OCP)

**Classes should be open for extension but closed for modification.**

```python
# ❌ BAD: Need to modify class to add new discount types
class DiscountCalculator:
    def calculate(self, order_total: float, discount_type: str) -> float:
        if discount_type == "percentage":
            return order_total * 0.1
        elif discount_type == "fixed":
            return 10.0
        elif discount_type == "bogo":  # Added later - modifies existing class
            return order_total * 0.5
        return 0


# ✅ GOOD: Extend behavior without modifying existing code
class DiscountStrategy(ABC):
    @abstractmethod
    def calculate(self, order_total: float) -> float:
        pass


class PercentageDiscount(DiscountStrategy):
    def __init__(self, percentage: float):
        self.percentage = percentage
    
    def calculate(self, order_total: float) -> float:
        return order_total * self.percentage


class FixedDiscount(DiscountStrategy):
    def __init__(self, amount: float):
        self.amount = amount
    
    def calculate(self, order_total: float) -> float:
        return min(self.amount, order_total)


class BOGODiscount(DiscountStrategy):
    """New discount type - no modification to existing code!"""
    def calculate(self, order_total: float) -> float:
        return order_total * 0.5


class Order:
    def __init__(self, total: float, discount: DiscountStrategy = None):
        self.total = total
        self.discount = discount
    
    def final_price(self) -> float:
        if self.discount:
            return self.total - self.discount.calculate(self.total)
        return self.total
```

### L - Liskov Substitution Principle (LSP)

**Subtypes must be substitutable for their base types without altering program correctness.**

```python
# ❌ BAD: Square violates LSP when inheriting from Rectangle
class Rectangle:
    def __init__(self, width: float, height: float):
        self._width = width
        self._height = height
    
    @property
    def width(self) -> float:
        return self._width
    
    @width.setter
    def width(self, value: float) -> None:
        self._width = value
    
    @property
    def height(self) -> float:
        return self._height
    
    @height.setter
    def height(self, value: float) -> None:
        self._height = value
    
    def area(self) -> float:
        return self._width * self._height


class Square(Rectangle):  # Problematic inheritance
    def __init__(self, side: float):
        super().__init__(side, side)
    
    @Rectangle.width.setter
    def width(self, value: float) -> None:
        self._width = value
        self._height = value  # Forces height to change too!
    
    @Rectangle.height.setter
    def height(self, value: float) -> None:
        self._width = value
        self._height = value


# This breaks expectations:
def resize_rectangle(rect: Rectangle):
    rect.width = 5
    rect.height = 10
    assert rect.area() == 50  # Fails for Square!


# ✅ GOOD: Use composition or separate hierarchies
class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass


class Rectangle(Shape):
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    
    def area(self) -> float:
        return self.width * self.height


class Square(Shape):
    def __init__(self, side: float):
        self.side = side
    
    def area(self) -> float:
        return self.side ** 2
```

### I - Interface Segregation Principle (ISP)

**Clients should not be forced to depend on interfaces they don't use.**

```python
# ❌ BAD: Fat interface forces implementations to have unused methods
class Worker(ABC):
    @abstractmethod
    def work(self) -> None:
        pass
    
    @abstractmethod
    def eat(self) -> None:
        pass
    
    @abstractmethod
    def sleep(self) -> None:
        pass


class Robot(Worker):  # Robot doesn't eat or sleep!
    def work(self) -> None:
        print("Robot working")
    
    def eat(self) -> None:
        pass  # Forced to implement useless method
    
    def sleep(self) -> None:
        pass  # Forced to implement useless method


# ✅ GOOD: Segregated interfaces
class Workable(ABC):
    @abstractmethod
    def work(self) -> None:
        pass


class Eatable(ABC):
    @abstractmethod
    def eat(self) -> None:
        pass


class Sleepable(ABC):
    @abstractmethod
    def sleep(self) -> None:
        pass


class Human(Workable, Eatable, Sleepable):
    def work(self) -> None:
        print("Human working")
    
    def eat(self) -> None:
        print("Human eating")
    
    def sleep(self) -> None:
        print("Human sleeping")


class Robot(Workable):  # Only implements what it needs
    def work(self) -> None:
        print("Robot working")
```

### D - Dependency Inversion Principle (DIP)

**High-level modules should not depend on low-level modules. Both should depend on abstractions.**

```python
# ❌ BAD: High-level module depends on low-level module
class MySQLDatabase:
    def query(self, sql: str) -> list:
        # MySQL specific implementation
        pass


class UserService:
    def __init__(self):
        self.db = MySQLDatabase()  # Tight coupling to MySQL
    
    def get_users(self) -> list:
        return self.db.query("SELECT * FROM users")


# ✅ GOOD: Depend on abstractions
class Database(ABC):
    @abstractmethod
    def query(self, sql: str) -> list:
        pass


class MySQLDatabase(Database):
    def query(self, sql: str) -> list:
        print("MySQL query")
        return []


class PostgreSQLDatabase(Database):
    def query(self, sql: str) -> list:
        print("PostgreSQL query")
        return []


class UserService:
    def __init__(self, database: Database):  # Depends on abstraction
        self.db = database
    
    def get_users(self) -> list:
        return self.db.query("SELECT * FROM users")


# Easy to swap implementations
mysql_service = UserService(MySQLDatabase())
postgres_service = UserService(PostgreSQLDatabase())
```

---

## 3️⃣ Design Patterns Quick Reference

### Creational Patterns

```python
# Singleton
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance


# Factory Method
class AnimalFactory:
    @staticmethod
    def create(animal_type: str) -> Animal:
        if animal_type == "dog":
            return Dog("Buddy", 3, "Lab")
        elif animal_type == "cat":
            return Cat("Whiskers", 2)
        raise ValueError(f"Unknown animal type: {animal_type}")


# Builder
class Pizza:
    def __init__(self):
        self.size = None
        self.cheese = False
        self.pepperoni = False
        self.mushrooms = False


class PizzaBuilder:
    def __init__(self):
        self.pizza = Pizza()
    
    def set_size(self, size: str) -> 'PizzaBuilder':
        self.pizza.size = size
        return self
    
    def add_cheese(self) -> 'PizzaBuilder':
        self.pizza.cheese = True
        return self
    
    def add_pepperoni(self) -> 'PizzaBuilder':
        self.pizza.pepperoni = True
        return self
    
    def build(self) -> Pizza:
        return self.pizza


# Usage: pizza = PizzaBuilder().set_size("large").add_cheese().add_pepperoni().build()
```

### Structural Patterns

```python
# Adapter
class LegacyPrinter:
    def print_document(self, text: str) -> None:
        print(f"Legacy: {text}")


class ModernPrinter(ABC):
    @abstractmethod
    def print(self, content: str) -> None:
        pass


class PrinterAdapter(ModernPrinter):
    def __init__(self, legacy_printer: LegacyPrinter):
        self.legacy = legacy_printer
    
    def print(self, content: str) -> None:
        self.legacy.print_document(content)


# Decorator
class Coffee(ABC):
    @abstractmethod
    def cost(self) -> float:
        pass


class SimpleCoffee(Coffee):
    def cost(self) -> float:
        return 2.0


class MilkDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee
    
    def cost(self) -> float:
        return self._coffee.cost() + 0.5


# Usage: coffee = MilkDecorator(SimpleCoffee())  # cost = 2.5
```

### Behavioral Patterns

```python
# Strategy
class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data: list) -> list:
        pass


class QuickSort(SortStrategy):
    def sort(self, data: list) -> list:
        # Quick sort implementation
        return sorted(data)


class Sorter:
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy
    
    def sort(self, data: list) -> list:
        return self._strategy.sort(data)


# Observer
class Subject:
    def __init__(self):
        self._observers = []
    
    def attach(self, observer) -> None:
        self._observers.append(observer)
    
    def notify(self, message: str) -> None:
        for observer in self._observers:
            observer.update(message)


class Observer(ABC):
    @abstractmethod
    def update(self, message: str) -> None:
        pass
```

---

## 📚 Interview Questions

### Common OOP Questions

1. **What are the four pillars of OOP?**
2. **Explain the difference between abstraction and encapsulation.**
3. **What is the diamond problem in multiple inheritance?**
4. **When would you use composition over inheritance?**
5. **What is method overloading vs method overriding?**

### SOLID Questions

1. **Explain each SOLID principle with examples.**
2. **How does DIP help with testing?**
3. **Give an example of LSP violation.**
4. **How would you refactor a god class using SRP?**
5. **What's the relationship between OCP and Strategy pattern?**

---

## 🔑 Key Takeaways

1. **Encapsulation**: Hide internal state, expose controlled access
2. **Abstraction**: Define interfaces, hide complexity
3. **Inheritance**: Reuse code, establish "is-a" relationships
4. **Polymorphism**: Program to interfaces, not implementations
5. **SOLID**: Write maintainable, extensible, testable code

---

*Last Updated: 2024*
