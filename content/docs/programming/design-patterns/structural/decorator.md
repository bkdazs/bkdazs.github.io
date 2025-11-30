---
title: "Decorator Pattern"
topic: "Structural Design Patterns"
difficulty: "Medium"
tags: ["decorator", "wrapper", "structural", "design-pattern"]
status: "complete"
weight: 2
---

# Decorator Pattern

## 📚 Overview

**Intent:** Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

**Also Known As:** Wrapper

---

## 1️⃣ Classic Decorator Pattern

```python
from abc import ABC, abstractmethod


# Component interface
class Coffee(ABC):
    @abstractmethod
    def get_description(self) -> str:
        pass
    
    @abstractmethod
    def get_cost(self) -> float:
        pass


# Concrete Component
class SimpleCoffee(Coffee):
    def get_description(self) -> str:
        return "Simple Coffee"
    
    def get_cost(self) -> float:
        return 2.00


# Base Decorator
class CoffeeDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee
    
    def get_description(self) -> str:
        return self._coffee.get_description()
    
    def get_cost(self) -> float:
        return self._coffee.get_cost()


# Concrete Decorators
class Milk(CoffeeDecorator):
    def get_description(self) -> str:
        return f"{self._coffee.get_description()}, Milk"
    
    def get_cost(self) -> float:
        return self._coffee.get_cost() + 0.50


class Sugar(CoffeeDecorator):
    def get_description(self) -> str:
        return f"{self._coffee.get_description()}, Sugar"
    
    def get_cost(self) -> float:
        return self._coffee.get_cost() + 0.25


class Vanilla(CoffeeDecorator):
    def get_description(self) -> str:
        return f"{self._coffee.get_description()}, Vanilla"
    
    def get_cost(self) -> float:
        return self._coffee.get_cost() + 0.75


class Whip(CoffeeDecorator):
    def get_description(self) -> str:
        return f"{self._coffee.get_description()}, Whipped Cream"
    
    def get_cost(self) -> float:
        return self._coffee.get_cost() + 0.60


# Usage - decorators can be stacked
coffee = SimpleCoffee()
coffee = Milk(coffee)
coffee = Sugar(coffee)
coffee = Vanilla(coffee)
coffee = Whip(coffee)

print(f"Order: {coffee.get_description()}")
print(f"Total: ${coffee.get_cost():.2f}")
```

---

## 2️⃣ Python Function Decorators

```python
import functools
import time
from typing import Callable, Any


# Basic decorator
def timer(func: Callable) -> Callable:
    """Measure execution time"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> Any:
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper


# Decorator with arguments
def retry(max_attempts: int = 3, delay: float = 1.0):
    """Retry decorator with configurable attempts"""
    def decorator(func: Callable) -> Callable:
        @functools.wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            last_exception = None
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_exception = e
                    print(f"Attempt {attempt + 1} failed: {e}")
                    if attempt < max_attempts - 1:
                        time.sleep(delay)
            raise last_exception
        return wrapper
    return decorator


# Caching decorator
def memoize(func: Callable) -> Callable:
    """Cache function results"""
    cache = {}
    
    @functools.wraps(func)
    def wrapper(*args) -> Any:
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    
    wrapper.cache = cache
    wrapper.clear_cache = lambda: cache.clear()
    return wrapper


# Logging decorator
def log_calls(func: Callable) -> Callable:
    """Log function calls with arguments"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> Any:
        args_repr = [repr(a) for a in args]
        kwargs_repr = [f"{k}={v!r}" for k, v in kwargs.items()]
        signature = ", ".join(args_repr + kwargs_repr)
        print(f"Calling {func.__name__}({signature})")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result!r}")
        return result
    return wrapper


# Usage
@timer
@log_calls
@memoize
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)


@retry(max_attempts=3, delay=0.5)
def unreliable_api_call():
    import random
    if random.random() < 0.7:
        raise ConnectionError("API unavailable")
    return "Success!"


# Test
result = fibonacci(10)
print(f"Fibonacci(10) = {result}")
```

---

## 3️⃣ Class-Based Python Decorators

```python
import functools
from typing import Callable, Any, Optional
from datetime import datetime


class CountCalls:
    """Class-based decorator to count function calls"""
    
    def __init__(self, func: Callable):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0
    
    def __call__(self, *args, **kwargs) -> Any:
        self.num_calls += 1
        print(f"Call {self.num_calls} of {self.func.__name__}")
        return self.func(*args, **kwargs)


class RateLimit:
    """Rate limiting decorator"""
    
    def __init__(self, max_calls: int, period: float):
        self.max_calls = max_calls
        self.period = period
        self.calls = []
    
    def __call__(self, func: Callable) -> Callable:
        @functools.wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            now = datetime.now().timestamp()
            
            # Remove old calls outside the window
            self.calls = [t for t in self.calls if now - t < self.period]
            
            if len(self.calls) >= self.max_calls:
                raise Exception(f"Rate limit exceeded: {self.max_calls} calls per {self.period}s")
            
            self.calls.append(now)
            return func(*args, **kwargs)
        return wrapper


class Singleton:
    """Singleton decorator for classes"""
    
    def __init__(self, cls):
        self._cls = cls
        self._instance = None
    
    def __call__(self, *args, **kwargs):
        if self._instance is None:
            self._instance = self._cls(*args, **kwargs)
        return self._instance


class ValidateTypes:
    """Type validation decorator"""
    
    def __init__(self, **expected_types):
        self.expected_types = expected_types
    
    def __call__(self, func: Callable) -> Callable:
        @functools.wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            # Validate kwargs
            for param, expected_type in self.expected_types.items():
                if param in kwargs:
                    if not isinstance(kwargs[param], expected_type):
                        raise TypeError(f"{param} must be {expected_type.__name__}")
            return func(*args, **kwargs)
        return wrapper


# Usage
@CountCalls
def greet(name: str) -> str:
    return f"Hello, {name}!"


@RateLimit(max_calls=3, period=10)
def api_request(endpoint: str) -> str:
    return f"Response from {endpoint}"


@Singleton
class DatabaseConnection:
    def __init__(self, host: str):
        self.host = host
        print(f"Connecting to {host}")


@ValidateTypes(age=int, name=str)
def create_user(name: str, age: int) -> dict:
    return {"name": name, "age": age}


# Test
print(greet("World"))
print(greet("Python"))
print(f"Total calls: {greet.num_calls}")

db1 = DatabaseConnection("localhost")
db2 = DatabaseConnection("remotehost")  # Same instance
print(f"Same instance: {db1 is db2}")
```

---

## 🏢 Real-World Examples

### HTTP Request/Response Decorators

```python
from abc import ABC, abstractmethod
from typing import Dict, Any, Optional
import gzip
import json
import hashlib


class HTTPHandler(ABC):
    @abstractmethod
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        pass


class BaseHandler(HTTPHandler):
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        return {
            "status": 200,
            "body": {"message": "OK"},
            "headers": {}
        }


class HandlerDecorator(HTTPHandler):
    def __init__(self, handler: HTTPHandler):
        self._handler = handler
    
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        return self._handler.handle(request)


class AuthenticationDecorator(HandlerDecorator):
    def __init__(self, handler: HTTPHandler, valid_tokens: set):
        super().__init__(handler)
        self.valid_tokens = valid_tokens
    
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        token = request.get("headers", {}).get("Authorization", "")
        
        if token.replace("Bearer ", "") not in self.valid_tokens:
            return {
                "status": 401,
                "body": {"error": "Unauthorized"},
                "headers": {}
            }
        
        return super().handle(request)


class LoggingDecorator(HandlerDecorator):
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        print(f"[LOG] Request: {request.get('method')} {request.get('path')}")
        response = super().handle(request)
        print(f"[LOG] Response: {response.get('status')}")
        return response


class CachingDecorator(HandlerDecorator):
    def __init__(self, handler: HTTPHandler, ttl: int = 300):
        super().__init__(handler)
        self.cache: Dict[str, Any] = {}
        self.ttl = ttl
    
    def _cache_key(self, request: Dict[str, Any]) -> str:
        key_data = f"{request.get('method')}:{request.get('path')}"
        return hashlib.md5(key_data.encode()).hexdigest()
    
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        # Only cache GET requests
        if request.get("method") != "GET":
            return super().handle(request)
        
        key = self._cache_key(request)
        if key in self.cache:
            print("[CACHE] Hit!")
            return self.cache[key]
        
        print("[CACHE] Miss")
        response = super().handle(request)
        self.cache[key] = response
        return response


class CompressionDecorator(HandlerDecorator):
    def handle(self, request: Dict[str, Any]) -> Dict[str, Any]:
        response = super().handle(request)
        
        # Check if client accepts gzip
        accept_encoding = request.get("headers", {}).get("Accept-Encoding", "")
        
        if "gzip" in accept_encoding:
            body = json.dumps(response["body"]).encode()
            compressed = gzip.compress(body)
            response["body"] = compressed
            response["headers"]["Content-Encoding"] = "gzip"
        
        return response


# Usage - stack decorators
handler = BaseHandler()
handler = LoggingDecorator(handler)
handler = CachingDecorator(handler)
handler = AuthenticationDecorator(handler, {"token123", "token456"})
handler = CompressionDecorator(handler)

# Test request
request = {
    "method": "GET",
    "path": "/api/users",
    "headers": {
        "Authorization": "Bearer token123",
        "Accept-Encoding": "gzip, deflate"
    }
}

response = handler.handle(request)
print(f"Final response status: {response['status']}")
```

### Stream Processing Decorators

```python
from abc import ABC, abstractmethod
from typing import Iterator, Any
import io


class DataStream(ABC):
    @abstractmethod
    def read(self) -> Iterator[str]:
        pass
    
    @abstractmethod  
    def write(self, data: str) -> None:
        pass


class FileStream(DataStream):
    def __init__(self, filename: str):
        self.filename = filename
        self._buffer = io.StringIO()
    
    def read(self) -> Iterator[str]:
        self._buffer.seek(0)
        for line in self._buffer:
            yield line.strip()
    
    def write(self, data: str) -> None:
        self._buffer.write(data + "\n")


class StreamDecorator(DataStream):
    def __init__(self, stream: DataStream):
        self._stream = stream
    
    def read(self) -> Iterator[str]:
        return self._stream.read()
    
    def write(self, data: str) -> None:
        self._stream.write(data)


class EncryptionDecorator(StreamDecorator):
    def __init__(self, stream: DataStream, key: int = 3):
        super().__init__(stream)
        self.key = key
    
    def _encrypt(self, text: str) -> str:
        return ''.join(chr(ord(c) + self.key) for c in text)
    
    def _decrypt(self, text: str) -> str:
        return ''.join(chr(ord(c) - self.key) for c in text)
    
    def read(self) -> Iterator[str]:
        for line in self._stream.read():
            yield self._decrypt(line)
    
    def write(self, data: str) -> None:
        self._stream.write(self._encrypt(data))


class CompressionDecorator(StreamDecorator):
    """Simple RLE compression for demonstration"""
    
    def _compress(self, text: str) -> str:
        if not text:
            return text
        result = []
        count = 1
        for i in range(1, len(text)):
            if text[i] == text[i-1]:
                count += 1
            else:
                result.append(f"{text[i-1]}{count}")
                count = 1
        result.append(f"{text[-1]}{count}")
        return ''.join(result)
    
    def _decompress(self, text: str) -> str:
        result = []
        i = 0
        while i < len(text):
            char = text[i]
            count = ""
            i += 1
            while i < len(text) and text[i].isdigit():
                count += text[i]
                i += 1
            result.append(char * int(count or 1))
        return ''.join(result)
    
    def read(self) -> Iterator[str]:
        for line in self._stream.read():
            yield self._decompress(line)
    
    def write(self, data: str) -> None:
        self._stream.write(self._compress(data))


# Usage
stream = FileStream("data.txt")
stream = CompressionDecorator(stream)
stream = EncryptionDecorator(stream, key=5)

# Write encrypted and compressed data
stream.write("Hello World!!!")
stream.write("aaabbbccc")

# Read decrypted and decompressed data  
for line in stream.read():
    print(f"Read: {line}")
```

---

## ❓ Interview Questions

1. **Q: Decorator vs Inheritance?**
   - Decorator: Runtime, composable, follows SRP
   - Inheritance: Compile-time, class explosion risk

2. **Q: When to use Decorator pattern?**
   - Add responsibilities without modifying classes
   - When extension via subclassing is impractical
   - Cross-cutting concerns (logging, auth, caching)

3. **Q: Decorator vs Proxy?**
   - Decorator: Adds behavior
   - Proxy: Controls access
   - Both wrap objects but with different intents

---

## 📝 Related Problems

| Problem | Platform | Key Pattern |
|---------|----------|-------------|
| Design Logger Rate Limiter | LeetCode #359 | Rate limit decorator |
| LRU Cache | LeetCode #146 | Caching decorator |
| Time Based Key-Value Store | LeetCode #981 | TTL decorator |

---

*Last Updated: 2024*
