---
title: "Sử dụng Spring Security trong ứng dụng Spring Boot"
date: 2024-12-30
tags: ["Spring Boot", "Spring Security", "Authentication", "Authorization"]
---

### Giới thiệu về Spring Security
Spring Security là một framework bảo mật mạnh mẽ cho các ứng dụng Java, đặc biệt là các ứng dụng Spring Boot. Nó cung cấp các tính năng bảo mật quan trọng như xác thực (authentication), phân quyền (authorization), và bảo vệ khỏi các mối đe dọa như CSRF, XSS.

Trong bài viết này, chúng ta sẽ tìm hiểu cách cấu hình Spring Security trong ứng dụng Spring Boot để bảo mật ứng dụng của bạn.

### 1. Cài đặt Spring Security trong Spring Boot
Để bắt đầu sử dụng Spring Security, bạn cần thêm phụ thuộc `spring-boot-starter-security` vào tệp `pom.xml` của dự án.

#### Ví dụ cài đặt trong `pom.xml`:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <!-- Các phụ thuộc khác -->
</dependencies>
```
Khi bạn thêm phụ thuộc này, Spring Boot sẽ tự động cấu hình một số tính năng bảo mật mặc định cho ứng dụng của bạn, chẳng hạn như bảo vệ tất cả các API bằng cách yêu cầu xác thực.

### 2. Cấu hình Spring Security
Spring Security cung cấp khả năng tùy chỉnh các cấu hình bảo mật. Bạn có thể tạo một lớp cấu hình để xác định các quy tắc bảo mật, ví dụ như những trang nào được phép truy cập công khai, những trang nào yêu cầu quyền truy cập đặc biệt.

**Ví dụ cấu hình cơ bản:**
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/public/**").permitAll()  // Cho phép truy cập công khai
                .anyRequest().authenticated()         // Yêu cầu xác thực cho các trang còn lại
            .and()
            .formLogin()  // Sử dụng trang đăng nhập mặc định
            .permitAll()
            .and()
            .logout()     // Cho phép người dùng đăng xuất
            .permitAll();
    }
}
```
- antMatchers("/public/**").permitAll(): Cho phép truy cập công khai tới các URL bắt đầu bằng /public/.
- anyRequest().authenticated(): Yêu cầu xác thực cho tất cả các yêu cầu khác.
- formLogin(): Sử dụng trang đăng nhập mặc định của Spring Security.
- logout(): Cho phép người dùng đăng xuất.
### 3. Xác thực người dùng với Spring Security
Spring Security hỗ trợ nhiều phương thức xác thực người dùng. Một cách phổ biến là sử dụng xác thực qua cơ sở dữ liệu hoặc bộ nhớ.

**Ví dụ cấu hình xác thực qua bộ nhớ:**
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.inmemory.InMemoryUserDetailsManager;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user").password("{noop}password").roles("USER")
            .and()
            .withUser("admin").password("{noop}admin").roles("ADMIN");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/user/**").hasRole("USER")
                .anyRequest().authenticated()
            .and()
            .formLogin()
            .permitAll()
            .and()
            .logout()
            .permitAll();
    }
}
```
- withUser("user"): Tạo người dùng với tên là "user" và mật khẩu là "password".
- roles("USER"): Gán vai trò "USER" cho người dùng.
- antMatchers("/admin/**").hasRole("ADMIN"): Chỉ cho phép người dùng có vai trò "ADMIN" truy cập vào các URL bắt đầu bằng /admin/.
### 4. Phân quyền người dùng
Trong Spring Security, bạn có thể phân quyền cho người dùng dựa trên vai trò của họ.

**Ví dụ phân quyền cho các vai trò:**
- Người dùng có vai trò "USER" chỉ có quyền truy cập vào các trang thông thường.
- Người dùng có vai trò "ADMIN" có quyền truy cập vào các trang quản trị.
### 5. Bảo mật API RESTful với Spring Security
Spring Security cũng hỗ trợ bảo mật cho các API RESTful. Bạn có thể sử dụng JWT (JSON Web Token) để xác thực người dùng mà không cần sử dụng session.

**Ví dụ bảo mật API với JWT:**
- Tạo lớp JwtAuthenticationFilter để kiểm tra JWT trong header của mỗi yêu cầu.
- Cấu hình Spring Security để chỉ cho phép truy cập API khi có JWT hợp lệ.
### 6. Kết luận
Spring Security là một công cụ tuyệt vời để bảo mật ứng dụng Spring Boot của bạn. Bằng cách sử dụng các tính năng như xác thực, phân quyền, và bảo vệ khỏi các mối đe dọa, bạn có thể xây dựng các ứng dụng bảo mật cao và dễ dàng mở rộng. Các cấu hình linh hoạt của Spring Security giúp bạn tùy chỉnh bảo mật theo yêu cầu của từng ứng dụng.