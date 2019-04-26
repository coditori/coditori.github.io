---
title:  "Spring Boot 2, Webflux, Reactor, H2"
date:   2018-04-26 19:33:40 +0330
tags:
  - spring-boot
  - webflux
  - reactive
  - restful
  - swagger-ui
  - maven
  - reactor
  - h2
toc: true
last_modified_at: 2018-04-26T19:33:40-03:30
---
Processing Real-Time data is a big challenge then Reactive tools come and help us to make an Asynchronous application without being involved with Low-Level headaches. This is a Reactive RESTful Web Service with Integration (End-To-End) test.

### Tech Stack
- Java 8
- Spring Boot 2
- WebFlux
- REST standards
- Swagger UI
- H2
- JUnit 4
- Reactor

### Maven Dependencies
It's the time to create a Maven Project and put the below dependencies on your POM.xml file.
{% highlight xml %}
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<properties>
    <h2.version>1.4.197</h2.version>
    <lombok.version>1.16.20</lombok.version>
    <swagger.version>2.8.0</swagger.version>
    <reactor.version>3.1.6.RELEASE</reactor.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>${h2.version}</version>
        <scope>runtime</scope>
    </dependency>
    <!-- swagger -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>${swagger.version}</version>
    </dependency>
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>${swagger.version}</version>
    </dependency>
    <!-- unit testing -->
    <dependency>
        <groupId>io.projectreactor</groupId>
        <artifactId>reactor-test</artifactId>
        <version>${reactor.version}</version>
        <scope>test</scope>
    </dependency>
{% endhighlight %}

### DB config (application.yml)
I used H2 to make this application more independent.
{% highlight yaml %}
spring:
  datasource:
    url: jdbc:h2:mem:customerdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
    username: sa
    password:
    driver-class-name: org.h2.Driver
    platform: h2

  # enable H2 web console and set url for web console
  # http://localhost:8080/console
  h2:
    console:
      enabled: true
      path: /console
{% endhighlight %}

### CustomerController.java
{% highlight java %}
@RestController
@RequestMapping("/customers")
public class CustomerController {

    @Autowired
    private CustomerService customerService;

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Customer> save(@RequestBody Customer customer) {
        return customerService.save(customer);
    }
    //...
}
{% endhighlight %}

### CustomerServiceImpl.java
{% highlight java %}
@Service
@Transactional
public class CustomerServiceImpl implements CustomerService {

    @Autowired
    private CustomerRepository customerRepository;

    @Override
    public Mono<Customer> save(Customer customer) {
        return Mono.just(customerRepository.save(customer));
    }
    //...
}
{% endhighlight %}

### CustomerRepository.java
As you see our repository is not reactive, because Spring does not support reactive interface "ReactiveCrudRepository" for RDBMS, but we made a web and service layer reactive! If you need an entire Reactive application you should use NoSQLs (Redis, MongoDB, Cassandra, ...).
{% highlight java %}
@Repository
public interface CustomerRepository extends JpaRepository<Customer, Long> {}
{% endhighlight %}

### Run Spring Boot
To make WebFlux Enabled we need "@EnableWebFlux" annotation.
{% highlight java %}
@EnableWebFlux
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
{% endhighlight %}
It's time to see what we have done, we can just run a Boot project with **mvn spring-boot:run** or use any IDE you like. If your Application is running on PORT 8080 just open [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html) then you will see all Restful Endpoints (/customers).

### CustomerIntegrationTest.java
This in an actual End-To-End test, That means we test the entire application like a user but with codes.
{% highlight java %}
@RunWith(SpringRunner.class)
@FixMethodOrder(MethodSorters.NAME_ASCENDING)
@SpringBootTest(classes=Application.class,
        webEnvironment=SpringBootTest.WebEnvironment.RANDOM_PORT)
public class CustomerIntegrationTest {

    @Autowired
    private WebTestClient webTestClient;

    private Customer newCustomer = new Customer();

    public void test2_add_customer_done() {
        WebClient webClient = WebClient.create("http://localhost:" + port);
        Customer customer = webClient.post().uri("/customers")
                .contentType(MediaType.APPLICATION_JSON)
                .body(BodyInserters.fromObject(newCustomer))
                .retrieve()
                .bodyToMono(Customer.class)
                .block();

        id = customer.getId();
        assertThat(customer.getName(), is(newCustomer.getName()));
    }

    @Test
    public void test3_find_customer_done() {
        webTestClient.get().uri("/customers/{id}", id)
                .accept(MediaType.APPLICATION_JSON)
                .exchange()
                .expectStatus().isOk()
                .expectBody()
                .jsonPath("$.name")
                .isEqualTo(newCustomer.getName());
    }
    //...
{% endhighlight %}

### Github repo
Just [git clone spring-boot-reactive-restful-rdbms][spring-boot-reactive-restful-rdbms] and run it on your Machine.

[spring-boot-reactive-restful-rdbms]: https://github.com/coditori/coditori/tree/master/java/spring-boot-reactive-restful-rdbms
