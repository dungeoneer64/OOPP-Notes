>REST Requests are nowadays often performed with **JSON** object mapping. 

For whatever you want to exchange between client and server (in `commons`):

1) Define proper **data structures**. This means `equals`, `hashCode` and `toString` methods. Your fields should be `public` and all implementation details can be exposed (since this isn't a regular class) - and **don't** implement any behaviour of any kind.
2) Use an **object mapper** to serialize and deserialize data. In the server, `Jackson` has been configured for this. What's nice in Spring, is that you can just return any old data type in a `@GetMapping` - the serialization is done automatically. You can also choose to use `ResponseEntity<T>`, which allows you to set HTTP return code.

```Java
// Serialization
ObjectMapper OM = new ObjectMapper();
String json = OM.writeValueAsString(myValue);

// Deserialization
Person x = OM.readValue(json, Person.class);
```

In the client, we're using `Jersey`. You can then send requests to the Spring API that you built. An example:

```Java
Person p = new Person("foo", "bar");

Entity<Person> requestBody = Entity.entity(p, MediaType.APPLICATION_JSON);

GenericType<List<Person>> responseBodyType = new GenericType<List<Person>>() {};

List<Person> people = ClientBuilder.newClient()
	.target("http://localhost:8080/").path("api/people")
	.request(MediaType.APPLICATION_JSON)
	.accept(MediaType.APPLICATION_JSON)
	.post(requestBody, responseBodyType);
// requestBody defines entity that should appear in request body
// responseBodyType defines return type.
```