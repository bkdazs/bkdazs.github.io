---
title: "Prototype Pattern"
topic: "Creational Design Patterns"
difficulty: "Medium"
tags: ["prototype", "clone", "creational", "design-pattern"]
status: "complete"
weight: 4
---

# Prototype Pattern

## 📚 Overview

**Intent:** Create new objects by copying an existing object (prototype) instead of creating from scratch.

**Use When:**
- Object creation is expensive (DB calls, network requests)
- System should be independent of how objects are created
- Objects have few variations but need many instances
- Need to avoid subclass explosion

---

## 1️⃣ Basic Prototype with copy Module

```python
import copy
from abc import ABC, abstractmethod
from typing import Dict, List, Any


class Prototype(ABC):
    """Abstract prototype interface"""
    
    @abstractmethod
    def clone(self) -> 'Prototype':
        pass


class Document(Prototype):
    def __init__(self, title: str, content: str, author: str):
        self.title = title
        self.content = content
        self.author = author
        self.metadata: Dict[str, Any] = {}
        self.sections: List[str] = []
        self.version = 1
    
    def clone(self) -> 'Document':
        """Deep clone the document"""
        cloned = copy.deepcopy(self)
        cloned.version = self.version + 1
        cloned.title = f"Copy of {self.title}"
        return cloned
    
    def shallow_clone(self) -> 'Document':
        """Shallow clone - nested objects shared"""
        return copy.copy(self)
    
    def __str__(self) -> str:
        return f"Document('{self.title}', v{self.version}, {len(self.sections)} sections)"


# Usage
original = Document("Design Patterns", "Content about patterns...", "GoF")
original.metadata = {"created": "2024-01-01", "tags": ["programming", "design"]}
original.sections = ["Introduction", "Creational", "Structural", "Behavioral"]

# Deep clone - independent copy
clone1 = original.clone()
clone1.sections.append("Conclusion")  # Doesn't affect original

# Shallow clone - shares nested objects
clone2 = original.shallow_clone()
clone2.metadata["tags"].append("architecture")  # Affects original!

print(f"Original sections: {original.sections}")
print(f"Clone1 sections: {clone1.sections}")
print(f"Original tags: {original.metadata['tags']}")
```

---

## 2️⃣ Prototype Registry

```python
from typing import Dict, TypeVar, Generic
import copy

T = TypeVar('T')


class PrototypeRegistry(Generic[T]):
    """Registry for managing prototypes"""
    
    def __init__(self):
        self._prototypes: Dict[str, T] = {}
    
    def register(self, name: str, prototype: T) -> None:
        """Register a prototype"""
        self._prototypes[name] = prototype
    
    def unregister(self, name: str) -> None:
        """Remove a prototype"""
        self._prototypes.pop(name, None)
    
    def clone(self, name: str) -> T:
        """Clone a registered prototype"""
        prototype = self._prototypes.get(name)
        if prototype is None:
            raise KeyError(f"Prototype '{name}' not found")
        return copy.deepcopy(prototype)
    
    def list_prototypes(self) -> list:
        """List all registered prototype names"""
        return list(self._prototypes.keys())


# Example usage with shapes
class Shape:
    def __init__(self, x: int = 0, y: int = 0, color: str = "black"):
        self.x = x
        self.y = y
        self.color = color
    
    def move(self, dx: int, dy: int) -> None:
        self.x += dx
        self.y += dy


class Circle(Shape):
    def __init__(self, radius: int = 10, **kwargs):
        super().__init__(**kwargs)
        self.radius = radius
    
    def __str__(self) -> str:
        return f"Circle(r={self.radius}, pos=({self.x},{self.y}), color={self.color})"


class Rectangle(Shape):
    def __init__(self, width: int = 10, height: int = 10, **kwargs):
        super().__init__(**kwargs)
        self.width = width
        self.height = height
    
    def __str__(self) -> str:
        return f"Rectangle({self.width}x{self.height}, pos=({self.x},{self.y}), color={self.color})"


# Setup registry
registry = PrototypeRegistry[Shape]()

# Register prototypes
registry.register("red_circle", Circle(radius=50, color="red"))
registry.register("blue_rectangle", Rectangle(width=100, height=50, color="blue"))
registry.register("small_circle", Circle(radius=10, color="green"))

# Clone and customize
shape1 = registry.clone("red_circle")
shape1.move(100, 100)

shape2 = registry.clone("red_circle")
shape2.radius = 75

shape3 = registry.clone("blue_rectangle")
shape3.color = "purple"

print(shape1)
print(shape2)
print(shape3)
```

---

## 3️⃣ Prototype with Custom Clone Logic

```python
from __future__ import annotations
import copy
from typing import Optional, List
from datetime import datetime


class Employee:
    """Employee with custom cloning behavior"""
    
    def __init__(self, name: str, department: str, salary: float):
        self.name = name
        self.department = department
        self.salary = salary
        self.hire_date = datetime.now()
        self.manager: Optional[Employee] = None
        self.subordinates: List[Employee] = []
        self._id = id(self)  # Unique identifier
    
    def __copy__(self) -> Employee:
        """Shallow copy - called by copy.copy()"""
        new = Employee(self.name, self.department, self.salary)
        new.manager = self.manager  # Same reference
        new.subordinates = self.subordinates  # Same list reference
        return new
    
    def __deepcopy__(self, memo: dict) -> Employee:
        """Deep copy - called by copy.deepcopy()"""
        # Check memo to handle circular references
        if id(self) in memo:
            return memo[id(self)]
        
        new = Employee(
            copy.deepcopy(self.name, memo),
            copy.deepcopy(self.department, memo),
            copy.deepcopy(self.salary, memo)
        )
        
        # Add to memo before copying references (handle cycles)
        memo[id(self)] = new
        
        # Deep copy relationships
        new.manager = copy.deepcopy(self.manager, memo)
        new.subordinates = copy.deepcopy(self.subordinates, memo)
        
        # Reset some fields for the clone
        new.hire_date = datetime.now()  # New hire date
        
        return new
    
    def clone_for_transfer(self, new_department: str) -> Employee:
        """Custom clone for department transfer"""
        new_emp = copy.deepcopy(self)
        new_emp.department = new_department
        new_emp.manager = None
        new_emp.subordinates = []
        return new_emp
    
    def __str__(self) -> str:
        return f"Employee({self.name}, {self.department}, ${self.salary})"


# Usage with circular reference handling
manager = Employee("Alice", "Engineering", 150000)
employee1 = Employee("Bob", "Engineering", 100000)
employee2 = Employee("Charlie", "Engineering", 95000)

employee1.manager = manager
employee2.manager = manager
manager.subordinates = [employee1, employee2]

# Deep copy handles the circular reference
team_copy = copy.deepcopy(manager)
print(f"Original manager: {manager}")
print(f"Cloned manager: {team_copy}")
print(f"Cloned has {len(team_copy.subordinates)} subordinates")

# Custom clone for transfer
transferred = employee1.clone_for_transfer("Sales")
print(f"Transferred: {transferred}")
```

---

## 4️⃣ Prototype with Serialization

```python
import json
import pickle
from abc import ABC, abstractmethod
from dataclasses import dataclass, field, asdict
from typing import Dict, Any, List


@dataclass
class GameCharacter:
    """Game character with multiple clone methods"""
    name: str
    health: int
    level: int
    position: Dict[str, int] = field(default_factory=lambda: {"x": 0, "y": 0})
    inventory: List[str] = field(default_factory=list)
    stats: Dict[str, int] = field(default_factory=lambda: {
        "strength": 10,
        "agility": 10,
        "intelligence": 10
    })
    
    def clone_pickle(self) -> 'GameCharacter':
        """Clone using pickle serialization"""
        return pickle.loads(pickle.dumps(self))
    
    def clone_json(self) -> 'GameCharacter':
        """Clone using JSON serialization"""
        data = asdict(self)
        return GameCharacter(**data)
    
    def clone_as_enemy(self) -> 'GameCharacter':
        """Clone as enemy (modified clone)"""
        enemy = self.clone_pickle()
        enemy.name = f"Evil {self.name}"
        enemy.stats = {k: int(v * 1.5) for k, v in self.stats.items()}
        return enemy
    
    def spawn_at(self, x: int, y: int) -> 'GameCharacter':
        """Clone and place at position"""
        clone = self.clone_pickle()
        clone.position = {"x": x, "y": y}
        return clone


# Character templates
warrior_template = GameCharacter(
    name="Warrior",
    health=100,
    level=1,
    stats={"strength": 15, "agility": 10, "intelligence": 5},
    inventory=["sword", "shield"]
)

mage_template = GameCharacter(
    name="Mage",
    health=70,
    level=1,
    stats={"strength": 5, "agility": 10, "intelligence": 15},
    inventory=["staff", "spellbook"]
)

# Spawn characters
player = warrior_template.clone_json()
player.name = "Hero"

enemies = [
    warrior_template.spawn_at(100, 200),
    warrior_template.spawn_at(150, 200),
    mage_template.clone_as_enemy().spawn_at(200, 250)
]

print(f"Player: {player}")
for i, enemy in enumerate(enemies):
    print(f"Enemy {i+1}: {enemy}")
```

---

## 5️⃣ Real-World: Configuration Prototype

```python
from dataclasses import dataclass, field
from typing import Dict, List, Optional, Any
import copy


@dataclass
class DatabaseConfig:
    host: str = "localhost"
    port: int = 5432
    database: str = "default"
    username: str = "user"
    password: str = "password"
    pool_size: int = 10
    timeout: int = 30


@dataclass  
class CacheConfig:
    host: str = "localhost"
    port: int = 6379
    ttl: int = 3600
    max_connections: int = 100


@dataclass
class ApplicationConfig:
    """Complex configuration with nested objects"""
    name: str
    environment: str
    debug: bool = False
    database: DatabaseConfig = field(default_factory=DatabaseConfig)
    cache: CacheConfig = field(default_factory=CacheConfig)
    features: Dict[str, bool] = field(default_factory=dict)
    allowed_hosts: List[str] = field(default_factory=list)
    extra: Dict[str, Any] = field(default_factory=dict)
    
    def clone(self) -> 'ApplicationConfig':
        """Deep clone configuration"""
        return copy.deepcopy(self)
    
    def for_environment(self, env: str) -> 'ApplicationConfig':
        """Clone and customize for environment"""
        config = self.clone()
        config.environment = env
        
        if env == "production":
            config.debug = False
            config.database.pool_size = 50
            config.cache.max_connections = 500
        elif env == "development":
            config.debug = True
            config.database.host = "localhost"
            config.cache.host = "localhost"
        elif env == "testing":
            config.debug = True
            config.database.database = f"test_{config.database.database}"
            
        return config


# Base configuration prototype
base_config = ApplicationConfig(
    name="MyApp",
    environment="base",
    database=DatabaseConfig(
        host="db.example.com",
        database="myapp",
        pool_size=20
    ),
    cache=CacheConfig(
        host="cache.example.com",
        ttl=7200
    ),
    features={
        "new_ui": True,
        "beta_features": False,
        "analytics": True
    },
    allowed_hosts=["*.example.com"]
)

# Create environment-specific configs
dev_config = base_config.for_environment("development")
prod_config = base_config.for_environment("production")
test_config = base_config.for_environment("testing")

print(f"Dev DB: {dev_config.database.host}, pool: {dev_config.database.pool_size}")
print(f"Prod DB: {prod_config.database.host}, pool: {prod_config.database.pool_size}")
print(f"Test DB: {test_config.database.database}")
```

---

## 🔄 Shallow vs Deep Copy

```python
import copy

class Container:
    def __init__(self, items: list):
        self.items = items
        self.metadata = {"count": len(items)}

original = Container([1, 2, 3])

# Shallow copy
shallow = copy.copy(original)
shallow.items.append(4)  # Modifies original.items too!
print(f"Original after shallow mod: {original.items}")  # [1, 2, 3, 4]

# Deep copy  
original2 = Container([1, 2, 3])
deep = copy.deepcopy(original2)
deep.items.append(4)  # Doesn't affect original
print(f"Original after deep mod: {original2.items}")  # [1, 2, 3]
```

| Aspect | Shallow Copy | Deep Copy |
|--------|--------------|-----------|
| Nested objects | Shared references | New copies |
| Performance | Faster | Slower |
| Memory | Less | More |
| Use case | Flat objects | Nested/complex objects |

---

## ❓ Interview Questions

1. **Q: When would you use Prototype over Factory?**
   - When object creation is expensive
   - When you need slight variations of existing objects
   - When avoiding complex class hierarchies

2. **Q: How to handle circular references in deep copy?**
   - Use `memo` dictionary in `__deepcopy__`
   - Store copied objects by id before recursing

3. **Q: Prototype vs Copy Constructor?**
   - Prototype: External cloning, more flexible
   - Copy Constructor: Internal, language-specific

---

## 📝 Related Problems

| Problem | Platform | Key Concept |
|---------|----------|-------------|
| Clone Graph | LeetCode #133 | Deep copy with cycles |
| Copy List with Random Pointer | LeetCode #138 | Clone with extra pointers |
| Clone N-ary Tree | LeetCode #1490 | Tree cloning |

---

*Last Updated: 2024*
