# Introduction

Dependency injection makes **testing** a *lot* easier, since the **DOC**s (Depend-On Components) your **SUT** (System Under Test) uses are passed as *arguments* to the SUT instead of being fetched from external sources such as component factories. 

This means you can configure the exact implementation of your DOC for testing purposes - e.g. a `FileReader` DOC simply returning a string literal.

## Dependency Inversion Principle

Typically - in the **traditional control flow** - an `App` makes direct calls to a `Library`, referencing the library-defined classes.

In an **inverted control flow**, however, both high-level and low-level code depend on an **abstraction** - the `App` makes use of an `Interface` (e.g. `IFileReader` or `IList`), and the `Library` simply provides concrete implementations for these interfaces.

> It's the same concept as in idiomatic Rust; in a method, you could take any `Writer` as parameter, as long as it supports `Writer` trait's methods. The actual implementation of the `Writer` trait is left to the function caller.

### An Example

```Java
public class ContentProcessor {

	// We use an interface instead of a class
	private IFileUtils fileUtils;
	
	// Crucially, we take a reference to a IFileUtils
	// instead of instantiating it. This is the DI!
	// - specifically, constructor injection.
	public ContentProcessor(IFileUtils fileUtils) {
		this.fileUtils = fileUtils;
	}
	
	public void run() {
		fileUtils.someMethod();
	}
	
}
```

**Why is this so useful?** 
In the actual application, we can pass a proper implementation of `IFileUtils` to the `ContentProcessor`. But, when testing, we can define our own *test implementation* of `IFileUtils`, which will function identically within `ContentProcessor`.

> For example, if we were testing reading and writing to a file, we could create an `IFileUtils` implementation that simply returns string literals on read, and saves written content to a public `String` field.

# Automatic DI

Normally, you'd perform **manual** dependency injection. However, there also exist libraries like `Guice` (dependencies can be added in either `pom.xml` or `build.gradle`).

When you're using automatic DI, you create a **Config** class (in Spring: `@Configuration`), which determines what *instance* gets coupled to what *type*. This could make it e.g. a **Singleton**, or you could return a new instance each time - it's up to the config.

You then use annotations such as `@Inject` to inject dependencies into methods and/or fields (framework specific, RTFM).

# DI Types

## Constructor Injection

The standard injection type - standard dependencies are defined in the **constructor**. 

```Java
public class ConstructorInjection {
	
	private IFileUtils fileUtils;
	
	@Inject
	public ConstructorInjection(IFileUtils fileUtils) {
		this.fileUtils = fileUtils;
	}
	
	/* snip */
	
}
```

An important benefit is that fields are **always valid**. However, components with many DOCs can grow to have inconveniently large constructors.

## Field Injection

In field injection, the DI system injects DOC references directly into **fields**.

```Java
public class FieldInjection {
	
	@Inject
	private IFileUtils fileUtils
	
	public FieldInjection() {
		/* snip */
	}
	
}
```

An advantage of this method is that code is **shorter**. However, you need reflection to access these private fields, so testing is made more difficult.