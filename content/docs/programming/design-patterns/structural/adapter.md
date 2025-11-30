---
title: "Adapter Pattern"
topic: "Structural Design Patterns"
difficulty: "Medium"
tags: ["adapter", "wrapper", "structural", "design-pattern"]
status: "complete"
weight: 1
---

# Adapter Pattern

## 📚 Overview

**Intent:** Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.

**Also Known As:** Wrapper

---

## 1️⃣ Object Adapter (Composition)

```python
from abc import ABC, abstractmethod


# Target interface (what client expects)
class MediaPlayer(ABC):
    @abstractmethod
    def play(self, filename: str) -> str:
        pass


# Adaptee (existing class with incompatible interface)
class VLCPlayer:
    """Third-party VLC player with different interface"""
    
    def play_vlc(self, filename: str) -> str:
        return f"VLC playing: {filename}"


class FFmpegPlayer:
    """Third-party FFmpeg player with different interface"""
    
    def play_ffmpeg(self, filename: str) -> str:
        return f"FFmpeg playing: {filename}"


# Object Adapters (using composition)
class VLCAdapter(MediaPlayer):
    def __init__(self):
        self.vlc_player = VLCPlayer()
    
    def play(self, filename: str) -> str:
        return self.vlc_player.play_vlc(filename)


class FFmpegAdapter(MediaPlayer):
    def __init__(self):
        self.ffmpeg_player = FFmpegPlayer()
    
    def play(self, filename: str) -> str:
        return self.ffmpeg_player.play_ffmpeg(filename)


# Client code
class AudioPlayer:
    def __init__(self):
        self.adapters = {
            ".vlc": VLCAdapter(),
            ".mp4": FFmpegAdapter(),
            ".avi": FFmpegAdapter(),
        }
    
    def play(self, filename: str) -> str:
        ext = "." + filename.split(".")[-1]
        adapter = self.adapters.get(ext)
        
        if adapter:
            return adapter.play(filename)
        elif ext in [".mp3", ".wav"]:
            return f"Native playing: {filename}"
        else:
            return f"Unsupported format: {ext}"


# Usage
player = AudioPlayer()
print(player.play("song.mp3"))     # Native
print(player.play("video.vlc"))    # VLC adapter
print(player.play("movie.mp4"))    # FFmpeg adapter
```

---

## 2️⃣ Class Adapter (Inheritance)

```python
# Adaptee
class LegacyRectangle:
    """Old rectangle with x1,y1,x2,y2 coordinates"""
    
    def __init__(self, x1: int, y1: int, x2: int, y2: int):
        self.x1, self.y1 = x1, y1
        self.x2, self.y2 = x2, y2
    
    def legacy_draw(self) -> str:
        return f"Rectangle from ({self.x1},{self.y1}) to ({self.x2},{self.y2})"
    
    def legacy_area(self) -> int:
        return abs((self.x2 - self.x1) * (self.y2 - self.y1))


# Target interface
class Shape(ABC):
    @abstractmethod
    def draw(self) -> str:
        pass
    
    @abstractmethod
    def area(self) -> int:
        pass


# Class Adapter (multiple inheritance)
class RectangleAdapter(Shape, LegacyRectangle):
    def __init__(self, x: int, y: int, width: int, height: int):
        # Convert to legacy format
        LegacyRectangle.__init__(self, x, y, x + width, y + height)
        self.width = width
        self.height = height
    
    def draw(self) -> str:
        return self.legacy_draw()
    
    def area(self) -> int:
        return self.legacy_area()


# Usage
rect = RectangleAdapter(10, 10, 100, 50)
print(rect.draw())
print(f"Area: {rect.area()}")
```

---

## 3️⃣ Two-Way Adapter

```python
# Two different interfaces
class USPlug:
    def connect_us(self) -> str:
        return "Connected to US outlet (120V)"

class EUPlug:
    def connect_eu(self) -> str:
        return "Connected to EU outlet (230V)"


# Two-way adapter works with both
class UniversalAdapter(USPlug, EUPlug):
    def __init__(self, device_voltage: int = 120):
        self.device_voltage = device_voltage
    
    def connect_us(self) -> str:
        if self.device_voltage == 120:
            return "Direct US connection"
        return f"Converting {self.device_voltage}V → 120V, connected to US"
    
    def connect_eu(self) -> str:
        if self.device_voltage == 230:
            return "Direct EU connection"
        return f"Converting {self.device_voltage}V → 230V, connected to EU"


# Usage
adapter = UniversalAdapter(120)
print(adapter.connect_us())  # Direct
print(adapter.connect_eu())  # Converted
```

---

## 🏢 Real-World Examples

### API Response Adapter

```python
from typing import Dict, Any, List
from dataclasses import dataclass
from datetime import datetime
import json


# External API response format
class ExternalAPIResponse:
    """Third-party API returns data in specific format"""
    
    def __init__(self, raw_data: dict):
        self.raw_data = raw_data
    
    def get_payload(self) -> dict:
        return self.raw_data.get("data", {})
    
    def get_status_code(self) -> int:
        return self.raw_data.get("statusCode", 0)
    
    def get_timestamp(self) -> str:
        return self.raw_data.get("ts", "")


# Our internal response format
@dataclass
class InternalResponse:
    success: bool
    data: Dict[str, Any]
    timestamp: datetime
    error: str = ""


# Adapter
class APIResponseAdapter:
    """Adapts external API response to internal format"""
    
    def __init__(self, external_response: ExternalAPIResponse):
        self.external = external_response
    
    def to_internal(self) -> InternalResponse:
        status = self.external.get_status_code()
        ts_str = self.external.get_timestamp()
        
        # Parse timestamp
        try:
            timestamp = datetime.fromisoformat(ts_str.replace("Z", "+00:00"))
        except:
            timestamp = datetime.now()
        
        return InternalResponse(
            success=200 <= status < 300,
            data=self.external.get_payload(),
            timestamp=timestamp,
            error="" if status < 400 else f"Error code: {status}"
        )


# Usage
external_data = {
    "statusCode": 200,
    "ts": "2024-01-15T10:30:00Z",
    "data": {"users": [{"id": 1, "name": "John"}]}
}

external_response = ExternalAPIResponse(external_data)
adapter = APIResponseAdapter(external_response)
internal = adapter.to_internal()

print(f"Success: {internal.success}")
print(f"Data: {internal.data}")
```

### Payment Gateway Adapter

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import Optional


@dataclass
class PaymentResult:
    success: bool
    transaction_id: str
    amount: float
    error: Optional[str] = None


# Target interface
class PaymentGateway(ABC):
    @abstractmethod
    def charge(self, amount: float, card_number: str, cvv: str) -> PaymentResult:
        pass
    
    @abstractmethod
    def refund(self, transaction_id: str, amount: float) -> PaymentResult:
        pass


# Adaptee 1: Stripe-like API
class StripeAPI:
    def create_charge(self, params: dict) -> dict:
        return {
            "id": f"ch_{hash(params['amount'])}",
            "status": "succeeded",
            "amount": params["amount"] * 100  # Stripe uses cents
        }
    
    def create_refund(self, charge_id: str, amount_cents: int) -> dict:
        return {
            "id": f"re_{hash(charge_id)}",
            "status": "succeeded",
            "amount": amount_cents
        }


# Adaptee 2: PayPal-like API
class PayPalAPI:
    def execute_payment(self, payment_data: dict) -> dict:
        return {
            "payment_id": f"PAY-{hash(str(payment_data))}",
            "state": "approved",
            "total": payment_data["total"]
        }
    
    def refund_payment(self, payment_id: str, refund_amount: float) -> dict:
        return {
            "refund_id": f"REF-{hash(payment_id)}",
            "state": "completed", 
            "amount": refund_amount
        }


# Adapters
class StripeAdapter(PaymentGateway):
    def __init__(self, api_key: str):
        self.stripe = StripeAPI()
        self.api_key = api_key
    
    def charge(self, amount: float, card_number: str, cvv: str) -> PaymentResult:
        response = self.stripe.create_charge({
            "amount": amount,
            "source": card_number[-4:]  # Last 4 digits
        })
        
        return PaymentResult(
            success=response["status"] == "succeeded",
            transaction_id=response["id"],
            amount=response["amount"] / 100  # Convert cents to dollars
        )
    
    def refund(self, transaction_id: str, amount: float) -> PaymentResult:
        response = self.stripe.create_refund(
            transaction_id,
            int(amount * 100)
        )
        
        return PaymentResult(
            success=response["status"] == "succeeded",
            transaction_id=response["id"],
            amount=response["amount"] / 100
        )


class PayPalAdapter(PaymentGateway):
    def __init__(self, client_id: str, secret: str):
        self.paypal = PayPalAPI()
        self.client_id = client_id
        self.secret = secret
    
    def charge(self, amount: float, card_number: str, cvv: str) -> PaymentResult:
        response = self.paypal.execute_payment({
            "total": amount,
            "currency": "USD"
        })
        
        return PaymentResult(
            success=response["state"] == "approved",
            transaction_id=response["payment_id"],
            amount=response["total"]
        )
    
    def refund(self, transaction_id: str, amount: float) -> PaymentResult:
        response = self.paypal.refund_payment(transaction_id, amount)
        
        return PaymentResult(
            success=response["state"] == "completed",
            transaction_id=response["refund_id"],
            amount=response["amount"]
        )


# Client code works with any gateway
class PaymentProcessor:
    def __init__(self, gateway: PaymentGateway):
        self.gateway = gateway
    
    def process_payment(self, amount: float, card: str, cvv: str) -> PaymentResult:
        return self.gateway.charge(amount, card, cvv)


# Usage
stripe_processor = PaymentProcessor(StripeAdapter("sk_test_xxx"))
paypal_processor = PaymentProcessor(PayPalAdapter("client_id", "secret"))

result1 = stripe_processor.process_payment(99.99, "4242424242424242", "123")
result2 = paypal_processor.process_payment(99.99, "4242424242424242", "123")

print(f"Stripe: {result1}")
print(f"PayPal: {result2}")
```

### Database ORM Adapter

```python
from abc import ABC, abstractmethod
from typing import List, Dict, Any, Optional


# Target interface
class Repository(ABC):
    @abstractmethod
    def find_all(self) -> List[Dict]:
        pass
    
    @abstractmethod
    def find_by_id(self, id: int) -> Optional[Dict]:
        pass
    
    @abstractmethod
    def save(self, entity: Dict) -> Dict:
        pass
    
    @abstractmethod
    def delete(self, id: int) -> bool:
        pass


# Adaptee 1: SQLAlchemy-like ORM
class SQLAlchemySession:
    def __init__(self):
        self._data = {}
        self._counter = 0
    
    def query(self, model: str) -> 'SQLAlchemyQuery':
        return SQLAlchemyQuery(self._data.get(model, []))
    
    def add(self, model: str, obj: dict) -> dict:
        self._counter += 1
        obj["id"] = self._counter
        if model not in self._data:
            self._data[model] = []
        self._data[model].append(obj)
        return obj
    
    def delete(self, model: str, obj: dict) -> None:
        if model in self._data:
            self._data[model] = [o for o in self._data[model] if o["id"] != obj["id"]]


class SQLAlchemyQuery:
    def __init__(self, data: list):
        self._data = data
    
    def all(self) -> list:
        return self._data
    
    def filter_by(self, **kwargs) -> 'SQLAlchemyQuery':
        filtered = [d for d in self._data if all(d.get(k) == v for k, v in kwargs.items())]
        return SQLAlchemyQuery(filtered)
    
    def first(self) -> Optional[dict]:
        return self._data[0] if self._data else None


# Adaptee 2: MongoDB-like client
class MongoCollection:
    def __init__(self):
        self._data = []
        self._counter = 0
    
    def find(self, query: dict = None) -> list:
        if not query:
            return self._data
        return [d for d in self._data if all(d.get(k) == v for k, v in query.items())]
    
    def find_one(self, query: dict) -> Optional[dict]:
        results = self.find(query)
        return results[0] if results else None
    
    def insert_one(self, document: dict) -> dict:
        self._counter += 1
        document["_id"] = self._counter
        self._data.append(document)
        return document
    
    def delete_one(self, query: dict) -> int:
        before = len(self._data)
        self._data = [d for d in self._data if not all(d.get(k) == v for k, v in query.items())]
        return before - len(self._data)


# Adapters
class SQLAlchemyAdapter(Repository):
    def __init__(self, session: SQLAlchemySession, model: str):
        self.session = session
        self.model = model
    
    def find_all(self) -> List[Dict]:
        return self.session.query(self.model).all()
    
    def find_by_id(self, id: int) -> Optional[Dict]:
        return self.session.query(self.model).filter_by(id=id).first()
    
    def save(self, entity: Dict) -> Dict:
        return self.session.add(self.model, entity)
    
    def delete(self, id: int) -> bool:
        entity = self.find_by_id(id)
        if entity:
            self.session.delete(self.model, entity)
            return True
        return False


class MongoAdapter(Repository):
    def __init__(self, collection: MongoCollection):
        self.collection = collection
    
    def find_all(self) -> List[Dict]:
        results = self.collection.find()
        return [{**d, "id": d.pop("_id")} for d in results]
    
    def find_by_id(self, id: int) -> Optional[Dict]:
        result = self.collection.find_one({"_id": id})
        if result:
            result["id"] = result.pop("_id")
        return result
    
    def save(self, entity: Dict) -> Dict:
        result = self.collection.insert_one(entity)
        result["id"] = result.pop("_id")
        return result
    
    def delete(self, id: int) -> bool:
        return self.collection.delete_one({"_id": id}) > 0


# Usage
sql_session = SQLAlchemySession()
sql_repo = SQLAlchemyAdapter(sql_session, "users")

mongo_collection = MongoCollection()
mongo_repo = MongoAdapter(mongo_collection)

# Same interface for both
for repo in [sql_repo, mongo_repo]:
    repo.save({"name": "John", "email": "john@example.com"})
    repo.save({"name": "Jane", "email": "jane@example.com"})
    
    print(f"All users: {repo.find_all()}")
    print(f"User 1: {repo.find_by_id(1)}")
```

---

## ❓ Interview Questions

1. **Q: Object Adapter vs Class Adapter?**
   - Object: Uses composition, more flexible, works at runtime
   - Class: Uses inheritance, can override behavior, compile-time

2. **Q: Adapter vs Bridge vs Decorator?**
   - Adapter: Makes incompatible interfaces work together
   - Bridge: Separates abstraction from implementation
   - Decorator: Adds behavior without changing interface

3. **Q: When to use Adapter pattern?**
   - Integrating with third-party libraries
   - Legacy code integration
   - Testing (mock adapters)

---

*Last Updated: 2024*
