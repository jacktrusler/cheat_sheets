# Java SpringBoot
Interfaces are an abstract blueprint on how to create a class

Classes are a blueprint of an object that is stored in the heap in memory.

## Spring Boot
[Watch this guys' playlist](https://www.youtube.com/playlist?list=PLqq-6Pq4lTTbx8p2oCgcAQGQyqN8XeA1x)  
**Spring + Boot**
Spring by itself is a framework that includes a lot of build tools, however you have to specify 
what specific tools you want to use, this is both difficult and time consuming knowing what tools
you might need for your project

### Boot
Spring Boot was made to speed up the process, spring boot gives you the tools and annotations that
are good for a vast majority of developers using spring, and allows you to configure further if 
needed.

### Example Basic pom.xml
Project Object Model (pom)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.javabrains</groupId>
    <artifactId>springboot</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>19</maven.compiler.source>
        <maven.compiler.target>19</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>19</java.version>
    </properties>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.1</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```
[intro to pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
Most of the XML tags are self explanitory, some things to note: 

- Parent as org.springframework.boot, this tells Maven what specific JARs you need when you 
specify your dependencies. 
- Dependencies indicate to maven to go fetch and download artifacts from their repo and save them 
locally so they can be used by the project.
- The java.version and maven compiler target must match, if you need to upgrade java [here's how.](https://docs.fedoraproject.org/en-US/quick-docs/installing-java/)
I just used `sudo dnf install java-latest-openjdk.x86_64`. Make sure your `$JAVA_HOME` is set, mine
is set to `/usr/lib/jvm/java-19-openjdk`
```bash
# I put this in my .zshrc file
export JAVA_HOME="/usr/lib/jvm/java-19-openjdk"
export PATH=$JAVA_HOME/bin:$PATH
```

## @RestController @RequestMapping @Autowired @Service
Spring annotations that tell springboot that the declared classes are a rest controller that 
is mapped to an endpoint. This endpoint when hit will run the function that is annotated with
@RequestMapping. The @Autowired method looks for a singleton @Service class that has been 
initialized and allows you to access it. 

```java
//file TopicController.java
@RestController
public class TopicController {

    @Autowired
    private TopicService topicService;
    @RequestMapping("/topics")
    // List gets automatically converted to JSON
    public List<Topic> getAllTopics() {
        return topicService.getAllTopics();
    }
}

//file TopicService.java
@Service
public class TopicService {
    private List<Topic> topics = Arrays.asList(
            new Topic("spring", "Spring Framework", "Description of spring"),
            new Topic("java", "Core Java", "Description of java"),
            new Topic("javascript", "React Framework", "Description")
        );
    public List<Topic> getAllTopics() {
        return topics;
    }
}
```


## JPA 
**Jakarta Persistence API** (i.e. hibernate is one JPA tool)
[Great JPA intro](https://www.youtube.com/watch?v=z3HnFBzn7DI)

Concerned with Persistence, specifically a mechanism by which Java objects outlive the application
that created them. Most applications persist key business objects. JPA lets you define which 
objects to persist and how. JPA is not a tool or framework, it defines a set of concepts that guide
implementations.

```java
@Entity //annotation that tells jpa that private member variables will be columns for the class name.
@Id //annotation that tells jpa that you want <this> member variable to be the primary key. 

//example 
@Entity
public class EpicTable {
  @Id
  @Column(name = "epic_id")
  private Integer epicId;

  @Column(name = "epic_rating_out_of_10")
  private Integer epicRating;

  @Column(name = "epic_food")
  private String epicFood;

  // No args constructor
  public EpicTable() {}

  // All args constructor
  public EpicTable(Integer epicId, Integer epicRating, String epicFood) {
    super();
    this.epicId = epicId;
    this.epicRating = epicRating;
    this.epicFood = epicFood;
  }

  // getters and setters
  public Integer getEpicId() {
    return epicId;
  }

  public Integer setEpicId(Integer id) {
    this.epicId = id;
  }

  // repeat for the other 2 member variables
}
```

Tell JPA you want to use the EpicTable instance.

```java
@Service
public class EpicTableService {

  @Autowired
  private EpicTableRepository epicTableRepository;
}
```

Use spring to make an interface to access EpicTableService

```java
//your custom interface will have a spring data interface called CrudRepository that has a bunch of common CRUD methods 
// ready out of the box: get, post, put, delete, you implement the custom ones that you want.
public interface EpicTableRepository extends CrudRepository<EpicTable, Integer> {

}
```

### ORMs
**Object Relational Mapping** 

ORM layer is responsible for managing conversion of software objects to interact with tables and columns in a 
relational database.

### Lombok
Allows the  
```java
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor

// taking above example
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class MyEpicTable {
  @Id
  @Column(name = "epic_id")
  private Integer epicId;

  @Column(name = "epic_rating_out_of_10")
  private Integer epicRating;

  @Column(name = "epic_food")
  private String epicFood;
} // This is exactly the same as the above example using Lombok convienience annotations.
```

syntax, that automatically generates getters and setters.

## Autogenerate project
[This link](http://start.spring.io)

If you're adding derby and get an error try putting this in a file at the root of your project named
application.properties

    spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
