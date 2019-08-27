# Creational Patterns Pt.1
Github ([link](https://github.com/Make-School-Courses/MOB-2.4-Advanced-Architectural-Patterns-in-iOS/blob/master/Lessons/01-Creational-PatternsPt.1/Lesson1.md))

Docs w/ Nyg ([link](https://docs.google.com/document/d/1HT9vY4O426WyIUZMPyK7rCcyGp7N_l2AQ1VQLxrIoLg/edit?usp=sharing))

# 3 types of desgin patterns
1. Creational -
1. Structural
1. Behavioral

| 0               | What is it?                                                                                                                                                                                 | When to use?                                                                                                                       | Benefits                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Pitfalls                                                                                                                                                                                                                          |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Singleton       | The singleton pattern guarantees that only one instance of a class is instantiated.                                                                                                         | When you want to make sure only one instance of a class is instantiated and that your application only uses that instance.         | Instance Control- Prevents other objects from instantiating their own copies of the singleton object, ensuring that all objects access the single instanceFlexibility- The class controls the instantiation process, therefore, the class has the flexibility to change the instantiation process.Ease of implementation- Able to create and use it anywhere and for the lifetime of the app. You are also sure of the number of instances when you use a singleton. | Singleton hinder unit testing. Might have to re write your singleton in your testing.They create hidden dependencies and carry state around for the lifetime of the application.They inherently cause code to be tightly coupled. |
| Object template | A behavioral design pattern that allows you to defines a skeleton of an algorithm in a base class and let subclasses override the steps without changing the overall algorithm’s structure. | Developers often use it to provide framework users with a simple means of extending standard functionality using inheritance.      | Helps prevent the tight coupling of componentsAlso provides a foundation for more advanced patterns                                                                                                                                                                                                                                                                                                                                                                  | The only pitfall is making the mistake of using a struct as a template when you intended to use a class.                                                                                                                          |
| Factory method  | The factory method is a creational design pattern that solves the problem of creating product objects without specifying their concrete classes.                                            | It’s very useful when you need to provide a high level of flexibility for your code.                                               | Factory Method Pattern makes the codebase more flexible to add or remove a new typeSimple to implementOften combined with a singleton and object pool patterns                                                                                                                                                                                                                                                                                                       | It’s very useful when you need to provide a high level of flexibility for your code.                                                                                                                                              |
| Builder         | It is used to construct a complex object step by step and the final step will return the object.                                                                                            | Compose complex objectsAvoid having a constructor with too many parametersYour code needs different versions of a specific project | It allows you to produce different types and representations of an object using the same construction code.Builder can use one of the other patterns to provide the logic that decides which object gets built.Builder is one of the top design patterns used in iOS development (along with Singleton and Factory Method)                                                                                                                                           | The number of lines of code increases at least to double in the builder pattern, but the effort pays off in terms of design flexibility and much more readable code.                                                              |


# Builder
```Swift
// factory protocol
protocol CarFactory {
    func create() -> Car
}

class Car {
    func drive() {
        print("driving a car")
    }
}

class Truck: Car {
    override func drive() {
        print("driving a truck")
    }
}

class SportsCar: Car {
    // missing function
    override func drive() {
        print("It's a lambo")
    }
}

class SUV: Car {
    override func drive() {
        print("I’d rather be driving!")
    }
}

/// concrete factory
class TruckFactory: CarFactory {
    func create() -> Car {
        return Truck()
    }
}

/// SUV factory
class SUVFactory: CarFactory {
    func create() -> Car {
        return SUV()
    }
}

/// Sportscar factory
class SportsCarFactory: CarFactory {
    func create() -> Car {
        return SportsCar()
    }
}

let truckFactory = TruckFactory()
let truck = truckFactory.create()
truck.drive() // prints "driving a truck"

let sportsCarFactory = SportsCarFactory()
let sportcar = sportsCarFactory.create()
sportcar.drive()

let suvFactory = SUVFactory()
let suv = suvFactory.create()
suv.drive()
```

# Factory method
```Swift
enum BicycleSize: String {
    case small
    case medium
    case large
}

enum BicycleType : String {
    case kids
    case standard
    case mountain
}

struct Bicycle
{
    public let type: BicycleType
    public let color: UIColor
    public let size: BicycleSize
}

extension Bicycle: CustomStringConvertible {
    public var description: String {
        return type.rawValue + "bicycle"
    }
}

// Builder Protocol
protocol BikeBuilder {
    var type: BicycleType { get set }
    var color: UIColor { get set }
    var size: BicycleSize { get set }

    func construct() -> Bicycle
}

// MARK: - Builder
class BicycleBuilder: BikeBuilder {

    var type: BicycleType = .standard
    var color: UIColor = .gray
    var size: BicycleSize = .medium

    func construct() -> Bicycle {
        return Bicycle(type: type, color: color, size: size)
    }
}

// MARK: - Director
public class BikeAssembler {

    // Build a kids bike
    func createKidsBike() -> Bicycle {
        let builder = BicycleBuilder()
        builder.type = .kids
        builder.size = .small
        return builder.construct()
    }

    // TODO: 1) build the Mountain bike
    func createMountainBike() -> Bicycle {
        let builder = BicycleBuilder()
        builder.type = .mountain
        builder.size = .large
        return builder.construct()
    }
    // TODO: 2) the Standard bike
    func createStandardBike() -> Bicycle {
        let builder = BicycleBuilder()
        builder.type = .standard
        builder.size = .medium
        return builder.construct()
    }
}

let bikeAssembler = BikeAssembler()
let kids = bikeAssembler.createKidsBike()
let standard = bikeAssembler.createStandardBike()
let mountain = bikeAssembler.createMountainBike()
print(standard.description)
```

# Object template pattern
Before
```Swift
var products = [
    ("Kayak", "A boat for one person", 275.0, 10),
        ("Lifejacket", "Protective and fashionable", 48.95, 14),
    ("Soccer Ball", "FIFA-approved size and weight", 19.5, 32)]

func calculateTax(product:(String, String, Double, Int)) -> Double {
    return product.2 * 0.2;
}

func calculateStockValue(tuples:[(String, String, Double, Int)]) -> Double {
    return tuples.reduce(0, {
        (total, product) -> Double in
        return total + (product.2 * Double(product.3))
    });
}

print("Sales tax for Kayak: $\(calculateTax(product: products[0]))")
print("Total value of stock: $\(calculateStockValue(tuples: products))")
```

After
```Swift
var products = [
    ("Kayak", 275.0, 10),
    ("Lifejacket", 48.95, 14),
    ("Soccer Ball", 19.5, 32)]

class Product {
    var name: String
    var price: Double
    var value: Int

    init(name: String, price: Double, value: Int) {
        self.name = name
        self.price = price
        self.value = value
    }
    func calculateTax() -> Double {
        return self.price * 0.2;
    }


}

func calculateStockValue2(products:[Product]) -> Double {
    var total = 0.0
    for product in products {
        total += (product.price * Double(product.value))
    }
    return total
}
```

# Singleton
```Swift
class DataSource {
    var creationalPatternsArray = ["Abstract Factory", "Factory Method",
                                   "Builder", "Dependency Injection", "Lazy Initialization",
                               "Object Pool", "Prototype", "Singleton"]

    static let sharedInstance = DataSource()

    private init() {
        print("self is:", self)
        print("creationalPatternsArray is", creationalPatternsArray)
    }

}

let data = DataSource.sharedInstance


func looper(){

    for num in 1...5 {
        _ = DataSource.sharedInstance
        print("num is:", num)
    }
}
looper()
```
