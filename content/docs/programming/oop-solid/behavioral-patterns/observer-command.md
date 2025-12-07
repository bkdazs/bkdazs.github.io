---
title: "Observer & Command Patterns"
topic: "Behavioral Design Patterns"
difficulty: "Medium"
tags: ["observer", "command", "behavioral", "design-pattern", "pub-sub"]
status: "complete"
weight: 2
---

# Observer & Command Patterns

---

## 1️⃣ Observer Pattern

**Intent:** Define a one-to-many dependency so that when one object changes state, all dependents are notified.

**Also Known As:** Publish-Subscribe, Event-Subscriber

```python
from abc import ABC, abstractmethod
from typing import List, Dict, Any
from dataclasses import dataclass, field
from enum import Enum


# Observer interface
class Observer(ABC):
    @abstractmethod
    def update(self, event: str, data: Any) -> None:
        pass


# Subject (Observable)
class Subject:
    def __init__(self):
        self._observers: Dict[str, List[Observer]] = {}
    
    def attach(self, event: str, observer: Observer) -> None:
        if event not in self._observers:
            self._observers[event] = []
        self._observers[event].append(observer)
    
    def detach(self, event: str, observer: Observer) -> None:
        if event in self._observers:
            self._observers[event].remove(observer)
    
    def notify(self, event: str, data: Any = None) -> None:
        for observer in self._observers.get(event, []):
            observer.update(event, data)


# Concrete Subject
class StockMarket(Subject):
    def __init__(self):
        super().__init__()
        self._prices: Dict[str, float] = {}
    
    def set_price(self, symbol: str, price: float) -> None:
        old_price = self._prices.get(symbol, 0)
        self._prices[symbol] = price
        
        self.notify("price_change", {
            "symbol": symbol,
            "old_price": old_price,
            "new_price": price,
            "change": price - old_price
        })
        
        # Notify on significant changes
        if old_price > 0 and abs(price - old_price) / old_price > 0.05:
            self.notify("significant_change", {
                "symbol": symbol,
                "change_percent": (price - old_price) / old_price * 100
            })
    
    def get_price(self, symbol: str) -> float:
        return self._prices.get(symbol, 0)


# Concrete Observers
class StockDisplay(Observer):
    def __init__(self, name: str):
        self.name = name
    
    def update(self, event: str, data: Any) -> None:
        if event == "price_change":
            symbol = data["symbol"]
            price = data["new_price"]
            change = data["change"]
            sign = "+" if change >= 0 else ""
            print(f"[{self.name}] {symbol}: ${price:.2f} ({sign}{change:.2f})")


class AlertSystem(Observer):
    def __init__(self, threshold: float = 5.0):
        self.threshold = threshold
    
    def update(self, event: str, data: Any) -> None:
        if event == "significant_change":
            symbol = data["symbol"]
            change = data["change_percent"]
            print(f"🚨 ALERT: {symbol} changed by {change:.1f}%!")


class Logger(Observer):
    def __init__(self):
        self.log: List[Dict] = []
    
    def update(self, event: str, data: Any) -> None:
        import datetime
        entry = {
            "timestamp": datetime.datetime.now().isoformat(),
            "event": event,
            "data": data
        }
        self.log.append(entry)
        print(f"📝 Logged: {event}")


# Usage
market = StockMarket()

display1 = StockDisplay("Main Display")
display2 = StockDisplay("Mobile App")
alerts = AlertSystem(threshold=5.0)
logger = Logger()

# Subscribe to events
market.attach("price_change", display1)
market.attach("price_change", display2)
market.attach("price_change", logger)
market.attach("significant_change", alerts)

# Price changes
market.set_price("AAPL", 150.00)
market.set_price("AAPL", 155.00)  # Small change
market.set_price("AAPL", 180.00)  # Big change - triggers alert
```

### Event System (Pythonic)

```python
from typing import Callable, Dict, List, Any
from functools import wraps


class EventEmitter:
    """Modern event system using callbacks"""
    
    def __init__(self):
        self._listeners: Dict[str, List[Callable]] = {}
    
    def on(self, event: str, callback: Callable) -> Callable:
        """Register event listener"""
        if event not in self._listeners:
            self._listeners[event] = []
        self._listeners[event].append(callback)
        return callback
    
    def off(self, event: str, callback: Callable) -> None:
        """Remove event listener"""
        if event in self._listeners:
            self._listeners[event].remove(callback)
    
    def emit(self, event: str, *args, **kwargs) -> None:
        """Emit event to all listeners"""
        for callback in self._listeners.get(event, []):
            callback(*args, **kwargs)
    
    def once(self, event: str, callback: Callable) -> Callable:
        """Register listener that fires only once"""
        @wraps(callback)
        def wrapper(*args, **kwargs):
            self.off(event, wrapper)
            return callback(*args, **kwargs)
        return self.on(event, wrapper)


# Decorator syntax for listeners
class UserService(EventEmitter):
    def create_user(self, name: str, email: str) -> dict:
        user = {"id": hash(email), "name": name, "email": email}
        self.emit("user_created", user)
        return user
    
    def delete_user(self, user_id: int) -> None:
        self.emit("user_deleted", user_id)


# Usage with decorators
user_service = UserService()

@user_service.on("user_created")
def send_welcome_email(user):
    print(f"📧 Sending welcome email to {user['email']}")

@user_service.on("user_created")
def log_user_creation(user):
    print(f"📝 User created: {user['name']}")

@user_service.on("user_deleted")
def cleanup_user_data(user_id):
    print(f"🗑️ Cleaning up data for user {user_id}")

# Create user triggers all listeners
user_service.create_user("John", "john@example.com")
```

---

## 2️⃣ Command Pattern

**Intent:** Encapsulate a request as an object, letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

```python
from abc import ABC, abstractmethod
from typing import List, Optional
from dataclasses import dataclass


# Command interface
class Command(ABC):
    @abstractmethod
    def execute(self) -> None:
        pass
    
    @abstractmethod
    def undo(self) -> None:
        pass


# Receiver
class TextEditor:
    def __init__(self):
        self.text = ""
        self.clipboard = ""
        self.cursor = 0
    
    def insert(self, text: str, position: int) -> None:
        self.text = self.text[:position] + text + self.text[position:]
        self.cursor = position + len(text)
    
    def delete(self, start: int, length: int) -> str:
        deleted = self.text[start:start + length]
        self.text = self.text[:start] + self.text[start + length:]
        self.cursor = start
        return deleted
    
    def select_all(self) -> str:
        return self.text
    
    def __str__(self) -> str:
        return f"Text: '{self.text}' | Cursor: {self.cursor}"


# Concrete Commands
class InsertCommand(Command):
    def __init__(self, editor: TextEditor, text: str, position: int = None):
        self.editor = editor
        self.text = text
        self.position = position if position is not None else editor.cursor
    
    def execute(self) -> None:
        self.editor.insert(self.text, self.position)
    
    def undo(self) -> None:
        self.editor.delete(self.position, len(self.text))


class DeleteCommand(Command):
    def __init__(self, editor: TextEditor, start: int, length: int):
        self.editor = editor
        self.start = start
        self.length = length
        self.deleted_text = ""
    
    def execute(self) -> None:
        self.deleted_text = self.editor.delete(self.start, self.length)
    
    def undo(self) -> None:
        self.editor.insert(self.deleted_text, self.start)


class CopyCommand(Command):
    def __init__(self, editor: TextEditor):
        self.editor = editor
        self.prev_clipboard = ""
    
    def execute(self) -> None:
        self.prev_clipboard = self.editor.clipboard
        self.editor.clipboard = self.editor.select_all()
    
    def undo(self) -> None:
        self.editor.clipboard = self.prev_clipboard


class PasteCommand(Command):
    def __init__(self, editor: TextEditor, position: int = None):
        self.editor = editor
        self.position = position if position is not None else editor.cursor
        self.pasted_length = 0
    
    def execute(self) -> None:
        text = self.editor.clipboard
        self.pasted_length = len(text)
        self.editor.insert(text, self.position)
    
    def undo(self) -> None:
        self.editor.delete(self.position, self.pasted_length)


# Invoker with history
class CommandHistory:
    def __init__(self):
        self._history: List[Command] = []
        self._redo_stack: List[Command] = []
    
    def execute(self, command: Command) -> None:
        command.execute()
        self._history.append(command)
        self._redo_stack.clear()  # Clear redo after new command
    
    def undo(self) -> bool:
        if not self._history:
            return False
        command = self._history.pop()
        command.undo()
        self._redo_stack.append(command)
        return True
    
    def redo(self) -> bool:
        if not self._redo_stack:
            return False
        command = self._redo_stack.pop()
        command.execute()
        self._history.append(command)
        return True


# Usage
editor = TextEditor()
history = CommandHistory()

# Execute commands
history.execute(InsertCommand(editor, "Hello "))
print(editor)

history.execute(InsertCommand(editor, "World"))
print(editor)

history.execute(DeleteCommand(editor, 5, 1))  # Delete space
print(editor)

# Undo
print("\n--- Undo ---")
history.undo()
print(editor)

history.undo()
print(editor)

# Redo
print("\n--- Redo ---")
history.redo()
print(editor)
```

### Macro Commands

```python
class MacroCommand(Command):
    """Composite command - executes multiple commands"""
    
    def __init__(self):
        self.commands: List[Command] = []
    
    def add(self, command: Command) -> 'MacroCommand':
        self.commands.append(command)
        return self
    
    def execute(self) -> None:
        for cmd in self.commands:
            cmd.execute()
    
    def undo(self) -> None:
        for cmd in reversed(self.commands):
            cmd.undo()


# Create macro
editor = TextEditor()
history = CommandHistory()

# Define a "Hello World" macro
hello_world_macro = MacroCommand()
hello_world_macro.add(InsertCommand(editor, "Hello"))
hello_world_macro.add(InsertCommand(editor, " "))
hello_world_macro.add(InsertCommand(editor, "World"))
hello_world_macro.add(InsertCommand(editor, "!"))

# Execute macro as single command
history.execute(hello_world_macro)
print(editor)  # "Hello World!"

# Undo entire macro
history.undo()
print(editor)  # ""
```

### Command Queue

```python
from collections import deque
from threading import Thread
import time


class CommandQueue:
    """Asynchronous command execution queue"""
    
    def __init__(self):
        self._queue = deque()
        self._running = False
    
    def add(self, command: Command) -> None:
        self._queue.append(command)
    
    def process(self) -> None:
        self._running = True
        while self._running and self._queue:
            command = self._queue.popleft()
            try:
                command.execute()
                print(f"Executed: {command.__class__.__name__}")
            except Exception as e:
                print(f"Failed: {command.__class__.__name__} - {e}")
            time.sleep(0.1)  # Simulate processing time
    
    def stop(self) -> None:
        self._running = False
    
    def process_async(self) -> Thread:
        thread = Thread(target=self.process)
        thread.start()
        return thread
```

---

## 🔄 Observer vs Command

| Aspect | Observer | Command |
|--------|----------|---------|
| **Direction** | One-to-many broadcast | One-to-one request |
| **Coupling** | Subject doesn't know observers | Command knows receiver |
| **History** | No built-in history | Often supports undo/redo |
| **Use case** | Events, notifications | Actions, transactions |

---

## ❓ Interview Questions

1. **Q: Observer pattern drawbacks?**
   - Memory leaks if observers not detached
   - Order of notification undefined
   - Cascade updates can be expensive

2. **Q: When to use Command pattern?**
   - Undo/redo functionality
   - Transaction logging
   - Queued/scheduled operations
   - Remote procedure calls

3. **Q: How to make Observer thread-safe?**
   - Use locks for observer list modifications
   - Copy list before iterating
   - Use thread-safe collections

---

## 📝 Related Problems

| Problem | Platform | Pattern |
|---------|----------|---------|
| Design Browser History | LeetCode #1472 | Command + Undo |
| Design Snake Game | LeetCode #353 | Command queue |
| Design Twitter | LeetCode #355 | Observer (feed) |

---

*Last Updated: 2024*
