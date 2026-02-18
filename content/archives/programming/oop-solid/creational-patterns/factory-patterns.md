---
title: "Factory Patterns"
topic: "Creational Design Patterns"
difficulty: "Medium"
tags: ["factory", "abstract-factory", "factory-method", "creational", "design-pattern"]
status: "complete"
weight: 2
---

# Factory Patterns

## 📚 Overview

Factory patterns deal with object creation, hiding the instantiation logic from the client.

1. **Simple Factory** - Not a GoF pattern but commonly used
2. **Factory Method** - Define interface, let subclasses decide what to create
3. **Abstract Factory** - Create families of related objects

---

## 1️⃣ Simple Factory

```python
from enum import Enum
from abc import ABC, abstractmethod

class VehicleType(Enum):
    CAR = "car"
    BIKE = "bike"
    TRUCK = "truck"

class Vehicle(ABC):
    @abstractmethod
    def drive(self) -> str:
        pass

class Car(Vehicle):
    def drive(self) -> str:
        return "Driving a car 🚗"

class Bike(Vehicle):
    def drive(self) -> str:
        return "Riding a bike 🏍️"

class Truck(Vehicle):
    def drive(self) -> str:
        return "Driving a truck 🚚"


class VehicleFactory:
    """Simple Factory - centralized creation logic"""
    
    @staticmethod
    def create(vehicle_type: VehicleType) -> Vehicle:
        factories = {
            VehicleType.CAR: Car,
            VehicleType.BIKE: Bike,
            VehicleType.TRUCK: Truck,
        }
        
        if vehicle_type not in factories:
            raise ValueError(f"Unknown vehicle type: {vehicle_type}")
        
        return factories[vehicle_type]()


# Usage
car = VehicleFactory.create(VehicleType.CAR)
print(car.drive())  # "Driving a car 🚗"
```

---

## 2️⃣ Factory Method Pattern

**Intent:** Define an interface for creating objects, but let subclasses decide which class to instantiate.

```python
from abc import ABC, abstractmethod

# Product hierarchy
class Document(ABC):
    @abstractmethod
    def create(self) -> str:
        pass
    
    @abstractmethod
    def save(self) -> str:
        pass

class PDFDocument(Document):
    def create(self) -> str:
        return "Creating PDF document"
    
    def save(self) -> str:
        return "Saving as .pdf"

class WordDocument(Document):
    def create(self) -> str:
        return "Creating Word document"
    
    def save(self) -> str:
        return "Saving as .docx"

class ExcelDocument(Document):
    def create(self) -> str:
        return "Creating Excel spreadsheet"
    
    def save(self) -> str:
        return "Saving as .xlsx"


# Creator hierarchy
class Application(ABC):
    @abstractmethod
    def create_document(self) -> Document:
        """Factory Method - subclasses override this"""
        pass
    
    def new_document(self) -> str:
        """Template method using factory method"""
        doc = self.create_document()
        result = doc.create()
        return result
    
    def save_document(self) -> str:
        doc = self.create_document()
        return doc.save()


class PDFApplication(Application):
    def create_document(self) -> Document:
        return PDFDocument()

class WordApplication(Application):
    def create_document(self) -> Document:
        return WordDocument()

class ExcelApplication(Application):
    def create_document(self) -> Document:
        return ExcelDocument()


# Usage
apps = [PDFApplication(), WordApplication(), ExcelApplication()]
for app in apps:
    print(app.new_document())
```

### Factory Method with Registration

```python
class DocumentFactory:
    """Extensible factory using registration"""
    _creators = {}
    
    @classmethod
    def register(cls, doc_type: str, creator):
        cls._creators[doc_type] = creator
    
    @classmethod
    def create(cls, doc_type: str, **kwargs) -> Document:
        creator = cls._creators.get(doc_type)
        if not creator:
            raise ValueError(f"Unknown document type: {doc_type}")
        return creator(**kwargs)


# Register creators
DocumentFactory.register("pdf", PDFDocument)
DocumentFactory.register("word", WordDocument)
DocumentFactory.register("excel", ExcelDocument)

# Usage
doc = DocumentFactory.create("pdf")
```

---

## 3️⃣ Abstract Factory Pattern

**Intent:** Create families of related objects without specifying concrete classes.

```python
from abc import ABC, abstractmethod

# Abstract Products
class Button(ABC):
    @abstractmethod
    def render(self) -> str:
        pass
    
    @abstractmethod
    def on_click(self, callback) -> str:
        pass

class Checkbox(ABC):
    @abstractmethod
    def render(self) -> str:
        pass
    
    @abstractmethod
    def toggle(self) -> str:
        pass

class TextInput(ABC):
    @abstractmethod
    def render(self) -> str:
        pass
    
    @abstractmethod
    def get_value(self) -> str:
        pass


# Windows Family
class WindowsButton(Button):
    def render(self) -> str:
        return "[====Windows Button====]"
    
    def on_click(self, callback) -> str:
        return f"Windows click: {callback}"

class WindowsCheckbox(Checkbox):
    def render(self) -> str:
        return "[☑] Windows Checkbox"
    
    def toggle(self) -> str:
        return "Windows checkbox toggled"

class WindowsTextInput(TextInput):
    def render(self) -> str:
        return "|____Windows Input____|"
    
    def get_value(self) -> str:
        return "Windows input value"


# Mac Family
class MacButton(Button):
    def render(self) -> str:
        return "(  Mac Button  )"
    
    def on_click(self, callback) -> str:
        return f"Mac click: {callback}"

class MacCheckbox(Checkbox):
    def render(self) -> str:
        return "(✓) Mac Checkbox"
    
    def toggle(self) -> str:
        return "Mac checkbox toggled"

class MacTextInput(TextInput):
    def render(self) -> str:
        return "(___Mac Input___)"
    
    def get_value(self) -> str:
        return "Mac input value"


# Linux Family
class LinuxButton(Button):
    def render(self) -> str:
        return "<Linux Button>"
    
    def on_click(self, callback) -> str:
        return f"Linux click: {callback}"

class LinuxCheckbox(Checkbox):
    def render(self) -> str:
        return "<X> Linux Checkbox"
    
    def toggle(self) -> str:
        return "Linux checkbox toggled"

class LinuxTextInput(TextInput):
    def render(self) -> str:
        return "<__Linux Input__>"
    
    def get_value(self) -> str:
        return "Linux input value"


# Abstract Factory
class GUIFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button:
        pass
    
    @abstractmethod
    def create_checkbox(self) -> Checkbox:
        pass
    
    @abstractmethod
    def create_text_input(self) -> TextInput:
        pass


# Concrete Factories
class WindowsFactory(GUIFactory):
    def create_button(self) -> Button:
        return WindowsButton()
    
    def create_checkbox(self) -> Checkbox:
        return WindowsCheckbox()
    
    def create_text_input(self) -> TextInput:
        return WindowsTextInput()


class MacFactory(GUIFactory):
    def create_button(self) -> Button:
        return MacButton()
    
    def create_checkbox(self) -> Checkbox:
        return MacCheckbox()
    
    def create_text_input(self) -> TextInput:
        return MacTextInput()


class LinuxFactory(GUIFactory):
    def create_button(self) -> Button:
        return LinuxButton()
    
    def create_checkbox(self) -> Checkbox:
        return LinuxCheckbox()
    
    def create_text_input(self) -> TextInput:
        return LinuxTextInput()


# Client code - works with any factory
class Application:
    def __init__(self, factory: GUIFactory):
        self.factory = factory
        self.button = None
        self.checkbox = None
        self.text_input = None
    
    def create_ui(self):
        self.button = self.factory.create_button()
        self.checkbox = self.factory.create_checkbox()
        self.text_input = self.factory.create_text_input()
    
    def render(self) -> str:
        return "\n".join([
            self.button.render(),
            self.checkbox.render(),
            self.text_input.render()
        ])


# Factory selector
def get_factory(os_type: str) -> GUIFactory:
    factories = {
        "windows": WindowsFactory(),
        "mac": MacFactory(),
        "linux": LinuxFactory()
    }
    return factories.get(os_type.lower(), LinuxFactory())


# Usage
import platform
os_type = platform.system().lower()
factory = get_factory(os_type)

app = Application(factory)
app.create_ui()
print(app.render())
```

---

## 🏢 Real-World Examples

### Database Connection Factory

```python
from abc import ABC, abstractmethod
from typing import Optional

class DatabaseConnection(ABC):
    @abstractmethod
    def connect(self) -> str:
        pass
    
    @abstractmethod
    def execute(self, query: str) -> list:
        pass
    
    @abstractmethod
    def close(self) -> None:
        pass

class MySQLConnection(DatabaseConnection):
    def __init__(self, host: str, port: int, database: str):
        self.host = host
        self.port = port
        self.database = database
        self._connection = None
    
    def connect(self) -> str:
        self._connection = f"MySQL://{self.host}:{self.port}/{self.database}"
        return f"Connected to MySQL at {self.host}"
    
    def execute(self, query: str) -> list:
        return [f"MySQL result for: {query}"]
    
    def close(self) -> None:
        self._connection = None

class PostgreSQLConnection(DatabaseConnection):
    def __init__(self, host: str, port: int, database: str):
        self.host = host
        self.port = port
        self.database = database
        self._connection = None
    
    def connect(self) -> str:
        self._connection = f"PostgreSQL://{self.host}:{self.port}/{self.database}"
        return f"Connected to PostgreSQL at {self.host}"
    
    def execute(self, query: str) -> list:
        return [f"PostgreSQL result for: {query}"]
    
    def close(self) -> None:
        self._connection = None

class MongoDBConnection(DatabaseConnection):
    def __init__(self, host: str, port: int, database: str):
        self.host = host
        self.port = port
        self.database = database
    
    def connect(self) -> str:
        return f"Connected to MongoDB at {self.host}"
    
    def execute(self, query: str) -> list:
        return [f"MongoDB result for: {query}"]
    
    def close(self) -> None:
        pass


class DatabaseFactory:
    @staticmethod
    def create(db_type: str, host: str, port: int, database: str) -> DatabaseConnection:
        factories = {
            "mysql": MySQLConnection,
            "postgresql": PostgreSQLConnection,
            "postgres": PostgreSQLConnection,
            "mongodb": MongoDBConnection,
            "mongo": MongoDBConnection,
        }
        
        creator = factories.get(db_type.lower())
        if not creator:
            raise ValueError(f"Unsupported database: {db_type}")
        
        return creator(host, port, database)


# Usage
config = {"type": "postgresql", "host": "localhost", "port": 5432, "database": "myapp"}
db = DatabaseFactory.create(**config)
print(db.connect())
```

### Payment Processor Factory

```python
class PaymentProcessor(ABC):
    @abstractmethod
    def process(self, amount: float) -> dict:
        pass
    
    @abstractmethod
    def refund(self, transaction_id: str, amount: float) -> dict:
        pass

class StripeProcessor(PaymentProcessor):
    def __init__(self, api_key: str):
        self.api_key = api_key
    
    def process(self, amount: float) -> dict:
        return {"provider": "stripe", "amount": amount, "status": "success"}
    
    def refund(self, transaction_id: str, amount: float) -> dict:
        return {"provider": "stripe", "refund": amount, "status": "refunded"}

class PayPalProcessor(PaymentProcessor):
    def __init__(self, client_id: str, client_secret: str):
        self.client_id = client_id
        self.client_secret = client_secret
    
    def process(self, amount: float) -> dict:
        return {"provider": "paypal", "amount": amount, "status": "success"}
    
    def refund(self, transaction_id: str, amount: float) -> dict:
        return {"provider": "paypal", "refund": amount, "status": "refunded"}

class SquareProcessor(PaymentProcessor):
    def __init__(self, access_token: str):
        self.access_token = access_token
    
    def process(self, amount: float) -> dict:
        return {"provider": "square", "amount": amount, "status": "success"}
    
    def refund(self, transaction_id: str, amount: float) -> dict:
        return {"provider": "square", "refund": amount, "status": "refunded"}


class PaymentFactory:
    _processors = {}
    
    @classmethod
    def register(cls, name: str, processor_cls, **default_kwargs):
        cls._processors[name] = (processor_cls, default_kwargs)
    
    @classmethod
    def create(cls, name: str, **kwargs) -> PaymentProcessor:
        if name not in cls._processors:
            raise ValueError(f"Unknown payment processor: {name}")
        
        processor_cls, defaults = cls._processors[name]
        merged_kwargs = {**defaults, **kwargs}
        return processor_cls(**merged_kwargs)


# Register processors
PaymentFactory.register("stripe", StripeProcessor, api_key="sk_test_xxx")
PaymentFactory.register("paypal", PayPalProcessor, client_id="xxx", client_secret="yyy")
PaymentFactory.register("square", SquareProcessor, access_token="sq_xxx")

# Usage
processor = PaymentFactory.create("stripe")
result = processor.process(99.99)
```

---

## 🔄 Comparison

| Aspect | Simple Factory | Factory Method | Abstract Factory |
|--------|---------------|----------------|------------------|
| **Complexity** | Low | Medium | High |
| **Extensibility** | Modify factory | Add new subclass | Add new factory |
| **Products** | One type | One type | Family of types |
| **Creation** | Static method | Inheritance | Composition |
| **When to use** | Simple needs | Need variations | Related products |

---

## ✅ When to Use

### Factory Method
- Class can't anticipate the objects it must create
- Class wants subclasses to specify created objects
- Need to localize object creation

### Abstract Factory
- System should be independent of product creation
- System needs to work with multiple product families
- Related products must be used together

---

## 📚 Related Patterns

- **Singleton** - Factories often are singletons
- **Prototype** - Alternative to factory when classes vary at runtime
- **Builder** - For complex object construction

---

*Last Updated: 2024*
