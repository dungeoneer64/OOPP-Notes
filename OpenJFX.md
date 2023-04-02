- The **Stage** represents a *window*, and can have one active **Scene** at a time.
- A **Scene** works much like it does in Unity, and has a root node.
- The **Scene Graph** is a hierarchical representation of all scene elements.

# Setting Up

OpenJFX is not integrated into Java, so if you want to run it in your IDE (for code debugging), you'll need to manually set it up:
- Download SDK & Scene Builder from https://openjfx.io/.
- Add VM arguments in your IDE Client's *Run Configuration*.
	- `--module-path="path-to-/javafx-sdk-version/lib" --add-modules=javafx.controls,javafx.fxml`
	- In IntelliJ, you have to `Add Run Options` and enable `Add VM Options`! (Run > Edit Configurations...)

# Scene Builder

OpenJFX can be set up **programmatically**, however this is quite tedious and difficult to maintain. It also offers the **Scene Builder**, which is a tool for visually creating Scenes. 

The Scene Builder tool saves files in **.fxml** format. These are to be saved in `src/main/resources/`. Create a separate package for all Scene **Controllers** for organization.

## Application Setup

```Java
public class FXMLExample extends javafx.application.Application {
	
	public static void main(String[] args) {
		launch(args);
	}
	
	@Override
	public void start(Stage primaryStage) throws IOException {
		
		FXMLLoader loader = new FXMLLoader();
		loader.setLocation(getClass().getClassLoader()
			.getResource("client/example.fxml"));
		Parent root = loader.load();
		
		Scene scene = new Scene(root);
		
		primaryStage.setScene(scene);
		primaryStage.show();
	}
} 
```

# Controllers

Scene Controllers can just be regular Java classes. In the Scene Builder, one then *selects* this Controller in the `Controller` panel. Then, in the **Inspector**, one can associate **Actions** (e.g. `On Drag Over`) to the Controller's methods.

```Java
public class ExampleController {
	public void DoSomething() {}
}
```

In the Inspector under **Code**, you can also set the `fx:id` to access a Component inside a Controller. You set this ID to be a valid field name (e.g. `myLabel`), and then inside the Controller, create a field of the same name annotated with `@FXML`.

```Java
public class ExampleController {
	
	@FXML
	private Label myLabel;
	
	private int counter = 0;
	
	public void Count() {
		counter += 1;
		myLabel.setText(Integer.toString(counter));
	}
}
```

# Dependecy Injection

## Setup

There are three classes (`Main`, `MainCtrl`, `MyModule`) that are involved in the process of creating new Scene Controllers. The process of adding a controller is summarized:
- Load it in `Main.start`.
- Add it to `MainCtrl` (controller and `Scene` fields, initialization and a custom method).
- Bind it in `MyModule`.

If you're not using a controller, just making a `Utils`, you don't need to do anything special to use dependency injection. You could bind it to `SINGLETON` scope in `MyModule` to be safe though!

### `Main`

In the example client application, the `Main` class contains a setup for an `Injector`, of type `MyFXML`. This helper class allows you to use dependency injection similarly to in [[Spring]].

You use it by calling `FXML.load`, providing the [[OpenJFX#Controllers|controller]] class and the (argument-separated) relative path of the scene, e.g.

```Java
var overv = FXML.load(MyCtrl.class, "client", "scenes", "s.fxml");
```

You can call this multiple times to load different scenes and their controllers into variables.

### `MainCtrl`

You can then call:

```Java
var mainCtrl = INJECTOR.getInstance(MainCtrl.class);
mainCtrl.initialize(primaryStage, myLoadedScene...)
```

The `MainCtrl` class is just a helper class (not from FXML) that provides utility functions for managing multiple scenes (setting titles, refreshing...).

### `MyModule`

`MyModule` is the configuration class for `Guice`. You'll need to bind each of your controllers in `SINGLETON` scope, as follows:

```Java
public class MyModule implements Module {
	
	@Override
	public void configure(Binder binder) {
		binder.bind(MyCtrl.class).in(Scopes.SINGLETON);
		/* snip */
	}
}
```

## Injection

You can request *other controllers* through the `@Inject` annotation, using constructor injection. You can then transition to other scenes through `mainCtrl.show(...)`.

### Initialization

You can have a Controller implement the `Initializable` interface, which provides the `public void initialize` method. Constructors are often bad times to initialize, since the whole application is still starting up - but `initialize` is only called after all startup is finished and the system is in a stable state!

### Shared State

- One-directional shared state? Just reference other controller via dependency injection.
- Two-directional? Decouple them by introducing a "Service" and bind it as a `SINGLETON` in `MyModule` - similar to how you'd approach this in Spring. The shared state is to be encapsulated in the Service.

# Debugging

- Make sure you're using the right annotations! IntelliJ can sneak the wrong `@Inject` in there...