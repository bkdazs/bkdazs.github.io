---
title: "Builder Pattern"
topic: "Creational Design Patterns"
difficulty: "Medium"
tags: ["builder", "fluent-interface", "creational", "design-pattern"]
status: "complete"
weight: 3
---

# Builder Pattern

## 📚 Overview

**Intent:** Separate the construction of a complex object from its representation, allowing the same construction process to create different representations.

**Use When:**
- Object requires many steps to construct
- Object can have different representations
- Need to create immutable objects with many optional parameters

---

## 1️⃣ Classic Builder Pattern

```python
from abc import ABC, abstractmethod
from typing import Optional, List

# Product
class Computer:
    def __init__(self):
        self.cpu: Optional[str] = None
        self.ram: Optional[str] = None
        self.storage: Optional[str] = None
        self.gpu: Optional[str] = None
        self.os: Optional[str] = None
        self.peripherals: List[str] = []
    
    def __str__(self) -> str:
        specs = [
            f"CPU: {self.cpu}",
            f"RAM: {self.ram}",
            f"Storage: {self.storage}",
            f"GPU: {self.gpu}",
            f"OS: {self.os}",
            f"Peripherals: {', '.join(self.peripherals) or 'None'}"
        ]
        return "\n".join(specs)


# Abstract Builder
class ComputerBuilder(ABC):
    @abstractmethod
    def reset(self) -> None:
        pass
    
    @abstractmethod
    def set_cpu(self) -> None:
        pass
    
    @abstractmethod
    def set_ram(self) -> None:
        pass
    
    @abstractmethod
    def set_storage(self) -> None:
        pass
    
    @abstractmethod
    def set_gpu(self) -> None:
        pass
    
    @abstractmethod
    def set_os(self) -> None:
        pass
    
    @abstractmethod
    def add_peripherals(self) -> None:
        pass
    
    @abstractmethod
    def get_computer(self) -> Computer:
        pass


# Concrete Builders
class GamingComputerBuilder(ComputerBuilder):
    def __init__(self):
        self.computer = Computer()
    
    def reset(self) -> None:
        self.computer = Computer()
    
    def set_cpu(self) -> None:
        self.computer.cpu = "Intel Core i9-13900K"
    
    def set_ram(self) -> None:
        self.computer.ram = "64GB DDR5"
    
    def set_storage(self) -> None:
        self.computer.storage = "2TB NVMe SSD"
    
    def set_gpu(self) -> None:
        self.computer.gpu = "NVIDIA RTX 4090"
    
    def set_os(self) -> None:
        self.computer.os = "Windows 11 Pro"
    
    def add_peripherals(self) -> None:
        self.computer.peripherals = ["Gaming Mouse", "Mechanical Keyboard", "144Hz Monitor"]
    
    def get_computer(self) -> Computer:
        return self.computer


class OfficeComputerBuilder(ComputerBuilder):
    def __init__(self):
        self.computer = Computer()
    
    def reset(self) -> None:
        self.computer = Computer()
    
    def set_cpu(self) -> None:
        self.computer.cpu = "Intel Core i5-13400"
    
    def set_ram(self) -> None:
        self.computer.ram = "16GB DDR4"
    
    def set_storage(self) -> None:
        self.computer.storage = "512GB SSD"
    
    def set_gpu(self) -> None:
        self.computer.gpu = "Integrated Graphics"
    
    def set_os(self) -> None:
        self.computer.os = "Windows 11 Home"
    
    def add_peripherals(self) -> None:
        self.computer.peripherals = ["Standard Keyboard", "Optical Mouse"]
    
    def get_computer(self) -> Computer:
        return self.computer


# Director
class ComputerDirector:
    def __init__(self, builder: ComputerBuilder):
        self._builder = builder
    
    def change_builder(self, builder: ComputerBuilder) -> None:
        self._builder = builder
    
    def build_minimal(self) -> Computer:
        """Build computer with minimal components"""
        self._builder.reset()
        self._builder.set_cpu()
        self._builder.set_ram()
        self._builder.set_storage()
        return self._builder.get_computer()
    
    def build_full(self) -> Computer:
        """Build computer with all components"""
        self._builder.reset()
        self._builder.set_cpu()
        self._builder.set_ram()
        self._builder.set_storage()
        self._builder.set_gpu()
        self._builder.set_os()
        self._builder.add_peripherals()
        return self._builder.get_computer()


# Usage
gaming_builder = GamingComputerBuilder()
director = ComputerDirector(gaming_builder)

gaming_pc = director.build_full()
print("Gaming PC:")
print(gaming_pc)

print("\n" + "="*50 + "\n")

office_builder = OfficeComputerBuilder()
director.change_builder(office_builder)

office_pc = director.build_full()
print("Office PC:")
print(office_pc)
```

---

## 2️⃣ Fluent Builder (Modern Python Style)

```python
from dataclasses import dataclass, field
from typing import Optional, List


@dataclass
class HTTPRequest:
    """Immutable HTTP Request object"""
    method: str
    url: str
    headers: dict = field(default_factory=dict)
    body: Optional[str] = None
    params: dict = field(default_factory=dict)
    timeout: int = 30
    retries: int = 3


class HTTPRequestBuilder:
    """Fluent builder for HTTPRequest"""
    
    def __init__(self):
        self._method: str = "GET"
        self._url: str = ""
        self._headers: dict = {}
        self._body: Optional[str] = None
        self._params: dict = {}
        self._timeout: int = 30
        self._retries: int = 3
    
    def method(self, method: str) -> 'HTTPRequestBuilder':
        self._method = method.upper()
        return self
    
    def url(self, url: str) -> 'HTTPRequestBuilder':
        self._url = url
        return self
    
    def header(self, key: str, value: str) -> 'HTTPRequestBuilder':
        self._headers[key] = value
        return self
    
    def headers(self, headers: dict) -> 'HTTPRequestBuilder':
        self._headers.update(headers)
        return self
    
    def body(self, body: str) -> 'HTTPRequestBuilder':
        self._body = body
        return self
    
    def json_body(self, data: dict) -> 'HTTPRequestBuilder':
        import json
        self._body = json.dumps(data)
        self._headers["Content-Type"] = "application/json"
        return self
    
    def param(self, key: str, value: str) -> 'HTTPRequestBuilder':
        self._params[key] = value
        return self
    
    def params(self, params: dict) -> 'HTTPRequestBuilder':
        self._params.update(params)
        return self
    
    def timeout(self, seconds: int) -> 'HTTPRequestBuilder':
        self._timeout = seconds
        return self
    
    def retries(self, count: int) -> 'HTTPRequestBuilder':
        self._retries = count
        return self
    
    def build(self) -> HTTPRequest:
        if not self._url:
            raise ValueError("URL is required")
        
        return HTTPRequest(
            method=self._method,
            url=self._url,
            headers=self._headers.copy(),
            body=self._body,
            params=self._params.copy(),
            timeout=self._timeout,
            retries=self._retries
        )


# Usage with fluent interface
request = (HTTPRequestBuilder()
    .method("POST")
    .url("https://api.example.com/users")
    .header("Authorization", "Bearer token123")
    .json_body({"name": "John", "email": "john@example.com"})
    .timeout(60)
    .retries(5)
    .build())

print(request)
```

---

## 3️⃣ Step Builder (Enforced Build Order)

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass


@dataclass(frozen=True)
class Pizza:
    size: str
    crust: str
    sauce: str
    cheese: str
    toppings: tuple


# Step interfaces
class SizeStep(ABC):
    @abstractmethod
    def size(self, size: str) -> 'CrustStep':
        pass

class CrustStep(ABC):
    @abstractmethod
    def crust(self, crust: str) -> 'SauceStep':
        pass

class SauceStep(ABC):
    @abstractmethod
    def sauce(self, sauce: str) -> 'CheeseStep':
        pass

class CheeseStep(ABC):
    @abstractmethod
    def cheese(self, cheese: str) -> 'ToppingsStep':
        pass

class ToppingsStep(ABC):
    @abstractmethod
    def topping(self, topping: str) -> 'ToppingsStep':
        pass
    
    @abstractmethod
    def build(self) -> Pizza:
        pass


class PizzaBuilder(SizeStep, CrustStep, SauceStep, CheeseStep, ToppingsStep):
    """Step builder enforces correct build order at compile time"""
    
    def __init__(self):
        self._size: str = ""
        self._crust: str = ""
        self._sauce: str = ""
        self._cheese: str = ""
        self._toppings: list = []
    
    def size(self, size: str) -> 'CrustStep':
        self._size = size
        return self
    
    def crust(self, crust: str) -> 'SauceStep':
        self._crust = crust
        return self
    
    def sauce(self, sauce: str) -> 'CheeseStep':
        self._sauce = sauce
        return self
    
    def cheese(self, cheese: str) -> 'ToppingsStep':
        self._cheese = cheese
        return self
    
    def topping(self, topping: str) -> 'ToppingsStep':
        self._toppings.append(topping)
        return self
    
    def build(self) -> Pizza:
        return Pizza(
            size=self._size,
            crust=self._crust,
            sauce=self._sauce,
            cheese=self._cheese,
            toppings=tuple(self._toppings)
        )


def pizza() -> SizeStep:
    """Factory function returns first step"""
    return PizzaBuilder()


# Usage - IDE enforces correct order
my_pizza = (pizza()
    .size("Large")           # Must call size first
    .crust("Thin")           # Then crust
    .sauce("Marinara")       # Then sauce  
    .cheese("Mozzarella")    # Then cheese
    .topping("Pepperoni")    # Then optional toppings
    .topping("Mushrooms")
    .build())                # Finally build

print(my_pizza)
```

---

## 4️⃣ Generic Builder with Validation

```python
from typing import TypeVar, Generic, Callable, Dict, Any, List
from dataclasses import dataclass

T = TypeVar('T')


class ValidationError(Exception):
    def __init__(self, errors: List[str]):
        self.errors = errors
        super().__init__(f"Validation failed: {errors}")


class GenericBuilder(Generic[T]):
    """Generic builder with validation support"""
    
    def __init__(self, cls: type):
        self._cls = cls
        self._values: Dict[str, Any] = {}
        self._validators: Dict[str, List[Callable]] = {}
    
    def set(self, field: str, value: Any) -> 'GenericBuilder[T]':
        self._values[field] = value
        return self
    
    def validate(self, field: str, validator: Callable[[Any], bool], 
                 message: str) -> 'GenericBuilder[T]':
        if field not in self._validators:
            self._validators[field] = []
        self._validators[field].append((validator, message))
        return self
    
    def build(self) -> T:
        errors = []
        
        for field, validators in self._validators.items():
            value = self._values.get(field)
            for validator, message in validators:
                if not validator(value):
                    errors.append(f"{field}: {message}")
        
        if errors:
            raise ValidationError(errors)
        
        return self._cls(**self._values)


@dataclass
class User:
    name: str
    email: str
    age: int
    role: str = "user"


# Usage with validation
def is_valid_email(email: str) -> bool:
    return email and "@" in email and "." in email

def is_adult(age: int) -> bool:
    return age is not None and age >= 18

def is_valid_role(role: str) -> bool:
    return role in ["user", "admin", "moderator"]


user = (GenericBuilder(User)
    .set("name", "John Doe")
    .set("email", "john@example.com")
    .set("age", 25)
    .set("role", "admin")
    .validate("email", is_valid_email, "Invalid email format")
    .validate("age", is_adult, "Must be 18 or older")
    .validate("role", is_valid_role, "Invalid role")
    .build())

print(user)
```

---

## 🏢 Real-World Examples

### SQL Query Builder

```python
from typing import List, Optional, Tuple, Any
from enum import Enum

class JoinType(Enum):
    INNER = "INNER JOIN"
    LEFT = "LEFT JOIN"
    RIGHT = "RIGHT JOIN"
    FULL = "FULL OUTER JOIN"


class QueryBuilder:
    """SQL Query Builder with fluent interface"""
    
    def __init__(self):
        self._select: List[str] = []
        self._from: Optional[str] = None
        self._joins: List[Tuple[JoinType, str, str]] = []
        self._where: List[str] = []
        self._group_by: List[str] = []
        self._having: List[str] = []
        self._order_by: List[Tuple[str, str]] = []
        self._limit: Optional[int] = None
        self._offset: Optional[int] = None
        self._params: List[Any] = []
    
    def select(self, *columns: str) -> 'QueryBuilder':
        self._select.extend(columns)
        return self
    
    def from_table(self, table: str) -> 'QueryBuilder':
        self._from = table
        return self
    
    def join(self, table: str, on: str, 
             join_type: JoinType = JoinType.INNER) -> 'QueryBuilder':
        self._joins.append((join_type, table, on))
        return self
    
    def left_join(self, table: str, on: str) -> 'QueryBuilder':
        return self.join(table, on, JoinType.LEFT)
    
    def where(self, condition: str, *params: Any) -> 'QueryBuilder':
        self._where.append(condition)
        self._params.extend(params)
        return self
    
    def and_where(self, condition: str, *params: Any) -> 'QueryBuilder':
        return self.where(condition, *params)
    
    def or_where(self, condition: str, *params: Any) -> 'QueryBuilder':
        if self._where:
            self._where[-1] = f"({self._where[-1]} OR {condition})"
        else:
            self._where.append(condition)
        self._params.extend(params)
        return self
    
    def group_by(self, *columns: str) -> 'QueryBuilder':
        self._group_by.extend(columns)
        return self
    
    def having(self, condition: str) -> 'QueryBuilder':
        self._having.append(condition)
        return self
    
    def order_by(self, column: str, direction: str = "ASC") -> 'QueryBuilder':
        self._order_by.append((column, direction.upper()))
        return self
    
    def limit(self, limit: int) -> 'QueryBuilder':
        self._limit = limit
        return self
    
    def offset(self, offset: int) -> 'QueryBuilder':
        self._offset = offset
        return self
    
    def build(self) -> Tuple[str, List[Any]]:
        if not self._select:
            self._select = ["*"]
        
        if not self._from:
            raise ValueError("FROM clause is required")
        
        parts = [f"SELECT {', '.join(self._select)}"]
        parts.append(f"FROM {self._from}")
        
        for join_type, table, on in self._joins:
            parts.append(f"{join_type.value} {table} ON {on}")
        
        if self._where:
            parts.append(f"WHERE {' AND '.join(self._where)}")
        
        if self._group_by:
            parts.append(f"GROUP BY {', '.join(self._group_by)}")
        
        if self._having:
            parts.append(f"HAVING {' AND '.join(self._having)}")
        
        if self._order_by:
            order_parts = [f"{col} {dir}" for col, dir in self._order_by]
            parts.append(f"ORDER BY {', '.join(order_parts)}")
        
        if self._limit is not None:
            parts.append(f"LIMIT {self._limit}")
        
        if self._offset is not None:
            parts.append(f"OFFSET {self._offset}")
        
        return "\n".join(parts), self._params


# Usage
query, params = (QueryBuilder()
    .select("u.id", "u.name", "COUNT(o.id) as order_count")
    .from_table("users u")
    .left_join("orders o", "u.id = o.user_id")
    .where("u.status = ?", "active")
    .where("u.created_at > ?", "2024-01-01")
    .group_by("u.id", "u.name")
    .having("COUNT(o.id) > 5")
    .order_by("order_count", "DESC")
    .limit(10)
    .build())

print(query)
print(f"Params: {params}")
```

### HTML Builder

```python
class HTMLElement:
    def __init__(self, tag: str):
        self.tag = tag
        self.attributes: dict = {}
        self.children: list = []
        self.text: str = ""
    
    def render(self, indent: int = 0) -> str:
        spaces = "  " * indent
        attrs = " ".join(f'{k}="{v}"' for k, v in self.attributes.items())
        opening = f"{self.tag} {attrs}".strip() if attrs else self.tag
        
        if not self.children and not self.text:
            return f"{spaces}<{opening} />"
        
        parts = [f"{spaces}<{opening}>"]
        
        if self.text:
            parts.append(f"{spaces}  {self.text}")
        
        for child in self.children:
            parts.append(child.render(indent + 1))
        
        parts.append(f"{spaces}</{self.tag}>")
        return "\n".join(parts)


class HTMLBuilder:
    def __init__(self, tag: str):
        self.root = HTMLElement(tag)
        self._stack = [self.root]
    
    @property
    def _current(self) -> HTMLElement:
        return self._stack[-1]
    
    def attr(self, key: str, value: str) -> 'HTMLBuilder':
        self._current.attributes[key] = value
        return self
    
    def id(self, id_value: str) -> 'HTMLBuilder':
        return self.attr("id", id_value)
    
    def class_(self, class_value: str) -> 'HTMLBuilder':
        return self.attr("class", class_value)
    
    def text(self, content: str) -> 'HTMLBuilder':
        self._current.text = content
        return self
    
    def child(self, tag: str) -> 'HTMLBuilder':
        child = HTMLElement(tag)
        self._current.children.append(child)
        self._stack.append(child)
        return self
    
    def end(self) -> 'HTMLBuilder':
        if len(self._stack) > 1:
            self._stack.pop()
        return self
    
    def build(self) -> str:
        return self.root.render()


# Usage
html = (HTMLBuilder("div")
    .id("container")
    .class_("main-content")
    .child("h1")
        .class_("title")
        .text("Welcome")
    .end()
    .child("ul")
        .class_("nav-list")
        .child("li").text("Home").end()
        .child("li").text("About").end()
        .child("li").text("Contact").end()
    .end()
    .build())

print(html)
```

---

## ❓ Interview Questions

1. **Q: Builder vs Factory - when to use which?**
   - Factory: Object can be created in single step
   - Builder: Object requires multiple steps or has many optional params

2. **Q: Why use Builder over telescoping constructors?**
   - Readability: Named parameters make code self-documenting
   - Flexibility: Optional parameters handled elegantly
   - Immutability: Can build immutable objects safely

3. **Q: Director's role in Builder pattern?**
   - Encapsulates construction algorithms
   - Reusable across different builders
   - Separates algorithm from representation

---

## 📝 Related Problems

| Problem | Platform | Key Pattern |
|---------|----------|-------------|
| Design Browser History | LeetCode #1472 | Step builder |
| Design Underground System | LeetCode #1396 | Builder with aggregation |
| Implement Trie | LeetCode #208 | Building complex structure |

---

*Last Updated: 2024*
