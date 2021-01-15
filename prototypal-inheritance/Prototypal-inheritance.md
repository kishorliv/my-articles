![Cover image](https://dev-to-uploads.s3.amazonaws.com/i/5ym6zwm1cpfq86mh1q9n.jpg)

Inheritance in general, implies 'copying' certain behaviors from a class to create new objects. For every single object(instance) that is created from a certain class, the attributes and behaviors are 'copied'. Class-based languages like C++, Java, etc implement this mechanism of object creation where each object is a separate 'copy' of a class. In languages like Javascsript, there is no concept of class! Still, Javascript is object oriented. Object creation and manipulation is pretty simple in Javascript. 

However, implementing concepts like inheritance is not so straightforward. Javascript is a Prototype-based language, meaning it does not have classes, it only deals with objects. So, the way inheritance works in Javascript is by the use of links or references to the other objects to use or share behaviors.

### Prototypes
Every object has a hidden property [[Prototype]] that references some object. Double braces in [[Prototype]] means its internal/hidden. Internal properties are not part of ECMAScript(a standard for scripting languages like Javascript), which means we cannot use it as an identifier in our code. But, in this case, we can access the hidden property [[Prototype]] with the help of \__proto__ property(dunder proto) which is available to every object in Javascript. \__proto__ is just a getter/setter for our hidden property [[Prototype]]. 

```js
var person = {
    name: 'Keanu',
    age: 35
}
console.log(person);
console.log(person.hasOwnProperty('name'));
```

If you see the console, you should see a person object with a __proto__ property along with the name and age properties. The code person.hasOwnProperty('name') works and actually returns true even though we have not defined 'hasOwnProperty' method on our person object. This is due to the \__proto__ property which points to 'Object.prototype'. 'prototype' in 'Object.prototype' is just a property of 'Object' constructor function that happens to point to some other object. Remember a function is an object too! Every function in Javascript has a 'prototype' property(when 'new' is used to create objects). Every object in Javascript has a \__proto__ property.  

Basically in Javascript, almost all objects are 'instances' of 'Object' which means all objects inherit from Object.prototype. If a certain property is not found in an object, Javascript tries to look that property in the object referred by \__proto__. Since 'Object.prototype' is pointed by \__proto__ and 'Object.prototype' has a method called'hasOwnProperty', person.hasOwnProperty('name') works fine.By default, every object in Javascript has a \__proto__ which points to 'Object.prototype' which we can see from our example.

This is the core concept of prototypes. If you want to inherit stuff, you need \__proto__ to point to the object whose properties you want. The object
pointed by \__proto__ is called a prototype object or simply a prototype. That's it.

### Inheritance in action
```js
var vehicle = {
    color: 'red',
    run: function(){
        console.log('Vehicle running.');
    }
};

var truck = {
    carryStuff: function(){
        console.log('Truck is carrying stuff.');
    }
};

truck.__proto__ = vehicle; // set [[Prototype]]
truck.run(); // truck runs too!
console.log(truck.__proto__ === vehicle); // obviously!
console.log(truck.__proto__.__proto__ === Object.prototype); // true
```

So, truck.run works because we have pointed \__proto__ to vehicle object. Since, run() is not available in truck, javascript looks up in the 'prototype chain' untill it reaches the end, which is our good old 'Object'.

We used object literals in above example but now lets use constructor functions to simulate inheritance for reusability.

```js
// a 'constructor' function
function Vehicle(color, price){
    this.color = color;
    this.price = price;
    this.run = function(){
        console.log('Vehicle is running!');
    }
  }
```

Since we want 'run' method to be availabe for child 'class', we put it in a prototype property so that it can be inherited.

```js
function Vehicle(color, price){
    this.color = color;
    this.price = price;
}

Vehicle.prototype.run = function(){
    console.log('Vehicle is running!');
}

// child class
function Car(color, price, fuelType){
    this.color = color;
    this.price = price;
    this.fuelType = fuelType;
}

Car.prototype = Object.create(Vehicle.prototype);
```

Object.create() creates a new object with [[Prototype]] set to its first parameter, in this case 'Vehicle.prototype'.

```js
var ferrari = new Car('red', 45465413, 'petrol');
ferrari.run(); //works
console.log(ferrari.__proto__ === Car.prototype); // true
console.log(ferrari.color);
console.log(ferrari.price);
```

We can improve our car class. We are setting color and price in Car which is redundant since Vehicle already does that and we are inheriting from it. So, we can call Vehicle which sets color and price properties but changing 'this' of Vehicle object to 'this' of Car object.

```js
function Car(color, price, fuelType){
    Vehicle.call(this, color, price); // calling Vehicle in Car's context
    this.fuelType = fuelType;
}
Car.prototype = Object.create(Vehicle.prototype);

var ferrari = new Car('red', 45465413, 'petrol');
ferrari.run(); //works
console.log(ferrari.__proto__ === Car.prototype); // true
console.log(ferrari.color);
console.log(ferrari.price);
```

Also, every prototype object has 'constructor' property which points to the constructor function back. This property points to the function it was created from. In our case for 'ferrari' object, its constructor should be 'Car'.

```js
console.log(ferrari.__proto__.constructor); // Oops... Vehicle!
```

So, we should set the constructor property as well.

```js
Car.prototype.constructor = Car;
console.log(ferrari.__proto__.constructor); // Car
```

So, overall our pattern for inheritance looks something like this in ES5.

```js
// parent class
function Vehicle(color, price){
    this.color = color;
    this.price = price;
}

Vehicle.prototype.run = function(){
    console.log('Vehicle is running!');
}

Vehicle.prototype.honk = function(){
    console.log('Peep peep!');
}

// child class
function Car(color, price, fuelType){
    Vehicle.call(this, color, price); // its like calling super()
    this.fuelType = fuelType;
}
Car.prototype = Object.create(Vehicle.prototype);
Car.prototype.constructor = Car;
```

In ES6 inheritance is straightforward due to some syntactic sugar.

```js
class Vehicle{
    constructor(color, price){
        this.color = color;
        this.price = price;
    }

    run(){
        console.log('Vehicle is running!');
    }

    honk(){
        console.log('Peep peep!');
    }
}

class Car extends Vehicle{
    constructor(color, price, fuelType){
        super(color, price);
        this.fuelType = fuelType;
    }

    someMethod(){
        console.log('Just some method of Car');
    }
}

var ferrari = new Car('red', 45465413, 'petrol');
ferrari.run(); // Vehicle is running!
console.log(ferrari.__proto__ === Car.prototype); // true
console.log(ferrari.color); // red
console.log(ferrari.price); // 45465413
```


In this way, with the help of prototypes, we can 'inherit' features for reusability in Javascript. Prototypal inheritance is useful in situations when we want add new properties after the creation of objects unlike classical inheritance. Also, developers can quickly customize objects without thinking much about the structure of the class itself. However, type checking is difficult in prototype based languages and object creation is a bit slower too. The concept of prototypes were built for simplicity and Javascript being a dynamic language, it is a perfect match. 
