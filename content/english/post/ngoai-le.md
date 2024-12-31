---
title: "Các kỹ thuật xử lý ngoại lệ trong Java"
date: 2024-12-30
tags: ["Java", "Exception Handling"]
---

### Giới thiệu về xử lý ngoại lệ trong Java
Xử lý ngoại lệ (Exception Handling) là một phần quan trọng trong việc lập trình Java. Nó giúp chương trình có thể tiếp tục hoạt động mà không bị dừng đột ngột khi gặp lỗi. Java cung cấp các cơ chế mạnh mẽ để xử lý ngoại lệ thông qua các cấu trúc `try`, `catch`, `finally`, và `throw`.

### 1. Cấu trúc cơ bản của xử lý ngoại lệ

Trong Java, cấu trúc xử lý ngoại lệ cơ bản bao gồm ba phần: `try`, `catch`, và `finally`.

#### Cú pháp:

```java
try {
    // Mã có thể gây ra ngoại lệ
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // Xử lý ngoại lệ
    System.out.println("Lỗi chia cho 0: " + e.getMessage());
} finally {
    // Khối mã luôn thực thi, dù có ngoại lệ hay không
    System.out.println("Khối finally luôn được thực thi.");
}
```
**Giải thích:**
- try: Chứa mã có thể gây ra ngoại lệ.
- catch: Xử lý ngoại lệ nếu có.
- finally: Đảm bảo mã trong khối này luôn được thực thi, dù có ngoại lệ hay không.
### 2. Ném ngoại lệ (Throwing Exceptions)
Java cung cấp từ khóa throw để ném một ngoại lệ. Điều này rất hữu ích khi bạn muốn báo lỗi từ một phương thức.

**Cú pháp:**
```java
public class CustomExceptionExample {
    public static void checkAge(int age) {
        if (age < 18) {
            throw new IllegalArgumentException("Tuổi phải lớn hơn hoặc bằng 18.");
        } else {
            System.out.println("Tuổi hợp lệ.");
        }
    }

    public static void main(String[] args) {
        try {
            checkAge(15);
        } catch (IllegalArgumentException e) {
            System.out.println("Ngoại lệ: " + e.getMessage());
        }
    }
}
```
**Giải thích:**
- throw: Ném một ngoại lệ, thường là một đối tượng kế thừa từ lớp Throwable.
### 3. Xử lý nhiều ngoại lệ
Kể từ Java 7, bạn có thể xử lý nhiều ngoại lệ trong cùng một khối catch.

**Cú pháp:**
```java
try {
    int result = 10 / 0;
    String str = null;
    System.out.println(str.length());
} catch (ArithmeticException | NullPointerException e) {
    System.out.println("Ngoại lệ: " + e.getClass().getSimpleName());
}
```
**Giải thích:**
- Bạn có thể kết hợp nhiều loại ngoại lệ trong một câu lệnh catch để xử lý nhiều trường hợp lỗi một cách hiệu quả.
### 4. Tạo ngoại lệ tùy chỉnh (Custom Exceptions)
Đôi khi bạn cần tạo các ngoại lệ riêng cho ứng dụng của mình. Để làm điều này, bạn có thể tạo lớp kế thừa từ Exception hoặc RuntimeException.

**Cú pháp:**
```java
public class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

public class CustomExceptionExample {
    public static void checkAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Tuổi phải lớn hơn hoặc bằng 18.");
        }
    }

    public static void main(String[] args) {
        try {
            checkAge(16);
        } catch (InvalidAgeException e) {
            System.out.println("Ngoại lệ: " + e.getMessage());
        }
    }
}
```
**Giải thích:**
- InvalidAgeException là một ngoại lệ tùy chỉnh mà bạn có thể ném và xử lý trong chương trình.
### 5. Tóm tắt các loại ngoại lệ trong Java
- Checked exceptions: Các ngoại lệ được kiểm tra tại thời điểm biên dịch (ví dụ: IOException, SQLException).
- Unchecked exceptions: Các ngoại lệ không được kiểm tra tại thời điểm biên dịch (ví dụ: NullPointerException, ArrayIndexOutOfBoundsException).

### Kết luận
Xử lý ngoại lệ là một phần quan trọng trong lập trình Java, giúp chương trình của bạn tránh được các sự cố bất ngờ và tiếp tục hoạt động một cách mượt mà. Bằng cách hiểu và sử dụng các kỹ thuật xử lý ngoại lệ, bạn có thể làm cho ứng dụng của mình trở nên mạnh mẽ và đáng tin cậy hơn.