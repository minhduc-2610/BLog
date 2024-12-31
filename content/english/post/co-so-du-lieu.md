---
title: "Làm việc với Cơ sở dữ liệu trong Spring Boot"
date: 2024-12-30
tags: ["Spring Boot", "Database"]
---

### Giới thiệu về làm việc với cơ sở dữ liệu trong Spring Boot
Spring Boot là một framework mạnh mẽ giúp xây dựng các ứng dụng Java một cách nhanh chóng và dễ dàng. Trong bài viết này, chúng ta sẽ tìm hiểu cách làm việc với cơ sở dữ liệu trong Spring Boot, từ việc kết nối cơ sở dữ liệu đến việc thực hiện các thao tác CRUD (Tạo, Đọc, Cập nhật, Xóa) với cơ sở dữ liệu.

### 1. Cấu hình kết nối cơ sở dữ liệu trong Spring Boot
Spring Boot hỗ trợ nhiều loại cơ sở dữ liệu như MySQL, PostgreSQL, H2, và nhiều hơn nữa. Để kết nối với một cơ sở dữ liệu, bạn cần cấu hình các thông tin kết nối trong tệp `application.properties` hoặc `application.yml`.

#### Ví dụ với MySQL:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/your_database
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect
spring.jpa.hibernate.ddl-auto=update
```
- spring.datasource.url: URL kết nối đến cơ sở dữ liệu.
- spring.datasource.username: Tên người dùng của cơ sở dữ liệu.
- spring.datasource.password: Mật khẩu của người dùng cơ sở dữ liệu.
- spring.jpa.hibernate.ddl-auto: Quy định cách Hibernate tạo bảng trong cơ sở dữ liệu (update, create, create-drop, validate).
### 2. Tạo Entity trong Spring Boot
Entity là các lớp Java đại diện cho bảng trong cơ sở dữ liệu. Mỗi thuộc tính của lớp Entity tương ứng với một cột trong bảng.

**Ví dụ với Entity** Product:
```java
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Product {

    @Id
    private Long id;
    private String name;
    private double price;

    // Getters và setters
}
```
- @Entity: Đánh dấu lớp này là một entity.
- @Id: Đánh dấu thuộc tính là khóa chính (Primary Key) của bảng.
### 3. Tạo Repository
Repository trong Spring Boot giúp bạn thực hiện các thao tác CRUD dễ dàng mà không cần viết mã SQL. Spring Data JPA cung cấp các giao diện (interfaces) mà bạn có thể kế thừa để thao tác với cơ sở dữ liệu.

**Ví dụ với** ProductRepository:
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface ProductRepository extends JpaRepository<Product, Long> {
    // Các phương thức CRUD được tạo tự động
}
```
- JpaRepository: Cung cấp các phương thức CRUD cơ bản, như save(), findById(), findAll(), deleteById().
### 4. Tạo Service để xử lý nghiệp vụ
Service là nơi bạn đặt các logic nghiệp vụ của ứng dụng. Bạn có thể sử dụng các repository trong service để thực hiện các thao tác với cơ sở dữ liệu.

**Ví dụ với** ProductService:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public Product createProduct(Product product) {
        return productRepository.save(product);
    }

    public Product getProductById(Long id) {
        return productRepository.findById(id).orElse(null);
    }

    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }

    public Iterable<Product> getAllProducts() {
        return productRepository.findAll();
    }
}
```
- @Service: Đánh dấu lớp này là một service.
- @Autowired: Tiêm phụ thuộc repository vào service.
### 5. Tạo Controller để xử lý yêu cầu HTTP
Controller là nơi bạn xử lý các yêu cầu HTTP từ phía người dùng và trả về kết quả. Trong Spring Boot, bạn có thể sử dụng @RestController để xây dựng các API RESTful.

**Ví dụ với** ProductController:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @PostMapping
    public Product createProduct(@RequestBody Product product) {
        return productService.createProduct(product);
    }

    @GetMapping("/{id}")
    public Product getProduct(@PathVariable Long id) {
        return productService.getProductById(id);
    }

    @GetMapping
    public Iterable<Product> getAllProducts() {
        return productService.getAllProducts();
    }

    @DeleteMapping("/{id}")
    public void deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
    }
}
```
- @RestController: Đánh dấu lớp này là controller RESTful.
- @RequestMapping: Định nghĩa URL của controller.
- @PostMapping, @GetMapping, @DeleteMapping: Đánh dấu các phương thức xử lý các yêu cầu HTTP tương ứng.
### 6. Tóm tắt các thao tác CRUD cơ bản trong Spring Boot
- Tạo mới: Sử dụng POST để thêm sản phẩm mới.
- Đọc: Sử dụng GET để lấy sản phẩm theo ID hoặc tất cả sản phẩm.
- Cập nhật: Sử dụng PUT hoặc PATCH để cập nhật thông tin sản phẩm.
- Xóa: Sử dụng DELETE để xóa sản phẩm.
### Kết luận
Spring Boot cung cấp các công cụ mạnh mẽ và dễ sử dụng để làm việc với cơ sở dữ liệu. Bằng cách kết hợp các tính năng như Spring Data JPA, Repository, Service và Controller, bạn có thể xây dựng các ứng dụng quản lý cơ sở dữ liệu một cách nhanh chóng và hiệu quả.