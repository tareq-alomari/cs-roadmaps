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

## أمثلة SOLID مفصلة

### S — Single Responsibility

```
Bad:
class User:
    def authenticate(self): ...
    def send_email(self): ...
    def generate_report(self): ...
    def save_to_database(self): ...

Good:
class Authenticator: ...
class EmailService: ...
class ReportGenerator: ...
class UserRepository: ...
```

### O — Open/Closed

```
Bad (تعدل الكود عند إضافة ميزة):
class Discount:
    def calculate(self, user):
        if user.type == "student": return 0.1
        if user.type == "senior": return 0.2
        return 0

Good (تضيف بدون تعديل):
class Discount(ABC):
    @abstractmethod
    def calculate(self, amount): pass

class StudentDiscount(Discount):
    def calculate(self, amount): return amount * 0.1

class SeniorDiscount(Discount):
    def calculate(self, amount): return amount * 0.2
```

### L — Liskov Substitution

```
Bad (الـ Subclass يختلف سلوك):
class Bird:
    def fly(self): pass

class Penguin(Bird):
    def fly(self): raise NotImplementedError()  # يكسر LSP

Good:
class Bird(ABC):
    @abstractmethod
    def move(self): pass

class FlyingBird(Bird):
    def fly(self): pass

class Penguin(Bird):
    def walk(self): pass
```

### I — Interface Segregation

```
Bad (interface كبيرة):
interface Worker:
    def work(): ...
    def eat(): ...
    def sleep(): ...

Good (interfaces صغيرة):
interface Workable:
    def work(): ...

interface Eatable:
    def eat(): ...

class Robot(Workable):
    def work(self): ...

class Human(Workable, Eatable):
    def work(self): ...
    def eat(self): ...
```

### D — Dependency Inversion

```
Bad ( зависит على konkret):
class MySQLDatabase:
    def query(self): ...

class UserService:
    def __init__(self): self.db = MySQLDatabase()

Good (يعتمد على abstraction):
class Database(ABC):
    @abstractmethod
    def query(self, sql): pass

class MySQLDatabase(Database):
    def query(self, sql): ...

class UserService:
    def __init__(self, db: Database): self.db = db
```

---

## أنماط متقدمة

### Repository Pattern

```python
class UserRepository:
    def __init__(self, db: Database): self.db = db

    def get_by_id(self, id): ...
    def get_by_email(self, email): ...
    def create(self, user): ...
    def update(self, user): ...
    def delete(self, id): ...

# Usage
repo = UserRepository(MySQLDatabase())
user = repo.get_by_id(1)
```

### Unit of Work

```python
class UnitOfWork:
    def __init__(self, session):
        self.session = session
        self.users = UserRepository(session)
        self.orders = OrderRepository(session)

    def __enter__(self):
        return self

    def __exit__(self, *args):
        self.session.rollback() if args else self.session.commit()

    def commit(self):
        self.session.commit()

# Usage
with UnitOfWork(session) as uow:
    uow.users.create(user)
    uow.orders.create(order)
```

### Factory with DI

```python
class Container:
    _services = {}

    @classmethod
    def register(cls, interface, implementation):
        cls._services[interface] = implementation

    @classmethod
    def resolve(cls, interface):
        impl = cls._services[interface]
        return impl()

# Registration
Container.register(Database, MySQLDatabase)
Container.register(EmailService, SendGridEmail)

# Usage
db = Container.resolve(Database)
```

### Pipeline / Chain of Responsibility

```python
class Middleware:
    def __init__(self, nextMiddleware=None):
        self.next = nextMiddleware

    def process(self, request):
        if self.next:
            return self.next.process(request)
        return request

class AuthMiddleware(Middleware):
    def process(self, request):
        if not request.user:
            raise Unauthorized()
        return super().process(request)

class ValidationMiddleware(Middleware):
    def process(self, request):
        if not request.data:
            raise ValidationError()
        return super().process(request)

# Usage
pipeline = ValidationMiddleware(AuthMiddleware(Middleware()))
result = pipeline.process(request)
```

### Event-Driven Architecture

```python
class EventBus:
    _handlers = {}

    @classmethod
    def subscribe(cls, event, handler):
        cls._handlers.setdefault(event, []).append(handler)

    @classmethod
    def publish(cls, event, data):
        for handler in cls._handlers.get(event, []):
            handler(data)

# Usage
EventBus.subscribe("user.registered", send_welcome_email)
EventBus.subscribe("user.registered", create_default_dashboard)

def create_user(data):
    user = db.create(data)
    EventBus.publish("user.registered", user)
```

---

## ⏱️ الوقت المقدر: 3-4 أسابيع

## مصادر
- كتاب: *Design Patterns* - Gang of Four
- [Refactoring Guru](https://refactoring.guru/design-patterns) ← مجاني وممتاز بصريًا
