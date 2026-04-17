# 03 — Deep Learning

---

## Neural Networks الأساس

```python
import torch
import torch.nn as nn

class NeuralNet(nn.Module):
    def __init__(self):
        super().__init__()
        self.layers = nn.Sequential(
            nn.Linear(784, 256),
            nn.ReLU(),
            nn.Dropout(0.2),
            nn.Linear(256, 10)
        )

    def forward(self, x):
        return self.layers(x)

model = NeuralNet()
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
criterion = nn.CrossEntropyLoss()

# Training loop
for epoch in range(10):
    for X, y in dataloader:
        optimizer.zero_grad()
        loss = criterion(model(X), y)
        loss.backward()   # Backpropagation
        optimizer.step()
```

---

## الأنواع الرئيسية

### CNN (Convolutional Neural Networks)

```python
# للصور
class CNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=3),
            nn.ReLU(),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 64, kernel_size=3),
            nn.ReLU(),
            nn.AdaptiveAvgPool2d(1)
        )
        self.fc = nn.Linear(64, 10)

    def forward(self, x):
        return self.fc(self.conv(x).squeeze())
```

### RNN / LSTM

```python
# للتسلسلات والنصوص
lstm = nn.LSTM(input_size=100, hidden_size=256, num_layers=2, batch_first=True)
```

### Transformer

```
الأساس في:
- GPT, BERT, LLaMA (NLP)
- Vision Transformer (Computer Vision)
- الـ Architecture المهيمنة حاليًا

المفاهيم:
- Self-Attention
- Multi-head Attention
- Positional Encoding
- Encoder-Decoder
```

---

## Transfer Learning

```python
# استخدام نموذج مدرب مسبقًا
from torchvision.models import resnet50, ResNet50_Weights

model = resnet50(weights=ResNet50_Weights.DEFAULT)

# Freeze الطبقات القديمة
for param in model.parameters():
    param.requires_grad = False

# استبدال الطبقة الأخيرة فقط
model.fc = nn.Linear(2048, num_classes)
# ثم Train على بياناتك فقط
```

---

## Frameworks

```
PyTorch   ← البحث والإنتاج (الأكثر انتشارًا حاليًا)
TensorFlow/Keras ← الإنتاج، خاصة Mobile
JAX       ← البحث المتقدم (Google)
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [fast.ai](https://www.fast.ai/) ← أفضل دورة عملية، مجانية
- [CS231n - Stanford](http://cs231n.stanford.edu/) ← CNN
- [Andrej Karpathy YouTube](https://www.youtube.com/@AndrejKarpathy) ← الأفضل للـ LLMs
