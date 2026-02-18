---
title: "More Behavioral Patterns"
topic: "Behavioral Design Patterns"
difficulty: "Medium"
tags: ["template-method", "iterator", "chain-of-responsibility", "mediator", "memento", "visitor", "interpreter", "behavioral", "design-pattern"]
status: "complete"
weight: 3
---

# More Behavioral Patterns

---

## 1️⃣ Template Method Pattern

**Intent:** Define the skeleton of an algorithm, deferring some steps to subclasses.

```python
from abc import ABC, abstractmethod


class DataMiner(ABC):
    """Template Method Pattern - defines algorithm skeleton"""
    
    def mine(self, path: str) -> dict:
        """Template method - final, can't be overridden"""
        raw_data = self.extract_data(path)
        parsed_data = self.parse_data(raw_data)
        analysis = self.analyze_data(parsed_data)
        report = self.generate_report(analysis)
        self.send_report(report)
        return report
    
    @abstractmethod
    def extract_data(self, path: str) -> str:
        """Abstract - must be implemented by subclasses"""
        pass
    
    @abstractmethod
    def parse_data(self, raw_data: str) -> list:
        """Abstract - must be implemented by subclasses"""
        pass
    
    def analyze_data(self, data: list) -> dict:
        """Hook - optional override, has default implementation"""
        return {"count": len(data), "data": data}
    
    def generate_report(self, analysis: dict) -> dict:
        """Hook - can be overridden"""
        return {"type": "generic", "analysis": analysis}
    
    def send_report(self, report: dict) -> None:
        """Hook - default does nothing"""
        pass


class CSVDataMiner(DataMiner):
    def extract_data(self, path: str) -> str:
        return "name,age,city\nJohn,30,NYC\nJane,25,LA"
    
    def parse_data(self, raw_data: str) -> list:
        lines = raw_data.strip().split('\n')
        headers = lines[0].split(',')
        return [dict(zip(headers, line.split(','))) for line in lines[1:]]


class JSONDataMiner(DataMiner):
    def extract_data(self, path: str) -> str:
        return '[{"name": "John"}, {"name": "Jane"}]'
    
    def parse_data(self, raw_data: str) -> list:
        import json
        return json.loads(raw_data)
    
    def generate_report(self, analysis: dict) -> dict:
        return {"type": "json", "format": "pretty", "analysis": analysis}


# Usage
csv_miner = CSVDataMiner()
result = csv_miner.mine("data.csv")
print(result)
```

---

## 2️⃣ Iterator Pattern

**Intent:** Provide a way to access elements of a collection sequentially without exposing underlying representation.

```python
from typing import TypeVar, Generic, Iterator, List, Optional
from collections.abc import Iterable

T = TypeVar('T')


class TreeNode(Generic[T]):
    def __init__(self, value: T):
        self.value = value
        self.left: Optional['TreeNode[T]'] = None
        self.right: Optional['TreeNode[T]'] = None


class BinaryTree(Generic[T]):
    def __init__(self, root: TreeNode[T] = None):
        self.root = root
    
    def inorder(self) -> Iterator[T]:
        """Inorder traversal iterator"""
        def _inorder(node: TreeNode[T]) -> Iterator[T]:
            if node:
                yield from _inorder(node.left)
                yield node.value
                yield from _inorder(node.right)
        return _inorder(self.root)
    
    def preorder(self) -> Iterator[T]:
        """Preorder traversal iterator"""
        def _preorder(node: TreeNode[T]) -> Iterator[T]:
            if node:
                yield node.value
                yield from _preorder(node.left)
                yield from _preorder(node.right)
        return _preorder(self.root)
    
    def postorder(self) -> Iterator[T]:
        """Postorder traversal iterator"""
        def _postorder(node: TreeNode[T]) -> Iterator[T]:
            if node:
                yield from _postorder(node.left)
                yield from _postorder(node.right)
                yield node.value
        return _postorder(self.root)
    
    def levelorder(self) -> Iterator[T]:
        """Level order (BFS) iterator"""
        if not self.root:
            return
        
        from collections import deque
        queue = deque([self.root])
        
        while queue:
            node = queue.popleft()
            yield node.value
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    
    def __iter__(self) -> Iterator[T]:
        """Default iteration is inorder"""
        return self.inorder()


# Build tree
#       1
#      / \
#     2   3
#    / \
#   4   5

root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)

tree = BinaryTree(root)

print("Inorder:", list(tree.inorder()))      # [4, 2, 5, 1, 3]
print("Preorder:", list(tree.preorder()))    # [1, 2, 4, 5, 3]
print("Postorder:", list(tree.postorder()))  # [4, 5, 2, 3, 1]
print("Level order:", list(tree.levelorder()))  # [1, 2, 3, 4, 5]
```

---

## 3️⃣ Chain of Responsibility

**Intent:** Pass request along a chain of handlers. Each handler decides whether to process or pass.

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import Optional
from enum import Enum


class LogLevel(Enum):
    DEBUG = 1
    INFO = 2
    WARNING = 3
    ERROR = 4
    CRITICAL = 5


@dataclass
class LogRecord:
    level: LogLevel
    message: str
    source: str


class LogHandler(ABC):
    def __init__(self, level: LogLevel):
        self.level = level
        self._next: Optional['LogHandler'] = None
    
    def set_next(self, handler: 'LogHandler') -> 'LogHandler':
        self._next = handler
        return handler
    
    def handle(self, record: LogRecord) -> None:
        if record.level.value >= self.level.value:
            self.write(record)
        
        if self._next:
            self._next.handle(record)
    
    @abstractmethod
    def write(self, record: LogRecord) -> None:
        pass


class ConsoleHandler(LogHandler):
    def write(self, record: LogRecord) -> None:
        print(f"[CONSOLE] {record.level.name}: {record.message}")


class FileHandler(LogHandler):
    def __init__(self, level: LogLevel, filename: str):
        super().__init__(level)
        self.filename = filename
    
    def write(self, record: LogRecord) -> None:
        print(f"[FILE:{self.filename}] {record.level.name}: {record.message}")


class EmailHandler(LogHandler):
    def __init__(self, level: LogLevel, email: str):
        super().__init__(level)
        self.email = email
    
    def write(self, record: LogRecord) -> None:
        print(f"[EMAIL:{self.email}] ALERT - {record.level.name}: {record.message}")


# Build chain
console = ConsoleHandler(LogLevel.DEBUG)
file_handler = FileHandler(LogLevel.WARNING, "app.log")
email = EmailHandler(LogLevel.ERROR, "admin@example.com")

console.set_next(file_handler).set_next(email)

# Process logs
logs = [
    LogRecord(LogLevel.DEBUG, "Debug message", "app"),
    LogRecord(LogLevel.INFO, "Info message", "app"),
    LogRecord(LogLevel.WARNING, "Warning!", "db"),
    LogRecord(LogLevel.ERROR, "Error occurred!", "api"),
    LogRecord(LogLevel.CRITICAL, "System crash!", "core"),
]

for log in logs:
    print(f"\n--- Processing: {log.level.name} ---")
    console.handle(log)
```

---

## 4️⃣ Mediator Pattern

**Intent:** Define an object that encapsulates how objects interact. Promotes loose coupling.

```python
from abc import ABC, abstractmethod
from typing import Dict, List


class ChatMediator(ABC):
    @abstractmethod
    def send_message(self, message: str, sender: 'User') -> None:
        pass
    
    @abstractmethod
    def add_user(self, user: 'User') -> None:
        pass


class User(ABC):
    def __init__(self, name: str, mediator: ChatMediator):
        self.name = name
        self.mediator = mediator
    
    @abstractmethod
    def send(self, message: str) -> None:
        pass
    
    @abstractmethod
    def receive(self, message: str, sender: str) -> None:
        pass


class ChatRoom(ChatMediator):
    def __init__(self):
        self._users: Dict[str, User] = {}
    
    def add_user(self, user: User) -> None:
        self._users[user.name] = user
        self.broadcast(f"{user.name} joined the chat", "System")
    
    def send_message(self, message: str, sender: User) -> None:
        for name, user in self._users.items():
            if user != sender:
                user.receive(message, sender.name)
    
    def broadcast(self, message: str, sender_name: str) -> None:
        for user in self._users.values():
            user.receive(message, sender_name)
    
    def send_private(self, message: str, sender: User, recipient: str) -> None:
        if recipient in self._users:
            self._users[recipient].receive(f"[Private] {message}", sender.name)


class ChatUser(User):
    def send(self, message: str) -> None:
        print(f"{self.name} sends: {message}")
        self.mediator.send_message(message, self)
    
    def send_private(self, message: str, to: str) -> None:
        print(f"{self.name} sends private to {to}: {message}")
        if isinstance(self.mediator, ChatRoom):
            self.mediator.send_private(message, self, to)
    
    def receive(self, message: str, sender: str) -> None:
        print(f"{self.name} received from {sender}: {message}")


# Usage
chatroom = ChatRoom()

alice = ChatUser("Alice", chatroom)
bob = ChatUser("Bob", chatroom)
charlie = ChatUser("Charlie", chatroom)

chatroom.add_user(alice)
chatroom.add_user(bob)
chatroom.add_user(charlie)

print("\n--- Public Message ---")
alice.send("Hello everyone!")

print("\n--- Private Message ---")
bob.send_private("Hey Alice!", "Alice")
```

---

## 5️⃣ Memento Pattern

**Intent:** Capture and restore an object's internal state without violating encapsulation.

```python
from dataclasses import dataclass
from typing import List, Optional
from datetime import datetime


@dataclass
class EditorMemento:
    """Memento - stores editor state"""
    content: str
    cursor_position: int
    timestamp: datetime
    
    def get_description(self) -> str:
        preview = self.content[:30] + "..." if len(self.content) > 30 else self.content
        return f"{self.timestamp.strftime('%H:%M:%S')} - '{preview}'"


class TextEditor:
    """Originator - creates and restores from mementos"""
    
    def __init__(self):
        self._content = ""
        self._cursor = 0
    
    def type(self, text: str) -> None:
        self._content = self._content[:self._cursor] + text + self._content[self._cursor:]
        self._cursor += len(text)
    
    def delete(self, count: int = 1) -> None:
        if self._cursor > 0:
            start = max(0, self._cursor - count)
            self._content = self._content[:start] + self._content[self._cursor:]
            self._cursor = start
    
    def save(self) -> EditorMemento:
        return EditorMemento(self._content, self._cursor, datetime.now())
    
    def restore(self, memento: EditorMemento) -> None:
        self._content = memento.content
        self._cursor = memento.cursor_position
    
    def __str__(self) -> str:
        return f"Content: '{self._content}' (cursor at {self._cursor})"


class History:
    """Caretaker - manages mementos"""
    
    def __init__(self, editor: TextEditor):
        self._editor = editor
        self._history: List[EditorMemento] = []
        self._current = -1
    
    def backup(self) -> None:
        # Remove any "future" states when making new backup
        self._history = self._history[:self._current + 1]
        self._history.append(self._editor.save())
        self._current = len(self._history) - 1
    
    def undo(self) -> bool:
        if self._current <= 0:
            return False
        self._current -= 1
        self._editor.restore(self._history[self._current])
        return True
    
    def redo(self) -> bool:
        if self._current >= len(self._history) - 1:
            return False
        self._current += 1
        self._editor.restore(self._history[self._current])
        return True
    
    def show_history(self) -> None:
        print("\n--- History ---")
        for i, memento in enumerate(self._history):
            marker = " <-- current" if i == self._current else ""
            print(f"  [{i}] {memento.get_description()}{marker}")


# Usage
editor = TextEditor()
history = History(editor)

history.backup()  # Initial state
editor.type("Hello")
history.backup()

editor.type(" World")
history.backup()

editor.type("!")
history.backup()

print(editor)
history.show_history()

print("\n--- Undo twice ---")
history.undo()
history.undo()
print(editor)

print("\n--- Redo once ---")
history.redo()
print(editor)
```

---

## 6️⃣ Visitor Pattern

**Intent:** Represent an operation to be performed on elements of a structure. Add new operations without modifying the classes.

```python
from abc import ABC, abstractmethod
from typing import List


# Element interface
class Shape(ABC):
    @abstractmethod
    def accept(self, visitor: 'ShapeVisitor') -> None:
        pass


# Concrete elements
class Circle(Shape):
    def __init__(self, radius: float):
        self.radius = radius
    
    def accept(self, visitor: 'ShapeVisitor') -> None:
        visitor.visit_circle(self)


class Rectangle(Shape):
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    
    def accept(self, visitor: 'ShapeVisitor') -> None:
        visitor.visit_rectangle(self)


class Triangle(Shape):
    def __init__(self, base: float, height: float):
        self.base = base
        self.height = height
    
    def accept(self, visitor: 'ShapeVisitor') -> None:
        visitor.visit_triangle(self)


# Visitor interface
class ShapeVisitor(ABC):
    @abstractmethod
    def visit_circle(self, circle: Circle) -> None:
        pass
    
    @abstractmethod
    def visit_rectangle(self, rect: Rectangle) -> None:
        pass
    
    @abstractmethod
    def visit_triangle(self, tri: Triangle) -> None:
        pass


# Concrete visitors
class AreaCalculator(ShapeVisitor):
    def __init__(self):
        self.total = 0
    
    def visit_circle(self, circle: Circle) -> None:
        import math
        area = math.pi * circle.radius ** 2
        self.total += area
        print(f"Circle area: {area:.2f}")
    
    def visit_rectangle(self, rect: Rectangle) -> None:
        area = rect.width * rect.height
        self.total += area
        print(f"Rectangle area: {area:.2f}")
    
    def visit_triangle(self, tri: Triangle) -> None:
        area = 0.5 * tri.base * tri.height
        self.total += area
        print(f"Triangle area: {area:.2f}")


class DrawingExporter(ShapeVisitor):
    def __init__(self):
        self.svg_parts: List[str] = []
    
    def visit_circle(self, circle: Circle) -> None:
        self.svg_parts.append(f'<circle r="{circle.radius}"/>')
    
    def visit_rectangle(self, rect: Rectangle) -> None:
        self.svg_parts.append(f'<rect width="{rect.width}" height="{rect.height}"/>')
    
    def visit_triangle(self, tri: Triangle) -> None:
        self.svg_parts.append(f'<polygon points="0,{tri.height} {tri.base/2},0 {tri.base},{tri.height}"/>')
    
    def get_svg(self) -> str:
        return "<svg>\n" + "\n".join(f"  {p}" for p in self.svg_parts) + "\n</svg>"


# Usage
shapes = [
    Circle(5),
    Rectangle(10, 20),
    Triangle(6, 8),
    Circle(3)
]

# Calculate areas
area_calc = AreaCalculator()
for shape in shapes:
    shape.accept(area_calc)
print(f"Total area: {area_calc.total:.2f}")

# Export to SVG
print("\n--- SVG Export ---")
exporter = DrawingExporter()
for shape in shapes:
    shape.accept(exporter)
print(exporter.get_svg())
```

---

## 🔄 Pattern Selection Guide

| Need | Pattern |
|------|---------|
| Algorithm skeleton with customizable steps | Template Method |
| Traverse collection without exposing internals | Iterator |
| Pass request through handlers | Chain of Responsibility |
| Decouple many-to-many communication | Mediator |
| Save/restore object state | Memento |
| Add operations without modifying classes | Visitor |

---

*Last Updated: 2024*
