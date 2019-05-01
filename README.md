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
    console.log('saving ' + this.name);
  }
}

// same example using a function that return an object
const Task = (name) => ({
  name,
  completed: false,

  complete: () => {
    this.completed = true
  },
  save() {
    console.log('saving ' + this.name);
  }
});
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
const Car = function(options) {
  this.doors = options.doors;
  this.color = options.color;
}

// Bike constructor
const Bike = function(options) {
  this.wheelSize = options.wheelSize;
  this.color = options.color;
}

// Factory
const VehicleFactory = function() {
  this.vehicleClass;

  this.createVehicle = function(options) {
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
---

# Structural
Concerned with how objects are made up and simplify relationships between objects.

* Decorator Pattern
* Façade Pattern
* Flyweight Pattern

## Decorator Pattern
Used to add new functionality to an existing object, without being obtrusive.

```javascript
const Task = function (name) {
  // props
  this.name = name;
  this.completed = false;
}

// methods
Task.prototype.complete = function() {
  this.completed = true
}

Task.prototype.save = function() {
  console.log('saving ' + this.name);
}

// decorated class
const UrgentTask = function (name, priority) {
  // call props from Task constructor (props with values already set ("completed"), and props with dynamic values ("name")
  Task.call(this, name);
  // add new props
  this.priority = priority;
}

// add the propotype methods from Task ("complete" and "save")
UrgentTask.prototype = Object.create(Task.prototype)

// add new proptotype method(s)
UrgentTask.prototype.doingSomething = function() {
  console.log('doing something...');
}

// edit the "save" prototype method, change its behavior or do something different, then call original method after
UrgentTask.prototype.save = function() {
  this.doingSomething();
  Task.prototype.save.call(this)
}

const urgentTask = new UrgentTask('my task name', 5)
urgentTask.save() // doing something... / saving my task name

/////////////////////////////////////////////////////////////////////////////

// same example using "class" approach
class Task {
  constructor(name) {
    this.name = name;
    this.completed = false;
  }

  complete() {
    this.completed = true
  }

  save() {
    console.log('saving ' + this.name);
  }
}

class UrgentTask extends Task {
  constructor(name, priority) {
    super(name);
    this.priority = priority;
  }

  doingSomething() {
    console.log('doing something...');
  }

  save() {
    this.doingSomething();
    super.save()
  }
}
```
---

## Façade Pattern
Used to provide a simplified interface to a complicated system.

```javascript
// example 1
const toggleClass = (el, cl) => {
  if (el.classList.contains(cl)) {
    el.classList.remove(cl);
  } else {
    el.classList.add(cl);
  }
}

toggleClass($btn, 'active');

// example 2
const addEvent = (el, ev, cb) => {
  if (el.addEventListener) {
    el.addEventListener(ev, cb);
  } else if (el.attachEvent) {
    el.attachEvent(`on${ev}`, cb);
  } else {
    el[`on${ev}`] = cb;
  }
}

addEvent($btn, 'click', () => console.log('clicked'));

// example 3
class CultureFacade {
  constructor(type) {
    this.type = type;
  }

  _findMusic(id) {
    const db = new FetchMusic();
    return db.fetch(id);
  }
  _findMovie(id) {
    return new GetMovie(id);
  }
  _findTVShow(id) {
    return getTvShow(id);
  }
  _findBook(id) {
    return booksResource.find(item => item.id === id);
  }

  _tryToReturn(func, id) {
    const result = func.call(this, id);
    return new Promise((res, rej) => result
      ? res(result)
      : rej({ status: 404, error: 'No item with this id found' }));
  }

  get(id) {
    switch (this.type) {
      case 'music':
        return this._tryToReturn(this._findMusic, id);
      case 'movie':
        return this._tryToReturn(this._findMovie, id);
      case 'tv':
        return this._tryToReturn(this._findTVShow, id);
      case 'book':
        return this._tryToReturn(this._findBook, id);
      default:
        throw new Error('No type set!');
    }
  }
}

const music = new CultureFacade('music');
music.get(3)
    .then(data => console.log(data))
    .catch(e => console.error(e));

```

# Behavioral
* Command
* Mediator
* Observer
