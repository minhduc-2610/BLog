
---
title: "Lập trình đa luồng (Multithreading) trong Java"
date: 2024-12-30
tags: ["Java", "Threads"]
---

### Giới thiệu về Multithreading
Đa luồng (Multithreading) cho phép một chương trình thực thi nhiều luồng (thread) đồng thời, giúp tối ưu hóa hiệu suất.

### Cách tạo một luồng trong Java
1. **Sử dụng lớp `Thread`**  
   ```java
   public class MyThread extends Thread {
       public void run() {
           System.out.println("Hello from thread!");
       }

       public static void main(String[] args) {
           MyThread thread = new MyThread();
           thread.start();
       }
   }
   ```

2. **Sử dụng giao diện `Runnable`**  
   ```java
   public class MyRunnable implements Runnable {
       public void run() {
           System.out.println("Runnable thread running!");
       }

       public static void main(String[] args) {
           Thread thread = new Thread(new MyRunnable());
           thread.start();
       }
   }
   ```

### Đồng bộ hóa trong đa luồng
Đồng bộ hóa đảm bảo rằng chỉ một luồng được truy cập tài nguyên chia sẻ tại một thời điểm.
```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

### Kết luận
Multithreading là một tính năng mạnh mẽ trong Java, giúp tăng hiệu suất cho các ứng dụng lớn.
