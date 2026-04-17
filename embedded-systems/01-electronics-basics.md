# 01 — الإلكترونيات الأساسية

---

## المفاهيم الأساسية

```
المكونات الرئيسية:
- Resistor  ← مقاومة التيار
- Capacitor ← تخزين الطاقة مؤقتًا
- Inductor  ← تخزين الطاقة مغناطيسيًا
- Diode     ← تدفق التيار باتجاه واحد
- Transistor ← تضخيم أو مفتاح (BJT, MOSFET)
- Op-Amp    ← تضخيم الإشارة

قوانين أساسية:
- Ohm's Law: V = I × R
- Kirchhoff's Voltage Law (KVL)
- Kirchhoff's Current Law (KCL)
```

---

## الإشارات الرقمية والتماثلية

```
Analog Signal  ← قيم مستمرة (0V إلى 5V)
Digital Signal ← قيمتان فقط (0 أو 1, LOW أو HIGH)

ADC: تحويل Analog → Digital (للمستشعرات)
DAC: تحويل Digital → Analog (للمكبرات الصوتية)

PWM (Pulse Width Modulation):
- محاكاة الـ Analog بالـ Digital
- التحكم في سرعة المحركات وسطوع LED
```

---

## أدوات القياس

```
Multimeter     ← قياس الفولتية والتيار والمقاومة
Oscilloscope   ← رؤية الإشارات بصريًا عبر الزمن
Logic Analyzer ← تحليل الإشارات الرقمية (SPI, I2C, UART)
Power Supply   ← مصدر طاقة قابل للتعديل
```

---

## C للـ Embedded

```c
#include <stdint.h>
#include <stdbool.h>

// Bit Manipulation — الأكثر استخدامًا في Embedded
uint8_t reg = 0b00000000;

// Set bit 3
reg |= (1 << 3);          // 0b00001000

// Clear bit 3
reg &= ~(1 << 3);         // 0b00000000

// Toggle bit 3
reg ^= (1 << 3);          // 0b00001000

// Check bit 3
bool is_set = (reg >> 3) & 1;

// Volatile — مهم جدًا للـ Hardware Registers
volatile uint32_t *gpio_reg = (volatile uint32_t *)0x40020000;
*gpio_reg |= (1 << 5);   // Set GPIO pin 5
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [Ben Eater (YouTube)](https://www.youtube.com/@BenEater) ← بناء حاسوب من الصفر
- [All About Circuits](https://www.allaboutcircuits.com/) ← مجاني وشامل
