A guide for Javascript **design patterns**.

<!-- # Types of Patterns -->

# Creational
Patterns that deals with the creation of new instances of an object.

* Constructor Pattern
* Module Pattern
* Factory Pattern

## Constructor Pattern
Used to create new objects with their own object scope.

```javascript
const Task = function(name) {
  // props
  this.name = name;
  this.completed = false;
}

// methods (set in "prototype" to be more efficient)
Task.prototype.complete = function() {
  this.completed = true
}

Task.prototype.save = function() {
  console.log('saving ' + this.name);
}

const myTask = new Task('my task');
myTask.save() // 'saving my task'

// same example using "class"
class Task {
  constructor(name) {
    this.name = name;
    this.completed = false;
  }

  complete() {
    this.completed = true
  }

  save() {
    console.log('saving ' this.name);
  }
}
```
---

## Module Pattern
Used to encapsulate a group of methods.

```javascript
// as a function / closure
const api = () => {
  return {
    get: id => {
      console.log('getting ' + id)
    },
    save: task => {
      console.log('saving ' + task.name)
    }
  }
}
// export default api()

// as an object
const api = {
  get: id => {
    console.log('getting ' + id)
  },
  save: task => {
    console.log('saving ' + task.name)
  }
}
// export default api

// "revealing module pattern" alternative
const api = () => {
  const get = id => {
    console.log('getting ' + id)
  }
  const save = task => {
    console.log('saving ' + task.name)
  }

  return {
    get,
    save
  }
}
// export default api()
```

---

## Factory Pattern
Used to simplify object creation. Can provide a generic interface for creating objects, where we can specify the type of factory object we wish to be created.

Ex: a vehicle factory where we are asked to create a type of vehicle. Rather than creating this vehicle directly using the 'new' operator (ex: new Car()) or via another creational constructor, we ask a Factory object for a new vehicle instead. We inform the Factory what type of vehicle is required and it instantiates this, returning it to us for use.

The Factory Pattern can be especially useful when applied to the following situations:
* When our object setup involves a high level of complexity
* When we need to easily generate different instances of objects depending on the environment we are in
* When we're working with many small objects that share the same properties

```javascript
// Car constructor
const Car = function (options) {
  this.doors = options.doors;
  this.color = options.color;
}

// Bike constructor
const Bike = function (options) {
  this.wheelSize = options.wheelSize;
  this.color = options.color;
}

// Factory
const VehicleFactory = function () {
  this.vehicleClass;

  this.createVehicle = function (options) {
    switch (options.vehicleType) {
      case 'car':
        this.vehicleClass = Car;
        break;
      case 'bike':
        this.vehicleClass = Bike;
        break;
    }
    return new this.vehicleClass(options)
  }
}

const factory = new VehicleFactory();

const blueCar = factory.createVehicle({
  vehicleType: 'car',
  doors: 4,
  color: 'blue'
});

const redBike = factory.createVehicle({
  vehicleType: 'bike',
  wheelSize: 'medium',
  color: 'red'
});

console.log(blueCar); // Car {doors: 4, color: "blue"}
console.log(redBike); // Bike {wheelSize: "medium", color: "red"}
```

# Structural
* Decorator
* Façade
* Flyweight

# Behavioral
* Command
* Mediator
* Observer
