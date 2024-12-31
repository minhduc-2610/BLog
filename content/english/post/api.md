---
title: "Xây dựng API RESTful với Spring Boot"
date: 2024-12-30
tags: ["Spring Boot", "REST API", "Java"]
---

### Giới thiệu về API RESTful
API RESTful là một phong cách kiến trúc cho phép các hệ thống giao tiếp với nhau thông qua HTTP, sử dụng các phương thức như GET, POST, PUT và DELETE. RESTful API được thiết kế đơn giản và dễ dàng sử dụng, giúp kết nối giữa các hệ thống và ứng dụng web, di động.

Spring Boot là một framework rất mạnh mẽ để xây dựng API RESTful vì nó giúp giảm thiểu sự phức tạp trong việc cấu hình và triển khai ứng dụng. Dưới đây là cách bạn có thể xây dựng API RESTful đơn giản với Spring Boot.

### 1. Cấu hình Spring Boot cho RESTful API
Đầu tiên, bạn cần tạo một dự án Spring Boot với các phụ thuộc như `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, và một cơ sở dữ liệu như MySQL hoặc H2. Dưới đây là một ví dụ cấu hình trong file `application.properties`.

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
```
### 2. Tạo Entity và Repository
Tiếp theo, bạn cần tạo các entity và repository để tương tác với cơ sở dữ liệu. Dưới đây là ví dụ về một entity Product và repository ProductRepository.
```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    // Getters and Setters
}
```
```java
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```
### 3. Tạo Controller cho API RESTful
Tiếp theo, tạo một controller để xử lý các yêu cầu HTTP cho API RESTful. Sử dụng annotation @RestController để khai báo rằng đây là một RESTful controller và @RequestMapping để định nghĩa các endpoint.
```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductRepository productRepository;

    @GetMapping
    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }

    @GetMapping("/{id}")
    public Product getProductById(@PathVariable Long id) {
        return productRepository.findById(id).orElseThrow(() -> new RuntimeException("Product not found"));
    }

    @PostMapping
    public Product createProduct(@RequestBody Product product) {
        return productRepository.save(product);
    }

    @PutMapping("/{id}")
    public Product updateProduct(@PathVariable Long id, @RequestBody Product product) {
        Product existingProduct = productRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Product not found"));
        existingProduct.setName(product.getName());
        existingProduct.setPrice(product.getPrice());
        return productRepository.save(existingProduct);
    }

    @DeleteMapping("/{id}")
    public void deleteProduct(@PathVariable Long id) {
        Product existingProduct = productRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Product not found"));
        productRepository.delete(existingProduct);
    }
}
```
### 4. Phương thức HTTP trong API RESTful
Các phương thức HTTP được sử dụng trong API RESTful thường là:

- GET: Lấy thông tin từ server.
- POST: Tạo mới một tài nguyên.
- PUT: Cập nhật một tài nguyên.
- DELETE: Xóa một tài nguyên.
Trong ví dụ trên, chúng ta đã sử dụng các phương thức @GetMapping, @PostMapping, @PutMapping, và @DeleteMapping để xử lý các yêu cầu HTTP tương ứng.

### 5. Xử lý lỗi trong API
Trong khi xây dựng API RESTful, việc xử lý lỗi là rất quan trọng để cung cấp thông tin rõ ràng cho người dùng khi có sự cố. Bạn có thể sử dụng @ExceptionHandler để bắt và xử lý các lỗi.
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleException(RuntimeException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```
### 6. Kết nối với Frontend
Sau khi tạo API RESTful, bạn có thể kết nối với các frontend ứng dụng web hoặc di động. Để thực hiện các yêu cầu HTTP từ frontend, bạn có thể sử dụng các công cụ như axios trong JavaScript hoặc Retrofit trong Android.

**Ví dụ, để lấy tất cả các sản phẩm từ API trong JavaScript:**
```java
axios.get('http://localhost:8080/api/products')
    .then(response => {
        console.log(response.data);
    })
    .catch(error => {
        console.log('There was an error!', error);
    });
```
### 7. Kết luận
Xây dựng API RESTful với Spring Boot là một cách tuyệt vời để tạo các ứng dụng backend có khả năng mở rộng và dễ dàng giao tiếp với frontend. Spring Boot giúp giảm thiểu cấu hình và cung cấp các công cụ mạnh mẽ để xử lý các yêu cầu HTTP, kết nối cơ sở dữ liệu, và xây dựng các ứng dụng RESTful nhanh chóng và hiệu quả.