# 02 — أنظمة التشغيل (OS Internals)

---

## المفاهيم الأساسية

### Process vs Thread

```
Process:
- برنامج قيد التشغيل
- له مساحة ذاكرة مستقلة
- التواصل بين Processes: IPC (Pipes, Sockets, Shared Memory)

Thread:
- وحدة تنفيذ داخل الـ Process
- يشارك الذاكرة مع باقي الـ Threads
- أسرع في الإنشاء من الـ Process

في Linux:
- كلاهما يُنشأ بـ clone() syscall
- الفرق في الـ Flags فقط
```

### Memory Management

```
Virtual Memory:
- كل Process يرى مساحة عنوان كاملة (4GB في 32-bit)
- الـ OS يترجم Virtual → Physical عبر الـ MMU

Stack vs Heap:
Stack: تلقائي، سريع، محدود الحجم
       ← Local variables, Function calls
Heap:  يدوي (malloc/free)، بطيء، كبير
       ← Dynamic allocation

Page Fault:
- الـ Process يطلب صفحة غير موجودة في الـ RAM
- الـ OS يجلبها من الـ Disk (Swap)
```

### Scheduling

```
الخوارزميات:
FCFS    ← First Come First Served (بسيط، غير عادل)
SJF     ← Shortest Job First (مثالي نظريًا)
Round Robin ← كل Process يأخذ Time Quantum
Priority ← الأعلى أولوية أولًا
CFS     ← Completely Fair Scheduler (Linux الحديث)

Context Switch:
- حفظ حالة الـ Process الحالي
- تحميل حالة الـ Process التالي
- مكلف! لذا نقلل منه
```

### Synchronization

```c
// Race Condition
int counter = 0;
// Thread 1: counter++
// Thread 2: counter++
// النتيجة قد تكون 1 بدلًا من 2!

// الحل: Mutex
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
counter++;
pthread_mutex_unlock(&lock);

// Deadlock:
// Thread A يحمل Lock1 وينتظر Lock2
// Thread B يحمل Lock2 وينتظر Lock1
// → توقف تام!

// الحل: ترتيب ثابت لاكتساب الـ Locks
```

### File System

```
VFS (Virtual File System):
- طبقة تجريد فوق أنظمة الملفات المختلفة
- ext4, NTFS, FAT32, APFS كلها تبدو متشابهة للتطبيق

Inode:
- يخزن metadata الملف (الحجم، الصلاحيات، الوقت)
- لا يخزن اسم الملف!
- الاسم مخزن في الـ Directory entry

Journaling:
- تسجيل العمليات قبل تنفيذها
- يضمن الاتساق عند انقطاع الكهرباء
```

---

## System Calls

```c
// التطبيق يتواصل مع الـ Kernel عبر Syscalls
#include <unistd.h>
#include <fcntl.h>

int fd = open("file.txt", O_RDONLY);    // syscall: open
char buf[1024];
read(fd, buf, sizeof(buf));              // syscall: read
write(STDOUT_FILENO, buf, strlen(buf)); // syscall: write
close(fd);                               // syscall: close

// strace لرؤية الـ Syscalls
// strace ./myprogram
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- كتاب: *Operating Systems: Three Easy Pieces* ← [مجاني](https://pages.cs.wisc.edu/~remzi/OSTEP/)
- [CS162 - Berkeley OS Course](https://cs162.org/)
