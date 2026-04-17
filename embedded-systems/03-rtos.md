# 03 — RTOS (Real-Time Operating System)

---

## ما هو RTOS؟

```
نظام تشغيل مصمم للاستجابة في وقت محدد ومضمون.

Hard Real-Time: الفشل في الوقت = كارثة (Airbag, Pacemaker)
Soft Real-Time: التأخير مقبول أحيانًا (Video streaming)

أشهر الأنظمة:
- FreeRTOS  ← الأكثر انتشارًا، مجاني
- Zephyr    ← Linux Foundation، حديث
- VxWorks   ← صناعي، مدفوع
- QNX       ← للسيارات والطيران
```

---

## FreeRTOS

```c
#include "FreeRTOS.h"
#include "task.h"
#include "queue.h"
#include "semphr.h"

// Tasks
void vSensorTask(void *pvParameters) {
    for (;;) {
        float temp = readSensor();
        xQueueSend(xTempQueue, &temp, portMAX_DELAY);
        vTaskDelay(pdMS_TO_TICKS(1000));  // 1 ثانية
    }
}

void vDisplayTask(void *pvParameters) {
    float temp;
    for (;;) {
        if (xQueueReceive(xTempQueue, &temp, portMAX_DELAY)) {
            displayTemperature(temp);
        }
    }
}

// Mutex للـ Shared Resources
SemaphoreHandle_t xMutex = xSemaphoreCreateMutex();

void accessSharedResource() {
    if (xSemaphoreTake(xMutex, pdMS_TO_TICKS(100))) {
        // Critical section
        xSemaphoreGive(xMutex);
    }
}

int main() {
    xTempQueue = xQueueCreate(10, sizeof(float));
    xTaskCreate(vSensorTask, "Sensor", 256, NULL, 2, NULL);
    xTaskCreate(vDisplayTask, "Display", 256, NULL, 1, NULL);
    vTaskStartScheduler();
}
```

---

## مفاهيم RTOS

```
Task/Thread    ← وحدة التنفيذ المستقلة
Scheduler      ← يقرر أي Task يعمل الآن
Priority       ← الأعلى أولوية يعمل أولًا
Preemption     ← Task ذو أولوية أعلى يقاطع الأدنى
Queue          ← تمرير البيانات بين Tasks
Semaphore      ← التزامن بين Tasks
Mutex          ← حماية الـ Shared Resources
Watchdog Timer ← إعادة تشغيل النظام عند التجمد
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [FreeRTOS Documentation](https://www.freertos.org/Documentation/)
- [Mastering the FreeRTOS Kernel](https://www.freertos.org/Documentation/RTOS_book.html) ← مجاني
