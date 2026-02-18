---
title: "Strategy & State Patterns"
topic: "Behavioral Design Patterns"
difficulty: "Medium"
tags: ["strategy", "state", "behavioral", "design-pattern"]
status: "complete"
weight: 1
---

# Strategy & State Patterns

---

## 1️⃣ Strategy Pattern

**Intent:** Define a family of algorithms, encapsulate each one, and make them interchangeable.

```python
from abc import ABC, abstractmethod
from typing import List
from dataclasses import dataclass


# Strategy Interface
class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data: List[int]) -> List[int]:
        pass
    
    @property
    @abstractmethod
    def name(self) -> str:
        pass


# Concrete Strategies
class BubbleSort(SortStrategy):
    @property
    def name(self) -> str:
        return "Bubble Sort"
    
    def sort(self, data: List[int]) -> List[int]:
        arr = data.copy()
        n = len(arr)
        for i in range(n):
            for j in range(0, n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
        return arr


class QuickSort(SortStrategy):
    @property
    def name(self) -> str:
        return "Quick Sort"
    
    def sort(self, data: List[int]) -> List[int]:
        if len(data) <= 1:
            return data
        pivot = data[len(data) // 2]
        left = [x for x in data if x < pivot]
        middle = [x for x in data if x == pivot]
        right = [x for x in data if x > pivot]
        return self.sort(left) + middle + self.sort(right)


class MergeSort(SortStrategy):
    @property
    def name(self) -> str:
        return "Merge Sort"
    
    def sort(self, data: List[int]) -> List[int]:
        if len(data) <= 1:
            return data
        mid = len(data) // 2
        left = self.sort(data[:mid])
        right = self.sort(data[mid:])
        return self._merge(left, right)
    
    def _merge(self, left: List[int], right: List[int]) -> List[int]:
        result = []
        i = j = 0
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                result.append(left[i])
                i += 1
            else:
                result.append(right[j])
                j += 1
        result.extend(left[i:])
        result.extend(right[j:])
        return result


# Context
class Sorter:
    def __init__(self, strategy: SortStrategy = None):
        self._strategy = strategy or QuickSort()
    
    @property
    def strategy(self) -> SortStrategy:
        return self._strategy
    
    @strategy.setter
    def strategy(self, strategy: SortStrategy):
        self._strategy = strategy
    
    def sort(self, data: List[int]) -> List[int]:
        print(f"Sorting using {self._strategy.name}")
        return self._strategy.sort(data)


# Usage
data = [64, 34, 25, 12, 22, 11, 90]

sorter = Sorter()
print(sorter.sort(data))

sorter.strategy = BubbleSort()
print(sorter.sort(data))

sorter.strategy = MergeSort()
print(sorter.sort(data))
```

### Strategy with Functions (Pythonic)

```python
from typing import Callable, List


# Strategies as functions
def linear_search(arr: List[int], target: int) -> int:
    for i, val in enumerate(arr):
        if val == target:
            return i
    return -1


def binary_search(arr: List[int], target: int) -> int:
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1


def jump_search(arr: List[int], target: int) -> int:
    n = len(arr)
    step = int(n ** 0.5)
    prev = 0
    
    while arr[min(step, n) - 1] < target:
        prev = step
        step += int(n ** 0.5)
        if prev >= n:
            return -1
    
    for i in range(prev, min(step, n)):
        if arr[i] == target:
            return i
    return -1


# Context using callable
class Searcher:
    def __init__(self, strategy: Callable[[List[int], int], int] = None):
        self.strategy = strategy or linear_search
    
    def search(self, arr: List[int], target: int) -> int:
        return self.strategy(arr, target)


# Usage
arr = [1, 3, 5, 7, 9, 11, 13, 15]
searcher = Searcher(binary_search)
print(f"Found at: {searcher.search(arr, 7)}")
```

---

## 2️⃣ State Pattern

**Intent:** Allow an object to alter its behavior when its internal state changes.

```python
from abc import ABC, abstractmethod
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from typing import Optional


# State Interface
class DocumentState(ABC):
    @abstractmethod
    def publish(self, doc: 'Document') -> str:
        pass
    
    @abstractmethod
    def render(self) -> str:
        pass
    
    @property
    @abstractmethod
    def name(self) -> str:
        pass


# Concrete States
class DraftState(DocumentState):
    @property
    def name(self) -> str:
        return "Draft"
    
    def publish(self, doc: 'Document') -> str:
        doc.state = ModerationState()
        return "Document sent for moderation"
    
    def render(self) -> str:
        return "[DRAFT] Content not ready"


class ModerationState(DocumentState):
    @property
    def name(self) -> str:
        return "Moderation"
    
    def publish(self, doc: 'Document') -> str:
        if doc.current_user.is_admin:
            doc.state = PublishedState()
            return "Document published!"
        return "Only admins can publish from moderation"
    
    def render(self) -> str:
        return "[PENDING REVIEW] Awaiting moderation"


class PublishedState(DocumentState):
    @property
    def name(self) -> str:
        return "Published"
    
    def publish(self, doc: 'Document') -> str:
        return "Document already published"
    
    def render(self) -> str:
        return "[LIVE] Content visible to all"


# User class for permissions
@dataclass
class User:
    name: str
    is_admin: bool = False


# Context
class Document:
    def __init__(self, content: str, user: User):
        self.content = content
        self.current_user = user
        self.state: DocumentState = DraftState()
    
    def publish(self) -> str:
        return self.state.publish(self)
    
    def render(self) -> str:
        return f"{self.state.render()}\nContent: {self.content}"
    
    @property
    def status(self) -> str:
        return self.state.name


# Usage
admin = User("Admin", is_admin=True)
regular = User("Regular", is_admin=False)

doc = Document("Hello World!", regular)
print(f"Status: {doc.status}")
print(doc.render())

print("\n" + doc.publish())  # Goes to moderation
print(f"Status: {doc.status}")

print("\n" + doc.publish())  # Regular user can't publish
doc.current_user = admin
print(doc.publish())  # Admin publishes
print(f"Status: {doc.status}")
```

### Vending Machine Example

```python
from abc import ABC, abstractmethod


class VendingMachineState(ABC):
    @abstractmethod
    def insert_money(self, machine: 'VendingMachine', amount: float) -> str:
        pass
    
    @abstractmethod
    def select_product(self, machine: 'VendingMachine', product: str) -> str:
        pass
    
    @abstractmethod
    def dispense(self, machine: 'VendingMachine') -> str:
        pass
    
    @abstractmethod
    def cancel(self, machine: 'VendingMachine') -> str:
        pass


class IdleState(VendingMachineState):
    def insert_money(self, machine: 'VendingMachine', amount: float) -> str:
        machine.balance = amount
        machine.state = HasMoneyState()
        return f"Inserted ${amount:.2f}"
    
    def select_product(self, machine: 'VendingMachine', product: str) -> str:
        return "Please insert money first"
    
    def dispense(self, machine: 'VendingMachine') -> str:
        return "Please insert money and select product"
    
    def cancel(self, machine: 'VendingMachine') -> str:
        return "Nothing to cancel"


class HasMoneyState(VendingMachineState):
    def insert_money(self, machine: 'VendingMachine', amount: float) -> str:
        machine.balance += amount
        return f"Added ${amount:.2f}. Total: ${machine.balance:.2f}"
    
    def select_product(self, machine: 'VendingMachine', product: str) -> str:
        price = machine.products.get(product)
        if not price:
            return f"Product {product} not available"
        if machine.balance < price:
            return f"Insufficient funds. Need ${price - machine.balance:.2f} more"
        
        machine.selected_product = product
        machine.state = DispensingState()
        return f"Selected {product}"
    
    def dispense(self, machine: 'VendingMachine') -> str:
        return "Please select a product first"
    
    def cancel(self, machine: 'VendingMachine') -> str:
        refund = machine.balance
        machine.balance = 0
        machine.state = IdleState()
        return f"Refunded ${refund:.2f}"


class DispensingState(VendingMachineState):
    def insert_money(self, machine: 'VendingMachine', amount: float) -> str:
        return "Please wait, dispensing product"
    
    def select_product(self, machine: 'VendingMachine', product: str) -> str:
        return "Please wait, dispensing product"
    
    def dispense(self, machine: 'VendingMachine') -> str:
        product = machine.selected_product
        price = machine.products[product]
        change = machine.balance - price
        
        machine.balance = 0
        machine.selected_product = None
        machine.state = IdleState()
        
        result = f"Dispensing {product}!"
        if change > 0:
            result += f" Change: ${change:.2f}"
        return result
    
    def cancel(self, machine: 'VendingMachine') -> str:
        return "Cannot cancel while dispensing"


class VendingMachine:
    def __init__(self):
        self.state: VendingMachineState = IdleState()
        self.balance: float = 0
        self.selected_product: str = None
        self.products = {
            "cola": 1.50,
            "chips": 1.00,
            "candy": 0.75
        }
    
    def insert_money(self, amount: float) -> str:
        return self.state.insert_money(self, amount)
    
    def select_product(self, product: str) -> str:
        return self.state.select_product(self, product)
    
    def dispense(self) -> str:
        return self.state.dispense(self)
    
    def cancel(self) -> str:
        return self.state.cancel(self)


# Usage
vm = VendingMachine()

print(vm.select_product("cola"))  # Need money first
print(vm.insert_money(1.00))
print(vm.insert_money(0.75))
print(vm.select_product("cola"))
print(vm.dispense())  # Get change
```

---

## 🔄 Strategy vs State

| Aspect | Strategy | State |
|--------|----------|-------|
| **Focus** | Algorithm selection | Object behavior based on state |
| **Who changes** | Client sets strategy | State changes itself |
| **Transitions** | No state transitions | States transition to each other |
| **Awareness** | Strategies don't know each other | States know about other states |

---

## ❓ Interview Questions

1. **Q: When to use Strategy vs State?**
   - Strategy: Different ways to do the same thing
   - State: Same method behaves differently based on state

2. **Q: Can Strategy and State be combined?**
   - Yes! Each state can use different strategies

3. **Q: How does Strategy promote Open/Closed principle?**
   - Add new algorithms without modifying context class

---

*Last Updated: 2024*
