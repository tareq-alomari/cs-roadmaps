# 03 — معمارية الحاسوب (Computer Architecture)

---

## المعالج (CPU)

### دورة الأوامر (Instruction Cycle)

```
Fetch → Decode → Execute → Write Back

1. Fetch:      جلب الأمر من الذاكرة (عنوانه في PC/RIP)
2. Decode:     فهم الأمر (ما العملية؟ ما العوامل؟)
3. Execute:    تنفيذ العملية في الـ ALU
4. Write Back: كتابة النتيجة في الـ Register أو الذاكرة
```

### Pipelining

```
بدون Pipeline:          مع Pipeline:
F D E W                 F D E W
    F D E W             F D E W ←  4 أضعاف الإنتاجية
        F D E W         F D E W
            F D E W     F D E W

Hazards (مشاكل):
- Data Hazard:    أمر يحتاج نتيجة أمر لم ينتهِ
- Control Hazard: Branch instructions
- Structural:     تعارض على نفس المورد
```

### Cache Memory

```
الهرمية:
L1 Cache: ~32KB,  ~1ns  (داخل كل Core)
L2 Cache: ~256KB, ~4ns  (داخل كل Core)
L3 Cache: ~8MB,   ~10ns (مشترك بين Cores)
RAM:      ~GBs,   ~100ns
SSD:      ~TBs,   ~100μs
HDD:      ~TBs,   ~10ms

Cache Miss:
- Compulsory: أول مرة نطلب البيانات
- Capacity:   الـ Cache ممتلئ
- Conflict:   Mapping تعارض
```

### RISC vs CISC

```
RISC (ARM, RISC-V):
- أوامر بسيطة وكثيرة
- كل أمر بنفس الطول
- Load/Store architecture
- الموبايل والـ IoT

CISC (x86):
- أوامر معقدة ومتغيرة الطول
- الـ PC والـ Server
```

---

## الذاكرة

```
RAM (Random Access Memory):
- DRAM: رخيص، بطيء، يحتاج Refresh
- SRAM: غالي، سريع، لا يحتاج Refresh (الـ Cache)

Virtual Memory:
- كل Process يرى 0 إلى 2^64
- الـ MMU تترجم إلى Physical Address
- TLB: Cache لعمليات الترجمة
```

---

## I/O

```
Polling:     الـ CPU يتحقق باستمرار (مُضيع للوقت)
Interrupts:  الجهاز يُنبه الـ CPU عند الجاهزية (أفضل)
DMA:         نقل البيانات بدون تدخل الـ CPU (الأفضل للبيانات الكبيرة)
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- كتاب: *Computer Organization and Design* - Patterson & Hennessy
- [CS61C - Berkeley](https://cs61c.org/) ← مجاني
- [Nand to Tetris](https://www.nand2tetris.org/) ← بناء حاسوب من الصفر
