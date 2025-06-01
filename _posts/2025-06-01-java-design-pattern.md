---
layout: post
title: "Java Design Pattern"
date: 2025-06-01
tags:
  - java
  - design-pattern
#series: ""
#series-alias: ""
excerpt: "Java design pattern - Creational, Structural and Behavioral"
order: 1
---

## What are design patterns and why are they so important in the world of Java development?

**Design patterns are generalized, reusable solutions to commonly occurring problems in software design.** They're not
ready-to-use pieces of code that you can just copy and paste. Instead, they are templates or descriptions for how to
solve a problem that can be used in many different situations. In Java, using them helps you create code that's:

- **More reusable:** You can apply the same pattern in different parts of your application or even in different
  projects.
- **More maintainable:** Code that follows well-known patterns is often easier for other developers (and your future
  self!) to
  understand and modify.
- **More flexible:** Patterns help you design systems that can adapt to changes and new requirements without needing a
  complete overhaul.

Think of it like learning a common language for discussing software architecture. When developers say, "We used a
Factory Method here," everyone instantly gets a good idea of how that part of the system is structured.

Design patterns are typically categorized into three main types:

1. **Creational Patterns:** These patterns deal with object creation mechanisms, trying to create objects in a manner
   suitable to the situation.
2. **Structural Patterns:** These patterns concern class and object composition. They help form larger structures from
   individual parts.
3. **Behavioral Patterns:** These patterns are concerned with algorithms and the assignment of responsibilities between
   objects.

## Creational Design Patterns.

As the name suggests, Creational patterns are all about object creation. Their main purpose is to provide sophisticated
ways to create objects, often hiding the creation logic from the client (the part of your code that needs the object).
This makes your system more flexible regarding which objects are created, how they are created, and when they are
created.

Imagine you're running a pizza shop. You don't want the customer to know all the intricate details of making a pizza –
kneading the dough, spreading the sauce, adding toppings, baking it, etc. The customer just wants a pizza! A creational
pattern helps you create the pizza (the object) without the customer needing to know the complex steps involved.

### Factory Method pattern

The **Factory Method pattern** defines an interface for creating an object, but lets subclasses decide which class to
instantiate. In simpler terms, it's like a "factory" that produces different "products" (objects) based on your request,
without you needing to know the exact manufacturing process for each product.

#### Why use it?

- It decouples the client code from the concrete classes it instantiates. This means your code that uses the objects
  doesn't need to change if you introduce new types of objects.
- It promotes loose coupling and high cohesion, which are good software design principles.

Let's look at a simple Java example. Imagine you have a game where you need to create different types of vehicles: cars,
bikes, and trucks.

```java
// Product Interface
interface Vehicle {
    void drive();
}

// Concrete Products
class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a Car!");
    }
}

class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Riding a Bike!");
    }
}

// Creator Interface
interface VehicleFactory {
    Vehicle createVehicle();
}

// Concrete Creators
class CarFactory implements VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
}

class BikeFactory implements VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Bike();
    }
}

// How you would use it:
public class Game {
    public static void main(String[] args) {
        VehicleFactory carFactory = new CarFactory();
        Vehicle car = carFactory.createVehicle();
        car.drive(); // Output: Driving a Car!

        VehicleFactory bikeFactory = new BikeFactory();
        Vehicle bike = bikeFactory.createVehicle();
        bike.drive(); // Output: Riding a Bike!
    }
}
```

In this example:

- `Vehicle` is our "product" interface.
- `Car` and `Bike` are concrete products.
- `VehicleFactory` is our "creator" interface, with a `createVehicle()` method.
- `CarFactory` and `BikeFactory` are concrete creators that implement `createVehicle()` to produce specific vehicle
  types.

Notice how the `Game` class (our client) doesn't directly create `Car` or `Bike` objects using `new Car()`
or `new Bike()`.
Instead, it uses the factories, which makes it very flexible. If we introduce a `Truck` later, we just add a `Truck`
class
and a `TruckFactory`, and the `Game` class's logic for creating vehicles doesn't need to change significantly.

### Singleton pattern

The **Singleton pattern** is one of the simplest and most frequently used design patterns. Its main purpose is to *
*ensure
that a class has only one instance and provides a global point of access to that instance.**

Think of it like the President of a country. There can only be one President at any given time, and everyone knows how
to access (or communicate with) the President's office. You don't need multiple presidents running around, right?
Similarly, in software, some resources should only ever have one instance.

#### Why would you need a Singleton?

- **Resource Management:** For managing a single database connection pool, a logging utility, or a configuration
  manager, where having multiple instances could lead to conflicts or inefficiencies.
- **Global Access:** When you need a single, centralized point of control for a specific service or data.

Let's look at a classic Java example for a `ConfigurationManager`:

```java
public class ConfigurationManager {
    // 1. Static field to hold the single instance
    private static ConfigurationManager instance;

    // 2. Private constructor to prevent direct instantiation
    private ConfigurationManager() {
        // Initialize configuration settings here
        System.out.println("ConfigurationManager instance created.");
    }

    // 3. Public static method to provide global access to the instance
    public static ConfigurationManager getInstance() {
        if (instance == null) {
            // This is crucial for thread safety in multi-threaded environments,
            // though a more robust synchronized approach is often used for production.
            synchronized (ConfigurationManager.class) {
                if (instance == null) {
                    instance = new ConfigurationManager();
                }
            }
        }
        return instance;
    }

    // Example method to get a setting
    public String getSetting(String key) {
        // In a real app, this would read from a config file/database
        return "Value for " + key;
    }

    public static void main(String[] args) {
        // Get the first instance
        ConfigurationManager config1 = ConfigurationManager.getInstance();
        System.out.println(config1.getSetting("database.url"));

        // Try to get another instance
        ConfigurationManager config2 = ConfigurationManager.getInstance();
        System.out.println(config2.getSetting("api.key"));

        // Check if they are the same instance
        System.out.println("Are config1 and config2 the same instance? " + (config1 == config2));
        // Output will be: "Are config1 and config2 the same instance? true"
    }
}
```

#### Key elements of a Singleton:

1. **Private static instance variable:** `private static ConfigurationManager instance;`
    - This holds the only instance of the class. `static` means it belongs to the class, not to any specific object.
      private ensures that it can only be accessed from within the class itself.
    - `private` ensures that it can only be accessed from within the class itself.
2. **Private constructor:** `private ConfigurationManager() { ... }`
    - By making the constructor private, you prevent other classes from using new ConfigurationManager() to create new
      instances directly.
3. Public static `getInstance()` method: `public static ConfigurationManager getInstance() { ... }`
    - This is the global access point. Anyone who needs the ConfigurationManager asks this method for it.
    - It checks if an instance already exists. If not, it creates one (and only one!). The `synchronized` block is
      important for multi-threaded applications to prevent multiple threads from creating separate instances
      simultaneously.

#### Where you wouldn't use Singleton pattern (example)

you wouldn't use a Singleton for a `UserSession` because e**ach user needs their own distinct, isolated session to
prevent
data mixing and security breaches.** The Singleton pattern is best suited for resources that are truly global and
independent of specific user contexts.

### Builder pattern

The `Builder pattern` is designed to solve the problem of creating complex objects with many optional parts or
configurations. Imagine you're ordering a custom-built computer. You don't just say "give me a computer"; you specify
the CPU, RAM, graphics card, storage, operating system, and so on. Each of these parts might be optional, or have many
variations.

If you tried to create such an object using a traditional constructor with many parameters, it would quickly become
unwieldy and hard to read (this is often called a "telescoping constructor anti-pattern").

The Builder pattern separates the construction of a complex object from its representation, allowing the same
construction process to create different representations. In simpler terms, it lets
you `construct an object step-by-step using a dedicated builder object.`

#### Why use it?

- **Readability:** Makes object creation code much cleaner and easier to understand, especially when an object has many
  attributes, some of which might be optional.
- **Flexibility:** You can create different configurations of an object using the same builder process.
- **Immutability:** It helps in creating immutable objects, as the object is constructed in stages and then "built" at
  the end.

Let's look at a common example: building a `Pizza` with various optional toppings.

```java
// The "Product" class - our Pizza
class Pizza {
    private String dough;
    private String sauce;
    private String cheese;
    private boolean pepperoni;
    private boolean mushrooms;

    // Private constructor, only the Builder can call it
    private Pizza(PizzaBuilder builder) {
        this.dough = builder.dough;
        this.sauce = builder.sauce;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.mushrooms = builder.mushrooms;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("Your Pizza:\n");
        sb.append("  Dough: ").append(dough).append("\n");
        sb.append("  Sauce: ").append(sauce).append("\n");
        sb.append("  Cheese: ").append(cheese).append("\n");
        if (pepperoni) sb.append("  + Pepperoni\n");
        if (mushrooms) sb.append("  + Mushrooms\n");
        return sb.toString();
    }

    // The "Builder" class nested within the Product
    public static class PizzaBuilder {
        private String dough;
        private String sauce;
        private String cheese;
        private boolean pepperoni = false; // Default to false
        private boolean mushrooms = false; // Default to false

        // Constructor for mandatory parts
        public PizzaBuilder(String dough, String sauce, String cheese) {
            this.dough = dough;
            this.sauce = sauce;
            this.cheese = cheese;
        }

        // Methods for optional parts, returning the builder itself for chaining
        public PizzaBuilder withPepperoni() {
            this.pepperoni = true;
            return this;
        }

        public PizzaBuilder withMushrooms() {
            this.mushrooms = true;
            return this;
        }

        // The final "build" method that returns the constructed Pizza object
        public Pizza build() {
            return new Pizza(this);
        }
    }
}

// How you would use it:
public class PizzaOrder {
    public static void main(String[] args) {
        // Order a simple Margherita pizza
        Pizza margherita = new Pizza.PizzaBuilder("Thin Crust", "Tomato", "Mozzarella")
                                    .build();
        System.out.println(margherita);

        System.out.println("---");

        // Order a loaded veggie pizza
        Pizza veggieDelight = new Pizza.PizzaBuilder("Thick Crust", "Pesto", "Cheddar")
                                       .withMushrooms()
                                       .build(); // No pepperoni
        System.out.println(veggieDelight);

        System.out.println("---");

        // Order a meat lover's pizza
        Pizza meatLover = new Pizza.PizzaBuilder("Hand-tossed", "BBQ", "Provolone")
                                   .withPepperoni()
                                   .withMushrooms() // You can add both!
                                   .build();
        System.out.println(meatLover);
    }
}
```

Notice how clean and readable the object creation is: `new Pizza.PizzaBuilder(...).withPepperoni().withMushrooms()
.build();`. Each method call (like `.withPepperoni()`) adds a specific component, and the `.build()` method finally
constructs the `Pizza` object.

#### Real-world scenario

Imagine you're developing software for an online travel agency. Users can book flights, hotels, rental cars, and even
tours. When a user wants to book a complete travel package, they have many choices:

- **Flight details:** departure and arrival airports, dates, airline, seat class.
- **Hotel details:** city, check-in/out dates, hotel name, room type, number of guests.
- **Rental car details:** pick-up/drop-off locations, dates, car type, insurance options.
- **Optional tours/activities:** specific tours, dates, number of participants.

## Structural Design Patterns

While Creational patterns focus on **how objects are created, Structural patterns are all about how classes and objects
are composed and organized to form larger structures.** They help ensure that if one part of a system changes, the
entire
structure doesn't need to be rebuilt. Think of them as ways to build flexible and robust frameworks using existing
building blocks.

Imagine you have a set of different types of building blocks (like Lego bricks, wooden blocks, and magnetic tiles).
Structural patterns are about how you can combine these different types of blocks in clever ways to build complex and
adaptable structures.

### Adapter pattern

The **Adapter pattern** is like a universal travel adapter for your electronics. You have a device with one type of
plug (an
interface), and the wall outlet has a different type of socket (another interface). The adapter allows your device to
work with the different outlet, even though they were originally incompatible.

In software, the Adapter pattern allows objects with incompatible interfaces to work together. It acts as a bridge
between two incompatible interfaces.

**Why use it?**

- **Reusability:** You can reuse existing classes even if their interfaces don't match the one you need.
- **Flexibility:** It helps integrate new components with existing systems without modifying the core code of either.

Let's consider a simple Java example. Imagine you have a legacy drawing application that expects shapes to implement an
interface called `LegacyShape`. Now, you've got a new set of modern geometric classes that implement a different
interface, `ModernShape`.

```java
// The "Target" interface that the client expects
interface LegacyShape {
    void display(int x1, int y1, int x2, int y2);
}

// The "Adaptee" class - the class with the incompatible interface
class ModernRectangle {
    public void drawRectangle(int startX, int startY, int width, int height) {
        System.out.println("Modern Rectangle: Drawing at (" + startX + "," + startY + ") with width " + width + " and height " + height);
    }
}

// The "Adapter" class - makes ModernRectangle compatible with LegacyShape
class ModernRectangleAdapter implements LegacyShape {
    private ModernRectangle modernRectangle;

    public ModernRectangleAdapter(ModernRectangle modernRectangle) {
        this.modernRectangle = modernRectangle;
    }

    @Override
    public void display(int x1, int y1, int x2, int y2) {
        // Adapt the LegacyShape's coordinates to ModernRectangle's parameters
        int startX = x1;
        int startY = y1;
        int width = x2 - x1;
        int height = y2 - y1;
        modernRectangle.drawRectangle(startX, startY, width, height);
    }
}

// Client code that expects LegacyShape
public class DrawingApplication {
    public void renderShape(LegacyShape shape, int x1, int y1, int x2, int y2) {
        System.out.println("Rendering using LegacyShape interface...");
        shape.display(x1, y1, x2, y2);
    }

    public static void main(String[] args) {
        DrawingApplication app = new DrawingApplication();

        // Use a legacy shape directly
        LegacyShape oldSquare = new LegacyShape() {
            @Override
            public void display(int x1, int y1, int x2, int y2) {
                System.out.println("Legacy Square: Displaying from (" + x1 + "," + y1 + ") to (" + x2 + "," + y2 + ")");
            }
        };
        app.renderShape(oldSquare, 10, 10, 50, 50);

        System.out.println("---");

        // Now, adapt the ModernRectangle to work with the LegacyShape interface
        ModernRectangle newRect = new ModernRectangle();
        LegacyShape adaptedRect = new ModernRectangleAdapter(newRect);
        app.renderShape(adaptedRect, 60, 60, 100, 80);
    }
}
```

In this example:

- `LegacyShape` is the `Target` (what the `DrawingApplication` expects).
- `ModernRectangle` is the `Adaptee` (the incompatible class).
- `ModernRectangleAdapter` is the `Adapter` that wraps `ModernRectangle` and implements `LegacyShape`, translating calls
  from `display` to `drawRectangle`.

The `DrawingApplication` doesn't even know it's dealing with a `ModernRectangle` because the adapter handles the
translation!

#### Real-world scenario

Imagine you're building an e-commerce website. You have a well-established `PaymentProcessor` interface that your
checkout
system uses, which only handles `processCreditCardPayment(String cardNumber, double amount)`.

Now, your company decides to integrate with a new, popular payment gateway called `PayPalService`.
However, `PayPalService`
has a method `makePayment(String email, double totalAmount, String currency)` which is different from your
PaymentProcessor interface.

### Decorator pattern

The `Decorator pattern` allows you to attach new behaviors or "responsibilities" to an object dynamically, without
altering its original code. Think of it like ordering a coffee: you start with a basic coffee, then you can "decorate"
it with milk, sugar, whipped cream, or extra shots. Each "decoration" adds to the coffee's features and potentially its
cost, but the core coffee remains the same.

#### Why use it?

- **Flexibility:** You can combine multiple decorators to add multiple functionalities.
- **Open/Closed Principle:** It allows you to extend the functionality of a class without modifying its existing code,
  which is a key principle in object-oriented design (classes should be "open for extension but closed for
  modification").
- **Avoids subclassing explosion:** Instead of creating a ton of subclasses for every combination of features (e.g.,
  `CoffeeWithMilk`, `CoffeeWithSugar`, `CoffeeWithMilkAndSugar`), you decorate a base object.

Let's look at a classic example: a coffee ordering system where you want to add condiments to a basic coffee.

```java
// Component Interface: What we want to decorate
interface Coffee {
    String getDescription();
    double getCost();
}

// Concrete Component: The basic object
class SimpleCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Simple Coffee";
    }

    @Override
    public double getCost() {
        return 5.0; // Base cost for coffee
    }
}

// Decorator Abstract Class: Must implement the Component interface
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee; // The object we are decorating

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    // Default implementations that delegate to the decorated object
    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }

    @Override
    public double getCost() {
        return decoratedCoffee.getCost();
    }
}

// Concrete Decorators: Add specific behaviors
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription() + ", Milk";
    }

    @Override
    public double getCost() {
        return decoratedCoffee.getCost() + 1.5; // Add cost of milk
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription() + ", Sugar";
    }

    @Override
    public double getCost() {
        return decoratedCoffee.getCost() + 0.5; // Add cost of sugar
    }
}

// How you would use it:
public class CoffeeShop {
    public static void main(String[] args) {
        // A simple coffee
        Coffee coffee = new SimpleCoffee();
        System.out.println("Order 1: " + coffee.getDescription() + " | Cost: $" + coffee.getCost());
        // Output: Order 1: Simple Coffee | Cost: $5.0

        // A coffee with milk
        Coffee coffeeWithMilk = new MilkDecorator(new SimpleCoffee());
        System.out.println("Order 2: " + coffeeWithMilk.getDescription() + " | Cost: $" + coffeeWithMilk.getCost());
        // Output: Order 2: Simple Coffee, Milk | Cost: $6.5

        // A coffee with milk AND sugar
        Coffee coffeeWithMilkAndSugar = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
        System.out.println("Order 3: " + coffeeWithMilkAndSugar.getDescription() + " | Cost: $" + coffeeWithMilkAndSugar.getCost());
        // Output: Order 3: Simple Coffee, Milk, Sugar | Cost: $7.0

        // You can even decorate a decorated object!
        Coffee extraSweetCoffee = new SugarDecorator(new SugarDecorator(new SimpleCoffee()));
        System.out.println("Order 4: " + extraSweetCoffee.getDescription() + " | Cost: $" + extraSweetCoffee.getCost());
        // Output: Order 4: Simple Coffee, Sugar, Sugar | Cost: $6.0
    }
}
```

As you can see, each decorator wraps the original `Coffee` object (or another decorated `Coffee` object) and adds its
own behavior (like adding "Milk" to the description and $1.5 to the cost).

#### Real-world scenario

Imagine you're developing a system for a text editor. You have a basic `TextDocument` class that can simply `display()`
its raw text. Now, you want to add various functionalities to this document, such as:

- **Spell Checking:** Highlight misspelled words.
- **Syntax Highlighting:** Color-code different parts of the code (if it's a code document).
- **Line Numbering:** Display line numbers next to each line.

### Facade pattern

The **Facade pattern** provides a simplified, higher-level interface to a complex subsystem. Imagine you're operating a
home
theater system with a TV, a Blu-ray player, a surround sound receiver, and a game console. To watch a movie, you might
need to:

1. Turn on the TV.
2. Switch the TV's input to the Blu-ray player.
3. Turn on the Blu-ray player.
4. Turn on the sound receiver.
5. Set the sound receiver's input to the Blu-ray player.
6. Adjust the volume.

That's a lot of steps! A Facade would be like a single "Watch Movie" button on a universal remote that performs all
these actions for you. You interact with the simple "Watch Movie" button, not the individual complex components.

#### Why use it?

- **Simplification:** It makes a complex subsystem easier to use by providing a single, simplified interface.
- **Decoupling:** It decouples the client from the complex internal workings of the subsystem, meaning changes within
  the subsystem are less likely to affect the client code.
- **Reduced Dependencies:** The client only depends on the facade, not on all the individual classes within the
  subsystem.

Let's look at a Java example for a `HomeTheaterFacade`:

```java
// Complex Subsystem Components
class Television {
    public void turnOn() { System.out.println("TV is ON"); }
    public void turnOff() { System.out.println("TV is OFF"); }
    public void setInput(String input) { System.out.println("TV input set to " + input); }
}

class DvdPlayer {
    public void turnOn() { System.out.println("DVD Player is ON"); }
    public void turnOff() { System.out.println("DVD Player is OFF"); }
    public void playMovie(String movie) { System.out.println("Playing movie: " + movie); }
}

class SoundSystem {
    public void turnOn() { System.out.println("Sound System is ON"); }
    public void turnOff() { System.out.println("Sound System is OFF"); }
    public void setVolume(int volume) { System.out.println("Sound volume set to " + volume); }
    public void setInput(String input) { System.out.println("Sound System input set to " + input); }
}

// The Facade
class HomeTheaterFacade {
    private Television tv;
    private DvdPlayer dvdPlayer;
    private SoundSystem soundSystem;

    public HomeTheaterFacade(Television tv, DvdPlayer dvdPlayer, SoundSystem soundSystem) {
        this.tv = tv;
        this.dvdPlayer = dvdPlayer;
        this.soundSystem = soundSystem;
    }

    // Simplified method for watching a movie
    public void watchMovie(String movie) {
        System.out.println("\nGet ready to watch a movie...");
        tv.turnOn();
        tv.setInput("DVD");
        dvdPlayer.turnOn();
        dvdPlayer.playMovie(movie);
        soundSystem.turnOn();
        soundSystem.setInput("DVD");
        soundSystem.setVolume(15);
    }

    // Simplified method for ending the movie
    public void endMovie() {
        System.out.println("\nShutting down home theater...");
        soundSystem.turnOff();
        dvdPlayer.turnOff();
        tv.turnOff();
    }
}

// Client Code
public class Client {
    public static void main(String[] args) {
        Television tv = new Television();
        DvdPlayer dvd = new DvdPlayer();
        SoundSystem sound = new SoundSystem();

        HomeTheaterFacade homeTheater = new HomeTheaterFacade(tv, dvd, sound);

        homeTheater.watchMovie("The Matrix");
        homeTheater.endMovie();
    }
}
```

In this example, the `HomeTheaterFacade` acts as a single entry point for complex operations like "watching a movie."
The client code (in `Client.java`) doesn't need to know the specific steps or the individual components; it just calls
`watchMovie()` and `endMovie()`.

#### Real-world scenario

You are building a complex banking application. The core system has many intricate components for managing accounts,
processing transactions, handling security, and generating reports. A new developer joins your team and needs to quickly
implement a feature for a user to simply "Deposit Money" into their account.

## Behavioral Design Patterns

Behavioral patterns are concerned with **algorithms and the assignment of responsibilities between objects.** They
describe
how objects and classes interact and distribute responsibilities. Think of them as defining the "dance" between
different parts of your system, ensuring they communicate efficiently and effectively.

Imagine a busy restaurant kitchen. Behavioral patterns are like the established routines and communication methods that
ensure orders flow from the waitstaff to the kitchen, dishes are prepared, and food is delivered to the right table
without chaos.

### Observer pattern

The **Observer pattern** defines a one-to-many dependency between objects so that when one object (the "subject" or "
observable") changes state, all its dependents (the "observers") are notified and updated automatically.

Think of a newspaper subscription. When a new issue of the newspaper is published (the "subject" changes state), all
subscribers (the "observers") automatically receive a copy. The newspaper doesn't need to know each subscriber
individually, it just sends out the update, and the subscribers receive it if they've signed up.

#### Why use it?

- **Loose Coupling:** The subject doesn't need to know the concrete classes of its observers, only that they implement a
  common interface. This makes the system more flexible.
- **Event Handling:** It's widely used in event handling systems, where a change (an event) in one part of the system
  needs to trigger actions in other parts.
- **Consistency:** Ensures consistency between related objects without making classes tightly coupled.

Let's look at a simple Java example. Imagine you have a `WeatherData` station (the subject) that updates temperature,
humidity, and pressure. You want different displays (observers) to automatically show this updated data.

```java
import java.util.ArrayList;
import java.util.List;

// Subject Interface (Observable)
interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// Observer Interface
interface Observer {
    void update(float temperature, float humidity, float pressure);
}

// Concrete Subject (WeatherData)
class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
        observers = new ArrayList<>();
    }

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
        System.out.println("Observer registered: " + o.getClass().getSimpleName());
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
        System.out.println("Observer removed: " + o.getClass().getSimpleName());
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }

    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }

    public void measurementsChanged() {
        notifyObservers();
    }
}

// Concrete Observer (CurrentConditionsDisplay)
class CurrentConditionsDisplay implements Observer {
    private float temperature;
    private float humidity;
    private Subject weatherData; // To unsubscribe if needed

    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this); // Register itself
    }

    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }

    public void display() {
        System.out.println("Current conditions: " + temperature
            + "F degrees and " + humidity + "% humidity");
    }
}

// Another Concrete Observer (StatisticsDisplay)
class StatisticsDisplay implements Observer {
    private float maxTemp = 0.0f;
    private float minTemp = 200.0f;
    private float tempSum = 0.0f;
    private int numReadings;
    private Subject weatherData;

    public StatisticsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }

    @Override
    public void update(float temperature, float humidity, float pressure) {
        tempSum += temperature;
        numReadings++;

        if (temperature > maxTemp) {
            maxTemp = temperature;
        }

        if (temperature < minTemp) {
            minTemp = temperature;
        }

        display();
    }

    public void display() {
        System.out.println("Avg/Max/Min temperature = " + (tempSum / numReadings)
            + "/" + maxTemp + "/" + minTemp);
    }
}


// Client Code
public class WeatherStation {
    public static void main(String[] args) {
        WeatherData weatherData = new WeatherData();

        CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
        StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);

        weatherData.setMeasurements(80, 65, 30.4f);
        weatherData.setMeasurements(82, 70, 29.2f);
        weatherData.setMeasurements(78, 90, 29.2f);

        // We can even remove an observer
        weatherData.removeObserver(statisticsDisplay);
        weatherData.setMeasurements(62, 70, 28.1f);
    }
}
```

In this example:

- `WeatherData` is the `Subject` (observable).
- `Observer` is the interface for all observers.
- `CurrentConditionsDisplay` and `StatisticsDisplay` are `Concrete Observers`.

When `setMeasurements()` is called on `WeatherData`, it notifies all registered observers, which then update themselves.

#### Real-world scenario

Imagine you are building a social media application. When a user posts a new photo (this is the "event" or "state
change"), several things need to happen automatically:

The user's followers should receive a notification.
The photo should be added to the user's personal photo gallery.
The photo should be analyzed by an AI service for content moderation.