---
title: "Singleton Pattern"
topic: "Creational Design Patterns"
difficulty: "Easy"
tags: ["singleton", "creational", "design-pattern", "thread-safe"]
status: "complete"
weight: 1
---

# Singleton Pattern

## 📚 Intent

Ensure a class has only one instance and provide a global point of access to it.

---

## 🔑 Key Components

1. **Private Constructor** - Prevents direct instantiation
2. **Static Instance** - Holds the single instance
3. **Static Access Method** - Returns the instance

---

## 📝 Implementations

### 1. Classic Singleton (Not Thread-Safe)

```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```

### 2. Thread-Safe Singleton (Double-Checked Locking)

```python
from threading import Lock

class ThreadSafeSingleton:
    _instance = None
    _lock = Lock()
    
    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                # Double check after acquiring lock
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        # Initialize only once
        if not hasattr(self, '_initialized'):
            self._initialized = True
            self.data = {}
```

### 3. Decorator-Based Singleton

```python
def singleton(cls):
    """Singleton decorator"""
    instances = {}
    
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    
    return get_instance


@singleton
class Database:
    def __init__(self, connection_string: str = ""):
        self.connection_string = connection_string
        print(f"Database initialized with {connection_string}")
```

### 4. Metaclass Singleton

```python
class SingletonMeta(type):
    _instances = {}
    _lock = Lock()
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            with cls._lock:
                if cls not in cls._instances:
                    instance = super().__call__(*args, **kwargs)
                    cls._instances[cls] = instance
        return cls._instances[cls]


class Logger(metaclass=SingletonMeta):
    def __init__(self):
        self.logs = []
    
    def log(self, message: str):
        self.logs.append(message)
        print(f"[LOG] {message}")
```

### 5. Module-Level Singleton (Pythonic Way)

```python
# config.py
class _Config:
    def __init__(self):
        self.settings = {}
    
    def set(self, key: str, value):
        self.settings[key] = value
    
    def get(self, key: str, default=None):
        return self.settings.get(key, default)

# Single instance at module level
config = _Config()

# Usage: from config import config
```

### 6. Lazy Initialization Singleton

```python
class LazySingleton:
    _instance = None
    
    @classmethod
    def get_instance(cls):
        if cls._instance is None:
            cls._instance = cls._create_instance()
        return cls._instance
    
    @classmethod
    def _create_instance(cls):
        instance = object.__new__(cls)
        instance._initialize()
        return instance
    
    def _initialize(self):
        self.expensive_resource = "Loaded"
```

---

## 🏢 Real-World Examples

### Configuration Manager

```python
import json
from pathlib import Path
from threading import Lock

class ConfigManager:
    _instance = None
    _lock = Lock()
    
    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
                    cls._instance._config = {}
                    cls._instance._loaded = False
        return cls._instance
    
    def load(self, config_path: str):
        if not self._loaded:
            with open(config_path) as f:
                self._config = json.load(f)
            self._loaded = True
    
    def get(self, key: str, default=None):
        return self._config.get(key, default)
    
    def set(self, key: str, value):
        self._config[key] = value


# Usage
config = ConfigManager()
config.load("settings.json")
db_host = config.get("database.host", "localhost")
```

### Connection Pool

```python
from queue import Queue
from threading import Lock
import sqlite3

class ConnectionPool:
    _instance = None
    _lock = Lock()
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self, db_path: str = ":memory:", pool_size: int = 5):
        if hasattr(self, '_initialized'):
            return
        self._initialized = True
        self.db_path = db_path
        self.pool_size = pool_size
        self._pool = Queue(maxsize=pool_size)
        self._create_connections()
    
    def _create_connections(self):
        for _ in range(self.pool_size):
            conn = sqlite3.connect(self.db_path, check_same_thread=False)
            self._pool.put(conn)
    
    def get_connection(self):
        return self._pool.get()
    
    def return_connection(self, conn):
        self._pool.put(conn)
    
    def __enter__(self):
        self._conn = self.get_connection()
        return self._conn
    
    def __exit__(self, *args):
        self.return_connection(self._conn)


# Usage
pool = ConnectionPool("app.db", pool_size=10)

with pool as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users")
```

### Logger

```python
import logging
from datetime import datetime
from threading import Lock

class AppLogger:
    _instance = None
    _lock = Lock()
    
    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
                    cls._instance._setup_logger()
        return cls._instance
    
    def _setup_logger(self):
        self.logger = logging.getLogger("AppLogger")
        self.logger.setLevel(logging.DEBUG)
        
        handler = logging.StreamHandler()
        formatter = logging.Formatter(
            '%(asctime)s - %(levelname)s - %(message)s'
        )
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
    
    def debug(self, msg): self.logger.debug(msg)
    def info(self, msg): self.logger.info(msg)
    def warning(self, msg): self.logger.warning(msg)
    def error(self, msg): self.logger.error(msg)


# Usage anywhere in app
logger = AppLogger()
logger.info("Application started")
```

---

## ⚠️ Pitfalls & Solutions

### 1. Testing Difficulty

```python
# Problem: Hard to test with singleton
# Solution: Use dependency injection

class Service:
    def __init__(self, logger=None):
        self.logger = logger or AppLogger()

# In tests
mock_logger = Mock()
service = Service(logger=mock_logger)
```

### 2. Hidden Dependencies

```python
# Problem: Global state makes dependencies unclear
# Solution: Make dependency explicit

# Bad
class OrderProcessor:
    def process(self, order):
        config = ConfigManager()  # Hidden dependency
        
# Good
class OrderProcessor:
    def __init__(self, config: ConfigManager):
        self.config = config  # Explicit dependency
```

### 3. Multiprocessing Issues

```python
# Problem: Singleton not shared across processes
# Solution: Use shared memory or avoid singleton

from multiprocessing import Manager

class ProcessSafeSingleton:
    _manager = Manager()
    _shared_data = _manager.dict()
    
    @classmethod
    def get_data(cls):
        return cls._shared_data
```

---

## ✅ When to Use

- Configuration management
- Logging
- Connection pools
- Caches
- Thread pools
- Registry objects

## ❌ When to Avoid

- When you need multiple instances later
- In heavily tested code (use DI instead)
- When global state causes issues
- In multiprocessing scenarios

---

## 📚 Related Patterns

- **Factory** - Often used to create singleton instances
- **Abstract Factory** - Can be singleton
- **Facade** - Often singleton for subsystem access

---

## 🔗 LeetCode Problems

| # | Problem | Concept |
|---|---------|---------|
| - | Design patterns rarely appear directly | Use in system design |

---

*Last Updated: 2024*
