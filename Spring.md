**Spring** is a web framework.

# Getting Started

Quickstarting a project:
- [Spring Initializr](https://start.spring.io/)

# Components

A component is any class that is managed by Spring. By default, Spring only looks into the main **package** - but you can use `@ComponentScan` to specify other packages to look for annotations in as well.

## Controller

- A class annotated with `@Controller` is responsible for **handling HTTP requests** (there also exists `@RestController`).
- You can request a specific path for a controller using `@RequestMapping(String path)`. To allow for *multiple* paths, use an array of strings (e.g. `@RequestMapping(path = { "", "/" })`).

> For mappings, put slashes `/` on the *left* side of your paths.

- Methods can use `@GetMapping(String path)` to mark self as an **endpoint** and trigger on specific paths during a `GET` request (this does not include protocol, hostname and port).

> Using `@GetMapping`, any path from `RequestMapping` is automatically appended to the front.

- Use `@ResponseBody` to mark a return value as response.

```Java
@Controller
@RequestMapping("/test")
public class HelloController {
	
	@GetMapping("/greeting/{name}")
	@ResponseBody
	public String named(@PathVariable("name") String name) {
		return "Hello " + name + "!";
	}
	
	/*
	You can then access the path through 
	http://localhost:8080/test/greeting/SomeName
	*/
}
```

- You can use path dependency injection using `@PathVariable(String variableName)`.
- Several annotations exist that can be used to access request details, including `@RequestParam`, `@RequestBody` and `@RequestHeader`.

## Service

- A non-static class annotated with `@Service` is handled by the Spring instantiator (this creates a Singleton-esque instance).
- Anything that is not used for direct endpoints or database access.
- You can then use [[Dependency Injection]] to request a service *instance*. Constructor injection is recommended (no `@` annotation is required, just use a constructor parameter).

## Configuration

- Using a `@Configuration` class, you can custom define the Spring dependency injection.
- Using a `@Bean` annotation on a method, you can associate a data type to an instance for dependency injection purposes. **By default**, it uses `singleton` scope.

```Java
@Configuration
public class Config {
	
	@Bean
	public Random getRandom() {
		return new Random();
	}
	
}
```

- Using the `@Scope(value = ConfigurableBeanFactory.X)` annotation on a bean, you can set e.g. `Singleton`, `Prototype` (regenerates instances), etc...

## Repository

- A `@Repository` is simply defined as an interface that extends `JpaRepository<DataType, KeyType>`. Repositories should be placed in the `server` `src` folder.

```Java
public interface UserRepository extends JpaRepository<User, String> { }
```

- An `@Entity` is a class that represents one relational DB entity. One of the fields must be annotated with `@Id`, the entity's primary key. Entities should be placed in the `commons` `src` folder. This will make it accessible to both `client` and `server` code.

> **IMPORTANT**: you need an *empty constructor* (alongside your regular constructor(s)) for object mappers! Otherwise you will encounter `500 internal server error`s when you `save` to the DB.

```Java
@Entity
public class User {
	
	@Id
	private long id;
	private String name;
	
	public User() {}
	
	public User(long id, String name) {
		this.id = id;
		this.name = name;
	}
}
```

- Using a repository can simply be achieved through dependency injection, by requesting e.g. `UserRepository` as a [[Spring#Controller|controller]] constructor parameter.
- The **H2 console** can be accessed at `[host]/h2-console`.