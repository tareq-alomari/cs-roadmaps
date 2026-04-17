# 03 — Design Patterns

---

## التصنيفات الثلاثة (GoF)

### 1. Creational Patterns — كيف تُنشئ الكائنات

```python
# Singleton — كائن واحد فقط
class Database:
    _instance = None
    def __new__(cls):
        if not cls._instance:
            cls._instance = super().__new__(cls)
        return cls._instance

# Factory — إنشاء كائنات بدون تحديد النوع
class AnimalFactory:
    @staticmethod
    def create(animal_type):
        if animal_type == "dog": return Dog()
        if animal_type == "cat": return Cat()

# Builder — بناء كائنات معقدة خطوة بخطوة
class QueryBuilder:
    def __init__(self): self._query = {}
    def select(self, *cols): self._query['select'] = cols; return self
    def where(self, cond): self._query['where'] = cond; return self
    def build(self): return self._query
```

### 2. Structural Patterns — كيف تربط الكائنات

```python
# Adapter — تحويل interface لآخر
class EuropeanSocket:
    def voltage(self): return 220

class USAdapter:
    def __init__(self, socket): self._socket = socket
    def voltage(self): return self._socket.voltage() // 2  # 110V

# Decorator — إضافة سلوك ديناميكيًا
def log_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

# Facade — واجهة بسيطة لنظام معقد
class HomeTheaterFacade:
    def watch_movie(self):
        self.tv.on()
        self.sound.set_surround()
        self.projector.on()
```

### 3. Behavioral Patterns — كيف تتواصل الكائنات

```python
# Observer — اشتراك في الأحداث
class EventEmitter:
    def __init__(self): self._listeners = {}
    def on(self, event, callback): self._listeners.setdefault(event, []).append(callback)
    def emit(self, event, data): [cb(data) for cb in self._listeners.get(event, [])]

# Strategy — تبديل الخوارزمية وقت التشغيل
class Sorter:
    def __init__(self, strategy): self._strategy = strategy
    def sort(self, data): return self._strategy(data)

sorter = Sorter(sorted)          # يمكن تغييرها لاحقًا

# Command — تغليف الطلب ككائن (Undo/Redo)
class TextEditor:
    def __init__(self): self._history = []
    def execute(self, command): self._history.append(command); command.execute()
    def undo(self): self._history.pop().undo()
```

---

## SOLID Principles

```
S - Single Responsibility  كل كلاس مسؤولية واحدة
O - Open/Closed            مفتوح للتوسع، مغلق للتعديل
L - Liskov Substitution    الـ Subclass تحل محل الـ Parent
I - Interface Segregation  interfaces صغيرة ومحددة
D - Dependency Inversion   اعتمد على Abstractions لا Concretions
```

---

## ⏱️ الوقت المقدر: 3-4 أسابيع

## مصادر
- كتاب: *Design Patterns* - Gang of Four
- [Refactoring Guru](https://refactoring.guru/design-patterns) ← مجاني وممتاز بصريًا
