# 02 — Microcontrollers

---

## المنصات الشائعة

| المنصة | المعالج | الاستخدام |
|--------|---------|-----------|
| Arduino Uno | ATmega328P | التعلم والنماذج الأولية |
| ESP32 | Xtensa LX6 | IoT + Wi-Fi + Bluetooth |
| STM32 | ARM Cortex-M | الصناعة والمنتجات |
| Raspberry Pi Pico | RP2040 | مشاريع متوسطة |

---

## Arduino — للبداية

```cpp
// Blink LED
const int LED_PIN = 13;

void setup() {
    pinMode(LED_PIN, OUTPUT);
    Serial.begin(9600);
}

void loop() {
    digitalWrite(LED_PIN, HIGH);
    delay(1000);
    digitalWrite(LED_PIN, LOW);
    delay(1000);
}

// قراءة مستشعر درجة الحرارة (DHT22)
#include <DHT.h>
DHT dht(2, DHT22);

void setup() { dht.begin(); }

void loop() {
    float temp = dht.readTemperature();
    float humidity = dht.readHumidity();
    Serial.printf("Temp: %.1f°C, Humidity: %.1f%%\n", temp, humidity);
    delay(2000);
}
```

---

## STM32 — للاحتراف

```c
// HAL Library (STM32CubeIDE)
#include "stm32f4xx_hal.h"

// GPIO Output
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);

// UART
uint8_t msg[] = "Hello STM32\r\n";
HAL_UART_Transmit(&huart2, msg, sizeof(msg), HAL_MAX_DELAY);

// ADC
HAL_ADC_Start(&hadc1);
HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY);
uint32_t value = HAL_ADC_GetValue(&hadc1);

// Timer PWM
__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, 500);  // 50% duty cycle
```

---

## ESP32 — للـ IoT

```cpp
#include <WiFi.h>
#include <PubSubClient.h>  // MQTT

WiFiClient espClient;
PubSubClient mqtt(espClient);

void setup() {
    WiFi.begin("SSID", "PASSWORD");
    while (WiFi.status() != WL_CONNECTED) delay(500);

    mqtt.setServer("broker.hivemq.com", 1883);
    mqtt.connect("ESP32_Client");
}

void loop() {
    float temp = readTemperature();
    String payload = "{\"temp\":" + String(temp) + "}";
    mqtt.publish("home/sensors/temp", payload.c_str());
    delay(5000);
}
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [Arduino Documentation](https://docs.arduino.cc/)
- [ESP-IDF Documentation](https://docs.espressif.com/projects/esp-idf/)
- [STM32 Tutorial - Controllers Tech](https://controllerstech.com/)
