# Java SpringBoot
![Spring](./svgs/spring.svg "Springboot")
*Spring based applications that you can "just run"*

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

### Repository
Extends the CrudRepository interface given by spring boot, Crud repository provides many basic Crud 
methods that are commonly used. (there are more than this table but these are the most used.)

| method | 
| ------ | 
| findAll() |
| findById(ID id) |
| save(entity) |
| deleteAll() | 
| count() |
| deleteById(ID id) | 
| delete(entity) | 

You can add custom methods, ones that will autogenerate method implementations are written in this
way:  
`**findBy**Property(String foo)`  
this notifies spring that you want an autogenerated method available to use in the project.

### Controller 
Handles requests, basically a Java Class with certain annotations that provide Spring with 
information about which requests will trigger which functions. These functions are implemented in the 
service. The service is initialized as a singleton in memory, you get access to the singleton using
the @Autowired annotation which ensures you have access to the topicService object in memory if it exists.

```java
@RestController
public class TopicController {

    @Autowired
    private TopicService topicService;

    @RequestMapping("/topics")
    public List<Topic> getAllTopics() {
        return topicService.getAllTopics();
    }

    @RequestMapping("/topics/{id}")
    public Topic getTopic(@PathVariable String id) {
        return topicService.getTopic(id);
    }   
}
```

### Service 
In spring, services are typically singletons, when spring starts it registers this single instance 
in memory. You declare a service in spring with @Service, and get access to the repository using 
```java
@Autowired
private TopicRepository topicRepository
```
@Autowired checks if the TopicRepository exists in memory and gives you access to it. Much like 
we used @Autowired in the controller for this service.
```java
@Service
public class TopicService {

    @Autowired
    private TopicRepository topicRepository;

    private List<Topic> topics = new ArrayList<>(Arrays.asList(
            new Topic("spring", "Spring Framework", "Description of spring"),
            new Topic("java", "Core Java", "Description of java"),
            new Topic("javascript", "React Framework", "Description")
        ));

    public List<Topic> getAllTopics() {
        List<Topic> topicList = new ArrayList<>();
        topicRepository.findAll().forEach(topicList::add);
        return topicList;
    }

    public Topic getTopic(@String id) {
        return topics.stream().filter(t -> t.getId().equals(id)).findFirst().get();
    }
}
```

### Specific Class 
This is the class that maps an object to our table, this is the core of the Object Relational
Mapping (ORM). Empty constructors and all variable constructors are usually both provided. 
Jakarta.persistance library contains annotations that maps the id to the primary key of a table with 
@Id and @Column. While @Entity tells springboot that this is a table in the database.

```java
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Topic {
    @Id
    @Column(name="id")
    private String id;
    private String name;
    private String description;
    public Topic() {}
    public Topic(String id, String name, String description) {
        super();
        this.id = id;
        this.name = name;
        this.description = description;
    }
    public String getId() {
        return id;
    }
    public String getName() {
        return name;
    }
    public String getDescription() {
        return description;
    }
}
```


### ORMs
**Object Relational Mapping** 

ORM layer is responsible for managing conversion of software objects to interact with tables and columns in a 
relational database.

## Lombok
```java
// taking above example
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class Topics {
  @Id
  @Column(name = "topics")
  private Integer id;

  @Column(name = "name")
  private Integer name;

  @Column(name = "description")
  private String description;
} // This is exactly the same as the above example using Lombok convienience annotations.
```
Allows annotation syntax, that automatically generates getters and setters using dependency injection.

## Dependency Injection

**Aka a fancy way of saying passing instance variables to objects**
[good intro](https://www.youtube.com/watch?v=GB8k2-Egfv0)  
[good intro 2](https://www.youtube.com/watch?v=EPv9-cHEmQw)  
From the second video:
> Dependency injection generally means passing a dependent object as a parameter to a method, rather than having the method create the dependent object.  

> What this means in practice is that the method does not have a direct dependency on a particular implementation; any implementation that meets the requirements can be passed as a parameter.

Follows the inversion of control principal which says: *"depend on abstractions rather than implementations."* 

you can do this by creating a generic class or interface that usable my multiple classes, then passing
in those classes to another generic class. Example below.
```java
public class SomeClass {
    Database database;
    
    public SomeClass(Database implementedClass) {
      this.database = implementedClass 
    }

    //use this.database somehow
}

interface Database {
  public void requiredMethod() {
    System.out.println("I am required");
  }
}

public class DerbyDatabase implements Database {
  public void requiredMethod() {
    System.out.println("I am a Derby Database");
  }
}

public class Main {
  public static void main(String[] args) {
    Database myDatabase = new DerbyDatabase();
    SomeClass anything = new SomeClass(myDatabase); // injecting the dependency aka giving an object it's instance variables
    anything.requiredMethod();
    // prints I am a Derby Database
  }
}
```

In this example it is easy to extend SomeClass methods with **any** database object that's passed in,
because you injected the database dependency, and the database dependency is abstract enough to use 
any database you want, as long as it implements the Database interface.

## Autogenerate project
[This link](http://start.spring.io)
