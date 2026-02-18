---
title: "Facade, Proxy, Bridge, Composite, Flyweight"
topic: "Structural Design Patterns"
difficulty: "Medium"
tags: ["facade", "proxy", "bridge", "composite", "flyweight", "structural", "design-pattern"]
status: "complete"
weight: 3
---

# Structural Design Patterns (Part 2)

---

## 1️⃣ Facade Pattern

**Intent:** Provide a unified interface to a set of interfaces in a subsystem.

```python
# Complex subsystem classes
class VideoConverter:
    def convert(self, filename: str, format: str) -> bytes:
        return f"Converting {filename} to {format}".encode()

class AudioExtractor:
    def extract(self, video: bytes) -> bytes:
        return b"extracted_audio"

class CodecFactory:
    def get_codec(self, format: str):
        return {"mp4": "H264", "avi": "MPEG"}.get(format, "RAW")

class BitrateReader:
    def read(self, video: bytes) -> int:
        return 128000

class Mixer:
    def mix(self, video: bytes, audio: bytes) -> bytes:
        return b"mixed_output"


# Facade
class VideoConversionFacade:
    """Simple interface to complex video conversion subsystem"""
    
    def __init__(self):
        self._converter = VideoConverter()
        self._audio = AudioExtractor()
        self._codec = CodecFactory()
        self._bitrate = BitrateReader()
        self._mixer = Mixer()
    
    def convert(self, filename: str, target_format: str) -> bytes:
        """One simple method hides all complexity"""
        print(f"Converting {filename} to {target_format}")
        
        # Complex internal operations
        codec = self._codec.get_codec(target_format)
        video = self._converter.convert(filename, target_format)
        audio = self._audio.extract(video)
        bitrate = self._bitrate.read(video)
        result = self._mixer.mix(video, audio)
        
        print(f"Used codec: {codec}, bitrate: {bitrate}")
        return result


# Client uses simple interface
facade = VideoConversionFacade()
result = facade.convert("movie.avi", "mp4")
```

---

## 2️⃣ Proxy Pattern

**Intent:** Provide a surrogate or placeholder for another object to control access.

### Types of Proxy

```python
from abc import ABC, abstractmethod
from typing import Optional
import time


# Subject interface
class Image(ABC):
    @abstractmethod
    def display(self) -> str:
        pass
    
    @abstractmethod
    def get_info(self) -> dict:
        pass


# Real Subject
class RealImage(Image):
    def __init__(self, filename: str):
        self.filename = filename
        self._load_from_disk()  # Expensive operation
    
    def _load_from_disk(self):
        print(f"Loading {self.filename} from disk...")
        time.sleep(0.1)  # Simulate slow load
        self.size = 1024 * 1024  # 1MB
    
    def display(self) -> str:
        return f"Displaying {self.filename}"
    
    def get_info(self) -> dict:
        return {"filename": self.filename, "size": self.size}


# Virtual Proxy (Lazy Loading)
class LazyImageProxy(Image):
    """Delays object creation until needed"""
    
    def __init__(self, filename: str):
        self.filename = filename
        self._real_image: Optional[RealImage] = None
    
    def _get_real_image(self) -> RealImage:
        if self._real_image is None:
            self._real_image = RealImage(self.filename)
        return self._real_image
    
    def display(self) -> str:
        return self._get_real_image().display()
    
    def get_info(self) -> dict:
        # Return basic info without loading
        return {"filename": self.filename, "loaded": self._real_image is not None}


# Protection Proxy (Access Control)
class ProtectedImageProxy(Image):
    """Controls access based on permissions"""
    
    def __init__(self, image: Image, user_role: str):
        self._image = image
        self._user_role = user_role
    
    def display(self) -> str:
        if self._user_role in ["admin", "viewer"]:
            return self._image.display()
        return "Access denied: insufficient permissions"
    
    def get_info(self) -> dict:
        return self._image.get_info()


# Caching Proxy
class CachingImageProxy(Image):
    """Caches results to avoid repeated operations"""
    
    _cache: dict = {}
    
    def __init__(self, filename: str):
        self.filename = filename
    
    def _get_image(self) -> RealImage:
        if self.filename not in self._cache:
            self._cache[self.filename] = RealImage(self.filename)
        else:
            print(f"Using cached image: {self.filename}")
        return self._cache[self.filename]
    
    def display(self) -> str:
        return self._get_image().display()
    
    def get_info(self) -> dict:
        return self._get_image().get_info()


# Logging Proxy
class LoggingImageProxy(Image):
    """Logs all access to the real object"""
    
    def __init__(self, image: Image):
        self._image = image
    
    def display(self) -> str:
        print(f"[LOG] display() called at {time.time()}")
        result = self._image.display()
        print(f"[LOG] display() returned: {result[:20]}...")
        return result
    
    def get_info(self) -> dict:
        print(f"[LOG] get_info() called")
        return self._image.get_info()


# Usage
print("=== Lazy Proxy ===")
lazy = LazyImageProxy("photo.jpg")
print(lazy.get_info())  # No loading
print(lazy.display())   # Loads here

print("\n=== Caching Proxy ===")
cached1 = CachingImageProxy("image.png")
cached2 = CachingImageProxy("image.png")
print(cached1.display())  # Loads
print(cached2.display())  # Uses cache
```

---

## 3️⃣ Bridge Pattern

**Intent:** Decouple an abstraction from its implementation so they can vary independently.

```python
from abc import ABC, abstractmethod


# Implementation hierarchy
class Renderer(ABC):
    @abstractmethod
    def render_circle(self, x: int, y: int, radius: int) -> str:
        pass
    
    @abstractmethod
    def render_rectangle(self, x: int, y: int, w: int, h: int) -> str:
        pass


class VectorRenderer(Renderer):
    def render_circle(self, x: int, y: int, radius: int) -> str:
        return f"Drawing circle as vectors at ({x},{y}) r={radius}"
    
    def render_rectangle(self, x: int, y: int, w: int, h: int) -> str:
        return f"Drawing rectangle as vectors at ({x},{y}) {w}x{h}"


class RasterRenderer(Renderer):
    def render_circle(self, x: int, y: int, radius: int) -> str:
        return f"Drawing pixels for circle at ({x},{y}) r={radius}"
    
    def render_rectangle(self, x: int, y: int, w: int, h: int) -> str:
        return f"Drawing pixels for rectangle at ({x},{y}) {w}x{h}"


class SVGRenderer(Renderer):
    def render_circle(self, x: int, y: int, radius: int) -> str:
        return f'<circle cx="{x}" cy="{y}" r="{radius}"/>'
    
    def render_rectangle(self, x: int, y: int, w: int, h: int) -> str:
        return f'<rect x="{x}" y="{y}" width="{w}" height="{h}"/>'


# Abstraction hierarchy
class Shape(ABC):
    def __init__(self, renderer: Renderer):
        self.renderer = renderer  # Bridge to implementation
    
    @abstractmethod
    def draw(self) -> str:
        pass
    
    @abstractmethod
    def resize(self, factor: float) -> None:
        pass


class Circle(Shape):
    def __init__(self, renderer: Renderer, x: int, y: int, radius: int):
        super().__init__(renderer)
        self.x, self.y, self.radius = x, y, radius
    
    def draw(self) -> str:
        return self.renderer.render_circle(self.x, self.y, self.radius)
    
    def resize(self, factor: float) -> None:
        self.radius = int(self.radius * factor)


class Rectangle(Shape):
    def __init__(self, renderer: Renderer, x: int, y: int, w: int, h: int):
        super().__init__(renderer)
        self.x, self.y, self.w, self.h = x, y, w, h
    
    def draw(self) -> str:
        return self.renderer.render_rectangle(self.x, self.y, self.w, self.h)
    
    def resize(self, factor: float) -> None:
        self.w = int(self.w * factor)
        self.h = int(self.h * factor)


# Usage - shapes and renderers can vary independently
vector = VectorRenderer()
raster = RasterRenderer()
svg = SVGRenderer()

shapes = [
    Circle(vector, 10, 10, 5),
    Circle(svg, 20, 20, 10),
    Rectangle(raster, 0, 0, 100, 50),
]

for shape in shapes:
    print(shape.draw())
```

---

## 4️⃣ Composite Pattern

**Intent:** Compose objects into tree structures. Let clients treat individual objects and compositions uniformly.

```python
from abc import ABC, abstractmethod
from typing import List


# Component
class FileSystemItem(ABC):
    def __init__(self, name: str):
        self.name = name
    
    @abstractmethod
    def get_size(self) -> int:
        pass
    
    @abstractmethod
    def display(self, indent: int = 0) -> str:
        pass
    
    def add(self, item: 'FileSystemItem') -> None:
        raise NotImplementedError("Cannot add to a file")
    
    def remove(self, item: 'FileSystemItem') -> None:
        raise NotImplementedError("Cannot remove from a file")


# Leaf
class File(FileSystemItem):
    def __init__(self, name: str, size: int):
        super().__init__(name)
        self.size = size
    
    def get_size(self) -> int:
        return self.size
    
    def display(self, indent: int = 0) -> str:
        return f"{'  ' * indent}📄 {self.name} ({self.size} bytes)"


# Composite
class Directory(FileSystemItem):
    def __init__(self, name: str):
        super().__init__(name)
        self.children: List[FileSystemItem] = []
    
    def add(self, item: FileSystemItem) -> None:
        self.children.append(item)
    
    def remove(self, item: FileSystemItem) -> None:
        self.children.remove(item)
    
    def get_size(self) -> int:
        return sum(child.get_size() for child in self.children)
    
    def display(self, indent: int = 0) -> str:
        lines = [f"{'  ' * indent}📁 {self.name}/"]
        for child in self.children:
            lines.append(child.display(indent + 1))
        return '\n'.join(lines)


# Build file system tree
root = Directory("root")
home = Directory("home")
user = Directory("user")

user.add(File("document.txt", 1024))
user.add(File("photo.jpg", 2048000))

downloads = Directory("downloads")
downloads.add(File("movie.mp4", 1500000000))
downloads.add(File("song.mp3", 5000000))

user.add(downloads)
home.add(user)
root.add(home)

root.add(Directory("etc"))
root.add(File("README.md", 512))

print(root.display())
print(f"\nTotal size: {root.get_size():,} bytes")
```

---

## 5️⃣ Flyweight Pattern

**Intent:** Use sharing to support large numbers of fine-grained objects efficiently.

```python
from typing import Dict, Tuple
from dataclasses import dataclass


# Flyweight (intrinsic state - shared)
@dataclass(frozen=True)
class CharacterStyle:
    """Shared character formatting"""
    font: str
    size: int
    bold: bool
    italic: bool


# Flyweight Factory
class StyleFactory:
    _styles: Dict[Tuple, CharacterStyle] = {}
    
    @classmethod
    def get_style(cls, font: str, size: int, bold: bool = False, 
                  italic: bool = False) -> CharacterStyle:
        key = (font, size, bold, italic)
        
        if key not in cls._styles:
            cls._styles[key] = CharacterStyle(font, size, bold, italic)
            print(f"Created new style: {key}")
        
        return cls._styles[key]
    
    @classmethod
    def style_count(cls) -> int:
        return len(cls._styles)


# Context (extrinsic state - unique per character)
@dataclass
class Character:
    char: str
    x: int
    y: int
    style: CharacterStyle  # Flyweight reference
    
    def render(self) -> str:
        return f"'{self.char}' at ({self.x},{self.y}) [{self.style.font} {self.style.size}pt]"


# Document using flyweight
class Document:
    def __init__(self):
        self.characters: list[Character] = []
    
    def add_character(self, char: str, x: int, y: int, 
                     font: str, size: int, bold: bool = False, 
                     italic: bool = False):
        style = StyleFactory.get_style(font, size, bold, italic)
        self.characters.append(Character(char, x, y, style))
    
    def render(self) -> str:
        return '\n'.join(c.render() for c in self.characters[:5]) + "..."


# Usage - many characters share few styles
doc = Document()

text = "Hello World! This is a test document with many characters."
x = 0
for char in text:
    doc.add_character(char, x, 0, "Arial", 12)
    x += 10

# Add some bold text
for i, char in enumerate("IMPORTANT"):
    doc.add_character(char, i * 12, 20, "Arial", 14, bold=True)

print(doc.render())
print(f"\nTotal characters: {len(doc.characters)}")
print(f"Unique styles: {StyleFactory.style_count()}")
```

### Game Example - Tree Forest

```python
from dataclasses import dataclass
from typing import Dict, List
import random


# Flyweight - shared tree type data
@dataclass(frozen=True)
class TreeType:
    name: str
    color: str
    texture: str  # In real app, this would be heavy texture data
    
    def draw(self, x: int, y: int) -> str:
        return f"{self.name} tree at ({x},{y})"


# Flyweight Factory
class TreeFactory:
    _types: Dict[str, TreeType] = {}
    
    @classmethod
    def get_tree_type(cls, name: str, color: str, texture: str) -> TreeType:
        key = f"{name}_{color}_{texture}"
        
        if key not in cls._types:
            cls._types[key] = TreeType(name, color, texture)
        
        return cls._types[key]
    
    @classmethod
    def type_count(cls) -> int:
        return len(cls._types)


# Context - individual tree with position
@dataclass
class Tree:
    x: int
    y: int
    tree_type: TreeType
    
    def draw(self) -> str:
        return self.tree_type.draw(self.x, self.y)


# Forest containing many trees
class Forest:
    def __init__(self):
        self.trees: List[Tree] = []
    
    def plant_tree(self, x: int, y: int, name: str, color: str, texture: str):
        tree_type = TreeFactory.get_tree_type(name, color, texture)
        self.trees.append(Tree(x, y, tree_type))
    
    def draw(self) -> str:
        return f"Forest with {len(self.trees)} trees"


# Create large forest efficiently
forest = Forest()

tree_specs = [
    ("Oak", "green", "oak_bark"),
    ("Pine", "dark_green", "pine_bark"),
    ("Birch", "light_green", "birch_bark"),
]

# Plant 10,000 trees but only 3 tree types
for _ in range(10000):
    name, color, texture = random.choice(tree_specs)
    x, y = random.randint(0, 1000), random.randint(0, 1000)
    forest.plant_tree(x, y, name, color, texture)

print(forest.draw())
print(f"Memory efficient: Only {TreeFactory.type_count()} tree types for 10,000 trees")
```

---

## 🔄 Pattern Comparison

| Pattern | Purpose | Key Mechanism |
|---------|---------|---------------|
| **Adapter** | Interface compatibility | Wraps with different interface |
| **Bridge** | Abstraction/impl separation | Composition over inheritance |
| **Composite** | Tree structures | Uniform component interface |
| **Decorator** | Add behavior dynamically | Wraps with same interface |
| **Facade** | Simplify subsystem | Single entry point |
| **Flyweight** | Memory optimization | Share common state |
| **Proxy** | Control access | Surrogate object |

---

*Last Updated: 2024*
