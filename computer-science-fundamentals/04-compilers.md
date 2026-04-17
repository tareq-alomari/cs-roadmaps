# 04 — المترجمات (Compilers)

---

## مراحل الترجمة

```
Source Code
    ↓
[Lexical Analysis]     → Tokens
    ↓
[Syntax Analysis]      → AST (Abstract Syntax Tree)
    ↓
[Semantic Analysis]    → Type checking, Scope
    ↓
[IR Generation]        → Intermediate Representation
    ↓
[Optimization]         → Dead code elimination, Inlining
    ↓
[Code Generation]      → Machine Code / Assembly
    ↓
Object File → [Linker] → Executable
```

---

## المفاهيم الأساسية

### Lexical Analysis (Tokenization)

```
الكود: int x = 42 + y;

Tokens:
[KEYWORD: int] [IDENT: x] [OP: =] [INT: 42] [OP: +] [IDENT: y] [SEMI: ;]

أداة: Flex (Lexer generator)
```

### Parsing (Syntax Analysis)

```
القواعد (Grammar):
stmt   → IDENT '=' expr ';'
expr   → expr '+' term | term
term   → INT | IDENT

الـ AST:
        ASSIGN
       /      \
      x       ADD
             /   \
           42     y

أداة: Bison/YACC (Parser generator)
```

### Optimization

```
تقنيات شائعة:
- Constant Folding:   2 + 3 → 5 (وقت الترجمة)
- Dead Code:          كود لا يُنفذ → حذف
- Inlining:           استبدال الدالة بمحتواها
- Loop Unrolling:     تكرار جسم الحلقة لتقليل الـ branching
- Register Allocation: تعيين المتغيرات لـ CPU Registers
```

---

## مقارنة: Compiled vs Interpreted vs JIT

```
Compiled (C, C++, Rust):
+ أسرع وقت تشغيل
- وقت بناء أطول
- مرتبط بالـ Platform

Interpreted (Python, Ruby):
+ مرونة، تنفيذ فوري
- أبطأ بكثير
+ Cross-platform

JIT (Java, C#, JavaScript V8):
+ يجمع الميزتين
+ يُحسّن الـ Hot paths في وقت التشغيل
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- كتاب: *Crafting Interpreters* - Robert Nystrom ← [مجاني](https://craftinginterpreters.com/)
- [CS143 - Stanford Compilers](https://web.stanford.edu/class/cs143/)
