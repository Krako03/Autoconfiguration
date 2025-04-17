# Debugging and Customizing Auto Configuration in Spring Boot

## 1. Debugging Auto Configuration
There is a way to get a detailed report of the autoconfiguration 
made by Spring Boot, by enabling the debug mode.
This will report to the console which autoconfiguration classes were 
evaluated, which conditions passed or failed, and why some candidates 
were not applied.

This could be done via `application.properties`:
  ```properties
  debug=true
```

Or via command line 
```bash
# With maven
mvn spring-boot:run -Dspring-boot.run.arguments="--debug"
# or with java -jar
java -jar myapp.jar --debug
```

This will output report on details such as:
- **Candidate Autoconfigurations**
  - A list of all the autoconfiguration classes that Spring Boot considered.
    <img width="1415" alt="Screenshot 2025-04-16 at 3 00 41 p m" src="https://github.com/user-attachments/assets/a464dc99-2b54-4296-bcd8-8f4ee6803fff" />

- **Conditions Evaluation**
  - For each candidate, the report shows which conditions were met or failed.
    <img width="1023" alt="Screenshot 2025-04-16 at 3 01 03 p m" src="https://github.com/user-attachments/assets/89e0915c-3e33-42ae-b4fd-75999cc25ecc" />

- **Exclusions and Overrides**
  - Information about configurations that were excluded or overridden by custom user configuration.
    <img width="153" alt="Screenshot 2025-04-16 at 3 01 42 p m" src="https://github.com/user-attachments/assets/e38ab68e-f6cf-4ef3-8f89-03bbffdd381f" />

## 2. Disabling Auto Configuration
There are several ways to disable some autoconfiguration classes, like setting on the .properties file:

```properties
spring.autoconfigure.exclude=\
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

Or by annotating our configuration class like:
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

But, if we want to disable an autoconfiguration class conditionally (based on a 
property or the presence/absence of a class), we can use the property that the class 
has annotated.

There is a class in Spring Boot autoconfiguration called `DataSourceAutoConfiguration`,
and it has the following conditional annotation:

```java
@ConditionalOnProperty(prefix = "spring.datasource",
                       name = "url")
```

So if we do not set `spring.datasource.url` in `application.properties`,
no DataSource will be autoconfigured.
