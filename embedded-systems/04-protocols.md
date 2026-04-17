# 04 — بروتوكولات الاتصال

---

## البروتوكولات الأساسية

### UART (Universal Asynchronous Receiver-Transmitter)

```c
// أبسط بروتوكول — نقطة لنقطة
// TX → RX, RX ← TX, GND
// لا يحتاج Clock مشترك

// STM32
HAL_UART_Transmit(&huart1, data, len, timeout);
HAL_UART_Receive(&huart1, buffer, len, timeout);

// الإعدادات: Baud Rate, Data bits, Stop bits, Parity
// مثال: 115200, 8N1
```

### SPI (Serial Peripheral Interface)

```c
// سريع، 4 أسلاك: MOSI, MISO, SCK, CS
// Master-Slave، يدعم أجهزة متعددة

HAL_SPI_Transmit(&hspi1, txData, size, timeout);
HAL_SPI_Receive(&hspi1, rxData, size, timeout);
HAL_SPI_TransmitReceive(&hspi1, txData, rxData, size, timeout);

// استخدامات: SD Cards, Flash Memory, Display screens
```

### I2C (Inter-Integrated Circuit)

```c
// سلكان فقط: SDA, SCL
// يدعم 127 جهاز على نفس الـ Bus
// أبطأ من SPI

// كتابة
HAL_I2C_Master_Transmit(&hi2c1, DEVICE_ADDR << 1, data, len, timeout);

// قراءة
HAL_I2C_Master_Receive(&hi2c1, DEVICE_ADDR << 1, buffer, len, timeout);

// استخدامات: Sensors, EEPROM, RTC, OLED displays
```

### CAN Bus (للسيارات والصناعة)

```c
// مقاوم للضوضاء، موثوق جدًا
// يستخدم في السيارات (OBD-II) والروبوتات

CAN_TxHeaderTypeDef txHeader = {
    .StdId = 0x123,
    .DLC = 8,
    .IDE = CAN_ID_STD,
    .RTR = CAN_RTR_DATA,
};
uint8_t data[8] = {0x01, 0x02, 0x03};
HAL_CAN_AddTxMessage(&hcan1, &txHeader, data, &txMailbox);
```

---

## بروتوكولات IoT

```
MQTT    ← Publish/Subscribe، خفيف، للـ IoT
CoAP    ← مثل HTTP لكن للأجهزة المحدودة
Modbus  ← للصناعة (RTU/TCP)
Zigbee  ← شبكة Mesh لاسلكية، طاقة منخفضة
LoRaWAN ← مسافات طويلة، طاقة منخفضة جدًا
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [I2C, SPI, UART Explained - Circuit Basics](https://www.circuitbasics.com/)
- [MQTT Documentation](https://mqtt.org/)
