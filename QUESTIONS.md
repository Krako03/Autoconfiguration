# AutoConfiguration

Spring Boots brings to the table the AutoConfiguration feature, these
are normal Spring `@Configuration` annotations with a lot of `@Conditionals` 
(This conditionals will make that the specified beans will be added to your
project, only if certain conditions apply to your application).

## Questions 

### 1. What is the difference between regular configuration and autoconfiguration?
Regular configurations are specified by the user, one by one, while the autoconfiguration
are default configurations specified by Spring Boot. So depending on your program classes 
and properties the predefined beans will be created and initialized.

### 2. Would all conditional annotations on bean definitions work in regular configuration classes ? Elaborate.
Yes, as long as, they are in a `@Configuration` class, even if it is a custom configuration, the specialized annotations
will also work in your code.

### 3. How can we customize the autoconfiguration process?
Spring Boot brings default behavior for beans, but there are ways to change this, so they can fit better on the application requirements.

#### Exclusion
One way is to exclude the autoconfiguration classes, you can achieve this via properties:
```properties
spring.autoconfigure.exclude= \
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration, \
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

Or via annotations:
```java
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

And after excluding the default implementation, we should make our own. 
Also, we should know the name of the class we are omitting, looking at the Spring Boot source code.

#### Overriding
Since Spring Boot autoconfiguration works only if the bean is not created yet, 
so you cloud override the bean, specifying the same name as Spring Boot, so it takes yours instead of the default one.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CustomDataSourceConfig {

    @Bean
    public DataSource dataSource() {
        // Provide your custom DataSource instance instead of the autoconfigured one.
        return new CustomDataSource();
    }
}
```

There are another ways, but these are the ones I found more interesting.

### What is the condition that causes a tomcat server to start on port 8080 when the application starts?
If the application has on his dependencies `spring-boot-starter-web`, Spring Boot will assume the app is a 
web app. So the autoconfiguration will bring classes like `TomcatServletWebServerFactoryAutoConfiguration`.
These classes are annotated with `@Conditionals` so it will ensure they apply only when a proper servlet 
environment is detected and no other manual configuration is provided.
And for the port, it will check if there is an external configuration for the value `server.port` if not
it defaults it to 8080.