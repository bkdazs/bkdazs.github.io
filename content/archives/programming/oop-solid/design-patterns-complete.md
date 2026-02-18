---
title: "Design Patterns - Complete Guide"
topic: "Design Patterns"
difficulty: "Medium-Hard"
tags: ["design-patterns", "creational", "structural", "behavioral", "gang-of-four"]
status: "complete"
weight: 1
---

# Design Patterns - Complete Guide

## 📚 Summary

Design patterns are reusable solutions to common software design problems. This guide covers all 23 Gang of Four (GoF) patterns organized into three categories: Creational, Structural, and Behavioral.

---

## 1️⃣ Creational Patterns

*Control object creation mechanisms.*

### Singleton

**Intent:** Ensure a class has only one instance and provide global access.

```python
from threading import Lock

class Singleton:
    """Thread-safe Singleton implementation."""
    _instance = None
    _lock = Lock()
    
    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        self.value = None


# Usage
s1 = Singleton()
s2 = Singleton()
assert s1 is s2  # Same instance


# Alternative: Using decorator
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance


@singleton
class Database:
    def __init__(self):
        self.connection = "Connected"
```

**Use Cases:** Configuration manager, logging, connection pools, caches.

---

### Factory Method

**Intent:** Define interface for creating objects, let subclasses decide which class to instantiate.

```python
from abc import ABC, abstractmethod

class Document(ABC):
    @abstractmethod
    def render(self) -> str:
        pass


class PDFDocument(Document):
    def render(self) -> str:
        return "Rendering PDF"


class HTMLDocument(Document):
    def render(self) -> str:
        return "Rendering HTML"


class DocumentFactory(ABC):
    @abstractmethod
    def create_document(self) -> Document:
        pass
    
    def open_document(self) -> str:
        doc = self.create_document()
        return doc.render()


class PDFFactory(DocumentFactory):
    def create_document(self) -> Document:
        return PDFDocument()


class HTMLFactory(DocumentFactory):
    def create_document(self) -> Document:
        return HTMLDocument()


# Usage
factory = PDFFactory()
print(factory.open_document())  # "Rendering PDF"
```

**Use Cases:** UI libraries, document processing, cross-platform apps.

---

### Abstract Factory

**Intent:** Create families of related objects without specifying concrete classes.

```python
class Button(ABC):
    @abstractmethod
    def render(self) -> str:
        pass


class Checkbox(ABC):
    @abstractmethod
    def render(self) -> str:
        pass


class WindowsButton(Button):
    def render(self) -> str:
        return "Windows Button"


class MacButton(Button):
    def render(self) -> str:
        return "Mac Button"


class WindowsCheckbox(Checkbox):
    def render(self) -> str:
        return "Windows Checkbox"


class MacCheckbox(Checkbox):
    def render(self) -> str:
        return "Mac Checkbox"


class GUIFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button:
        pass
    
    @abstractmethod
    def create_checkbox(self) -> Checkbox:
        pass


class WindowsFactory(GUIFactory):
    def create_button(self) -> Button:
        return WindowsButton()
    
    def create_checkbox(self) -> Checkbox:
        return WindowsCheckbox()


class MacFactory(GUIFactory):
    def create_button(self) -> Button:
        return MacButton()
    
    def create_checkbox(self) -> Checkbox:
        return MacCheckbox()


# Usage
def create_ui(factory: GUIFactory):
    button = factory.create_button()
    checkbox = factory.create_checkbox()
    return f"{button.render()}, {checkbox.render()}"


print(create_ui(WindowsFactory()))  # "Windows Button, Windows Checkbox"
```

---

### Builder

**Intent:** Separate construction of complex objects from their representation.

```python
class Computer:
    def __init__(self):
        self.cpu = None
        self.ram = None
        self.storage = None
        self.gpu = None
    
    def __str__(self):
        return f"CPU: {self.cpu}, RAM: {self.ram}, Storage: {self.storage}, GPU: {self.gpu}"


class ComputerBuilder:
    def __init__(self):
        self._computer = Computer()
    
    def set_cpu(self, cpu: str) -> 'ComputerBuilder':
        self._computer.cpu = cpu
        return self
    
    def set_ram(self, ram: str) -> 'ComputerBuilder':
        self._computer.ram = ram
        return self
    
    def set_storage(self, storage: str) -> 'ComputerBuilder':
        self._computer.storage = storage
        return self
    
    def set_gpu(self, gpu: str) -> 'ComputerBuilder':
        self._computer.gpu = gpu
        return self
    
    def build(self) -> Computer:
        return self._computer


class Director:
    @staticmethod
    def build_gaming_pc(builder: ComputerBuilder) -> Computer:
        return (builder
                .set_cpu("Intel i9")
                .set_ram("32GB")
                .set_storage("1TB SSD")
                .set_gpu("RTX 4090")
                .build())
    
    @staticmethod
    def build_office_pc(builder: ComputerBuilder) -> Computer:
        return (builder
                .set_cpu("Intel i5")
                .set_ram("16GB")
                .set_storage("512GB SSD")
                .build())


# Usage
gaming = Director.build_gaming_pc(ComputerBuilder())
office = Director.build_office_pc(ComputerBuilder())
```

---

### Prototype

**Intent:** Create new objects by copying existing ones.

```python
import copy

class Prototype(ABC):
    @abstractmethod
    def clone(self) -> 'Prototype':
        pass


class Document(Prototype):
    def __init__(self, title: str, content: str):
        self.title = title
        self.content = content
        self.images = []  # Complex nested object
    
    def clone(self) -> 'Document':
        # Deep copy for nested objects
        return copy.deepcopy(self)
    
    def __str__(self):
        return f"Document: {self.title}"


# Usage
original = Document("Template", "Default content")
original.images.append("logo.png")

cloned = original.clone()
cloned.title = "New Document"
cloned.images.append("chart.png")

print(len(original.images))  # 1 (deep copy, not affected)
print(len(cloned.images))    # 2
```

---

## 2️⃣ Structural Patterns

*Compose objects into larger structures.*

### Adapter

**Intent:** Convert interface of a class into another interface clients expect.

```python
class EuropeanSocket:
    def voltage(self) -> int:
        return 230
    
    def plug_type(self) -> str:
        return "Type C"


class USASocket:
    def voltage(self) -> int:
        return 120
    
    def plug_type(self) -> str:
        return "Type A"


class SocketAdapter:
    """Adapts European socket to work like USA socket."""
    
    def __init__(self, european_socket: EuropeanSocket):
        self._socket = european_socket
    
    def voltage(self) -> int:
        # Convert voltage
        return 120
    
    def plug_type(self) -> str:
        return "Type A (adapted)"


# Usage
euro = EuropeanSocket()
adapter = SocketAdapter(euro)
print(adapter.voltage())  # 120
```

---

### Bridge

**Intent:** Decouple abstraction from implementation so both can vary independently.

```python
class Renderer(ABC):
    @abstractmethod
    def render_circle(self, radius: float) -> str:
        pass


class VectorRenderer(Renderer):
    def render_circle(self, radius: float) -> str:
        return f"Drawing circle with radius {radius} as vectors"


class RasterRenderer(Renderer):
    def render_circle(self, radius: float) -> str:
        return f"Drawing circle with radius {radius} as pixels"


class Shape(ABC):
    def __init__(self, renderer: Renderer):
        self.renderer = renderer
    
    @abstractmethod
    def draw(self) -> str:
        pass


class Circle(Shape):
    def __init__(self, renderer: Renderer, radius: float):
        super().__init__(renderer)
        self.radius = radius
    
    def draw(self) -> str:
        return self.renderer.render_circle(self.radius)


# Usage
vector_circle = Circle(VectorRenderer(), 5)
raster_circle = Circle(RasterRenderer(), 5)
```

---

### Composite

**Intent:** Compose objects into tree structures; treat individual objects and compositions uniformly.

```python
class FileSystemComponent(ABC):
    @abstractmethod
    def get_size(self) -> int:
        pass
    
    @abstractmethod
    def display(self, indent: int = 0) -> str:
        pass


class File(FileSystemComponent):
    def __init__(self, name: str, size: int):
        self.name = name
        self.size = size
    
    def get_size(self) -> int:
        return self.size
    
    def display(self, indent: int = 0) -> str:
        return " " * indent + f"📄 {self.name} ({self.size}KB)"


class Directory(FileSystemComponent):
    def __init__(self, name: str):
        self.name = name
        self.children: list[FileSystemComponent] = []
    
    def add(self, component: FileSystemComponent) -> None:
        self.children.append(component)
    
    def get_size(self) -> int:
        return sum(child.get_size() for child in self.children)
    
    def display(self, indent: int = 0) -> str:
        result = " " * indent + f"📁 {self.name}/"
        for child in self.children:
            result += "\n" + child.display(indent + 2)
        return result


# Usage
root = Directory("root")
docs = Directory("docs")
docs.add(File("readme.txt", 5))
docs.add(File("notes.txt", 3))
root.add(docs)
root.add(File("config.json", 1))

print(root.display())
print(f"Total size: {root.get_size()}KB")
```

---

### Decorator

**Intent:** Attach additional responsibilities to objects dynamically.

```python
class Coffee(ABC):
    @abstractmethod
    def cost(self) -> float:
        pass
    
    @abstractmethod
    def description(self) -> str:
        pass


class SimpleCoffee(Coffee):
    def cost(self) -> float:
        return 2.0
    
    def description(self) -> str:
        return "Coffee"


class CoffeeDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee


class MilkDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 0.5
    
    def description(self) -> str:
        return self._coffee.description() + ", Milk"


class SugarDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 0.2
    
    def description(self) -> str:
        return self._coffee.description() + ", Sugar"


class WhipDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 0.7
    
    def description(self) -> str:
        return self._coffee.description() + ", Whip"


# Usage
coffee = SimpleCoffee()
coffee = MilkDecorator(coffee)
coffee = SugarDecorator(coffee)
coffee = WhipDecorator(coffee)

print(f"{coffee.description()}: ${coffee.cost()}")
# "Coffee, Milk, Sugar, Whip: $3.4"
```

---

### Facade

**Intent:** Provide unified interface to a set of interfaces in a subsystem.

```python
class CPU:
    def freeze(self): print("CPU freeze")
    def jump(self, position): print(f"CPU jump to {position}")
    def execute(self): print("CPU execute")


class Memory:
    def load(self, position, data): print(f"Memory load {data} at {position}")


class HardDrive:
    def read(self, lba, size): return f"Data from sector {lba}"


class ComputerFacade:
    """Simplified interface to computer subsystems."""
    
    def __init__(self):
        self._cpu = CPU()
        self._memory = Memory()
        self._hard_drive = HardDrive()
    
    def start(self):
        self._cpu.freeze()
        data = self._hard_drive.read(0, 1024)
        self._memory.load(0, data)
        self._cpu.jump(0)
        self._cpu.execute()


# Usage - Client doesn't need to know subsystem details
computer = ComputerFacade()
computer.start()
```

---

### Flyweight

**Intent:** Share common state between multiple objects to save memory.

```python
class TreeType:
    """Intrinsic state (shared)."""
    
    def __init__(self, name: str, color: str, texture: str):
        self.name = name
        self.color = color
        self.texture = texture
    
    def draw(self, x: int, y: int) -> str:
        return f"Drawing {self.name} tree at ({x}, {y})"


class TreeFactory:
    _tree_types: dict[str, TreeType] = {}
    
    @classmethod
    def get_tree_type(cls, name: str, color: str, texture: str) -> TreeType:
        key = f"{name}_{color}_{texture}"
        if key not in cls._tree_types:
            cls._tree_types[key] = TreeType(name, color, texture)
        return cls._tree_types[key]


class Tree:
    """Extrinsic state (unique per instance)."""
    
    def __init__(self, x: int, y: int, tree_type: TreeType):
        self.x = x
        self.y = y
        self.tree_type = tree_type
    
    def draw(self) -> str:
        return self.tree_type.draw(self.x, self.y)


class Forest:
    def __init__(self):
        self.trees: list[Tree] = []
    
    def plant_tree(self, x: int, y: int, name: str, color: str, texture: str):
        tree_type = TreeFactory.get_tree_type(name, color, texture)
        tree = Tree(x, y, tree_type)
        self.trees.append(tree)


# Usage
forest = Forest()
for i in range(10000):
    forest.plant_tree(i % 100, i // 100, "Oak", "Green", "Rough")
# Only one TreeType object created, shared by 10000 trees!
```

---

### Proxy

**Intent:** Provide surrogate or placeholder for another object.

```python
class Image(ABC):
    @abstractmethod
    def display(self) -> str:
        pass


class RealImage(Image):
    def __init__(self, filename: str):
        self.filename = filename
        self._load_from_disk()
    
    def _load_from_disk(self):
        print(f"Loading {self.filename} from disk...")
    
    def display(self) -> str:
        return f"Displaying {self.filename}"


class ProxyImage(Image):
    """Lazy loading proxy."""
    
    def __init__(self, filename: str):
        self.filename = filename
        self._real_image = None
    
    def display(self) -> str:
        if self._real_image is None:
            self._real_image = RealImage(self.filename)
        return self._real_image.display()


# Usage
images = [ProxyImage(f"image{i}.jpg") for i in range(100)]
# No loading yet!

images[0].display()  # Loads only when needed
```

---

## 3️⃣ Behavioral Patterns

*Handle communication between objects.*

### Strategy

**Intent:** Define family of algorithms, encapsulate each, make them interchangeable.

```python
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount: float) -> str:
        pass


class CreditCardPayment(PaymentStrategy):
    def __init__(self, card_number: str):
        self.card_number = card_number
    
    def pay(self, amount: float) -> str:
        return f"Paid ${amount} with card {self.card_number[-4:]}"


class PayPalPayment(PaymentStrategy):
    def __init__(self, email: str):
        self.email = email
    
    def pay(self, amount: float) -> str:
        return f"Paid ${amount} via PayPal ({self.email})"


class CryptoPayment(PaymentStrategy):
    def __init__(self, wallet: str):
        self.wallet = wallet
    
    def pay(self, amount: float) -> str:
        return f"Paid ${amount} in crypto to {self.wallet[:8]}..."


class ShoppingCart:
    def __init__(self):
        self.items = []
        self._payment_strategy = None
    
    def set_payment_strategy(self, strategy: PaymentStrategy):
        self._payment_strategy = strategy
    
    def checkout(self) -> str:
        total = sum(item['price'] for item in self.items)
        return self._payment_strategy.pay(total)


# Usage
cart = ShoppingCart()
cart.items = [{'name': 'Book', 'price': 20}]

cart.set_payment_strategy(CreditCardPayment("1234567890123456"))
print(cart.checkout())

cart.set_payment_strategy(PayPalPayment("user@email.com"))
print(cart.checkout())
```

---

### Observer

**Intent:** Define one-to-many dependency so when one object changes state, all dependents are notified.

```python
class Observer(ABC):
    @abstractmethod
    def update(self, temperature: float, humidity: float) -> None:
        pass


class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer) -> None:
        pass
    
    @abstractmethod
    def detach(self, observer: Observer) -> None:
        pass
    
    @abstractmethod
    def notify(self) -> None:
        pass


class WeatherStation(Subject):
    def __init__(self):
        self._observers: list[Observer] = []
        self._temperature = 0.0
        self._humidity = 0.0
    
    def attach(self, observer: Observer) -> None:
        self._observers.append(observer)
    
    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)
    
    def notify(self) -> None:
        for observer in self._observers:
            observer.update(self._temperature, self._humidity)
    
    def set_measurements(self, temp: float, humidity: float) -> None:
        self._temperature = temp
        self._humidity = humidity
        self.notify()


class PhoneDisplay(Observer):
    def update(self, temperature: float, humidity: float) -> None:
        print(f"📱 Phone: {temperature}°C, {humidity}% humidity")


class WebDisplay(Observer):
    def update(self, temperature: float, humidity: float) -> None:
        print(f"🌐 Web: {temperature}°C, {humidity}% humidity")


# Usage
station = WeatherStation()
station.attach(PhoneDisplay())
station.attach(WebDisplay())

station.set_measurements(25.0, 60.0)
# Both displays update automatically
```

---

### Command

**Intent:** Encapsulate a request as an object, allowing parameterization and queuing.

```python
class Command(ABC):
    @abstractmethod
    def execute(self) -> None:
        pass
    
    @abstractmethod
    def undo(self) -> None:
        pass


class Light:
    def __init__(self, location: str):
        self.location = location
        self.is_on = False
    
    def on(self) -> None:
        self.is_on = True
        print(f"{self.location} light is ON")
    
    def off(self) -> None:
        self.is_on = False
        print(f"{self.location} light is OFF")


class LightOnCommand(Command):
    def __init__(self, light: Light):
        self._light = light
    
    def execute(self) -> None:
        self._light.on()
    
    def undo(self) -> None:
        self._light.off()


class LightOffCommand(Command):
    def __init__(self, light: Light):
        self._light = light
    
    def execute(self) -> None:
        self._light.off()
    
    def undo(self) -> None:
        self._light.on()


class RemoteControl:
    def __init__(self):
        self._history: list[Command] = []
    
    def execute(self, command: Command) -> None:
        command.execute()
        self._history.append(command)
    
    def undo(self) -> None:
        if self._history:
            command = self._history.pop()
            command.undo()


# Usage
living_room = Light("Living Room")
remote = RemoteControl()

remote.execute(LightOnCommand(living_room))
remote.execute(LightOffCommand(living_room))
remote.undo()  # Light turns back on
```

---

### State

**Intent:** Allow object to alter behavior when internal state changes.

```python
class State(ABC):
    @abstractmethod
    def insert_coin(self, machine: 'VendingMachine') -> str:
        pass
    
    @abstractmethod
    def dispense(self, machine: 'VendingMachine') -> str:
        pass


class NoCoinState(State):
    def insert_coin(self, machine: 'VendingMachine') -> str:
        machine.state = HasCoinState()
        return "Coin inserted"
    
    def dispense(self, machine: 'VendingMachine') -> str:
        return "Please insert a coin first"


class HasCoinState(State):
    def insert_coin(self, machine: 'VendingMachine') -> str:
        return "Coin already inserted"
    
    def dispense(self, machine: 'VendingMachine') -> str:
        machine.state = NoCoinState()
        return "Item dispensed!"


class VendingMachine:
    def __init__(self):
        self.state: State = NoCoinState()
    
    def insert_coin(self) -> str:
        return self.state.insert_coin(self)
    
    def dispense(self) -> str:
        return self.state.dispense(self)


# Usage
machine = VendingMachine()
print(machine.dispense())    # "Please insert a coin first"
print(machine.insert_coin()) # "Coin inserted"
print(machine.dispense())    # "Item dispensed!"
```

---

### Iterator

**Intent:** Provide a way to access elements of a collection sequentially without exposing underlying representation.

```python
from typing import Iterator, Any

class MyCollection:
    def __init__(self):
        self._items = []
    
    def add(self, item: Any) -> None:
        self._items.append(item)
    
    def __iter__(self) -> Iterator:
        return MyIterator(self._items)


class MyIterator:
    def __init__(self, items: list):
        self._items = items
        self._index = 0
    
    def __iter__(self) -> 'MyIterator':
        return self
    
    def __next__(self) -> Any:
        if self._index < len(self._items):
            item = self._items[self._index]
            self._index += 1
            return item
        raise StopIteration


# Usage
collection = MyCollection()
collection.add("A")
collection.add("B")
collection.add("C")

for item in collection:
    print(item)
```

---

## 📚 Pattern Selection Guide

| Problem | Pattern |
|---------|---------|
| Single instance needed | Singleton |
| Create objects without specifying class | Factory Method |
| Create families of related objects | Abstract Factory |
| Complex object construction | Builder |
| Clone existing objects | Prototype |
| Incompatible interfaces | Adapter |
| Separate abstraction from implementation | Bridge |
| Tree structures | Composite |
| Add responsibilities dynamically | Decorator |
| Simplify complex subsystems | Facade |
| Share objects to save memory | Flyweight |
| Control access to objects | Proxy |
| Interchangeable algorithms | Strategy |
| Notify multiple objects of changes | Observer |
| Encapsulate requests as objects | Command |
| Object behavior depends on state | State |
| Sequential access to collection | Iterator |

---

*Last Updated: 2024*
