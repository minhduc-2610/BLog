---
title: "Tối ưu hóa ứng dụng Spring Boot với Caching"
date: 2024-12-30
tags: ["Spring Boot", "Caching", "Performance"]
---

### Giới thiệu về Caching
Caching là một kỹ thuật tối ưu hóa hiệu suất giúp giảm thiểu thời gian xử lý bằng cách lưu trữ kết quả của các thao tác tính toán hoặc truy vấn vào bộ nhớ tạm thời, giúp tiết kiệm tài nguyên và thời gian cho các lần truy vấn tiếp theo. Spring Boot cung cấp một giải pháp caching tích hợp sẵn giúp dễ dàng cải thiện hiệu suất ứng dụng.

Trong bài viết này, chúng ta sẽ tìm hiểu cách cấu hình và sử dụng caching trong Spring Boot để tối ưu hóa hiệu suất ứng dụng của bạn.

### 1. Cấu hình Caching trong Spring Boot
Để sử dụng caching trong Spring Boot, bạn cần thêm một số phụ thuộc vào file `pom.xml` (nếu sử dụng Maven) hoặc `build.gradle` (nếu sử dụng Gradle).

**Thêm phụ thuộc trong `pom.xml`**:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```
**Hoặc trong** build.gradle:
```groovy
implementation 'org.springframework.boot:spring-boot-starter-cache'
```
Sau khi thêm phụ thuộc, bạn cần bật tính năng caching bằng cách sử dụng annotation @EnableCaching trong class cấu hình chính của ứng dụng.
```java
@Configuration
@EnableCaching
public class CacheConfig {
}
```
### 2. Sử dụng Cache với Annotation @Cacheable
Spring cung cấp một cách rất đơn giản để sử dụng caching với annotation @Cacheable. Khi áp dụng annotation này vào một phương thức, Spring sẽ lưu kết quả của phương thức vào bộ nhớ cache và trả lại kết quả từ cache nếu phương thức được gọi lại với cùng một tham số.

Dưới đây là một ví dụ về việc sử dụng @Cacheable:
```java
@Service
public class ProductService {

    @Cacheable("products")
    public Product getProductById(Long id) {
        // Giả sử đây là một truy vấn tốn thời gian
        simulateLongQuery();
        return productRepository.findById(id).orElseThrow(() -> new RuntimeException("Product not found"));
    }

    private void simulateLongQuery() {
        try {
            Thread.sleep(2000); // Giả lập truy vấn mất 2 giây
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
Trong ví dụ trên, khi phương thức getProductById được gọi với cùng một tham số id, Spring sẽ trả lại kết quả từ bộ nhớ cache thay vì thực hiện lại truy vấn cơ sở dữ liệu.

### 3. Cấu hình Cache
Mặc định, Spring Boot sử dụng bộ nhớ cache trong bộ nhớ (In-memory cache). Tuy nhiên, bạn có thể cấu hình cache để sử dụng các giải pháp như Redis hoặc EhCache.

**Sử dụng Redis làm Cache Store**
Để cấu hình Redis, bạn cần thêm phụ thuộc Redis vào file pom.xml:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
Sau đó, cấu hình kết nối Redis trong application.properties:
```properties
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
```
**Sử dụng EhCache**
EhCache là một bộ nhớ cache phổ biến khác. Để sử dụng EhCache, bạn cần thêm phụ thuộc sau vào pom.xml:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```
Sau đó, cấu hình EhCache trong application.properties:
```properties
spring.cache.type=ehcache
```
### 4. Cập nhật Cache với @CachePut
Nếu bạn muốn cập nhật cache sau khi thực hiện một thao tác thay đổi dữ liệu, bạn có thể sử dụng annotation @CachePut. Annotation này đảm bảo rằng kết quả mới của phương thức được lưu vào cache.

Ví dụ:
```java
@CachePut(value = "products", key = "#product.id")
public Product updateProduct(Product product) {
    return productRepository.save(product);
}
```
Trong ví dụ trên, sau khi cập nhật thông tin sản phẩm, cache sẽ được làm mới với kết quả mới của phương thức updateProduct.

### 5. Xóa Cache với @CacheEvict
Nếu bạn muốn xóa một phần tử trong cache, bạn có thể sử dụng annotation @CacheEvict. Điều này rất hữu ích khi dữ liệu trong cache bị thay đổi hoặc bị xóa khỏi cơ sở dữ liệu.

Ví dụ:
```java
@CacheEvict(value = "products", key = "#id")
public void deleteProduct(Long id) {
    productRepository.deleteById(id);
}
```
Trong ví dụ này, sau khi sản phẩm bị xóa, Spring sẽ tự động xóa kết quả tương ứng trong cache.

### 6. Cách kiểm tra và tối ưu hóa Cache
Để tối ưu hóa việc sử dụng cache, bạn cần đảm bảo rằng các kết quả cache được tái sử dụng hiệu quả. Việc lựa chọn đúng đối tượng để cache và thiết lập thời gian sống (TTL) của cache sẽ giúp cải thiện hiệu suất.

Dưới đây là một ví dụ cấu hình TTL cho Redis cache trong application.properties:
```properties
spring.cache.redis.time-to-live=60000
```
Điều này sẽ đảm bảo rằng dữ liệu trong cache sẽ hết hạn sau 60 giây.

### 7. Kết luận
Caching là một phương pháp rất hiệu quả để tối ưu hóa hiệu suất của ứng dụng, đặc biệt là với Spring Boot. Với các annotation như @Cacheable, @CachePut và @CacheEvict, việc tích hợp caching vào ứng dụng Spring Boot trở nên rất đơn giản. Bằng cách sử dụng các công nghệ caching như Redis và EhCache, bạn có thể cải thiện đáng kể thời gian phản hồi và giảm tải cho cơ sở dữ liệu.