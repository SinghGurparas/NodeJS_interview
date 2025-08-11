-----

### 1\. What's the difference between ES6 `class` syntax and prototypal inheritance in JavaScript?

**Answer:**

The `class` syntax in ES6 is **syntactic sugar** over JavaScript's existing **prototypal inheritance** model. It doesn't introduce a new object-oriented inheritance model. Instead, it provides a cleaner, more familiar syntax (like in languages such as Java or C++) for creating constructors and setting up the prototype chain.

* **Prototypes:** This is the underlying mechanism. Every JavaScript object has a prototype, and objects inherit properties and methods from their prototype. We use constructor functions and manually set up the prototype chain (`Object.create()`, `.prototype`).
* **Classes:** This is the modern, cleaner way to do the same thing. You use the `class` keyword, `constructor` method, and `extends` keyword, which behind the scenes, are manipulating the prototype chain for you.

-----

### 2\. Can you explain the concept of a prototype in JavaScript?

**Answer:**

A prototype is an object that other objects inherit from. Every object in JavaScript has a special internal property called `[[Prototype]]` (or `__proto__` in many browsers) that points to its prototype object. When you try to access a property or method on an object, and it can't find it directly on that object, it looks up the prototype chain until it finds the property or reaches the end of the chain (where the prototype is `null`). This is how inheritance is implemented in JavaScript.

-----

### 3\. How would you create a simple object and inherit methods using a constructor function and prototypes before ES6 classes?

**Answer:**

You would typically use a constructor function and then add methods to its `prototype` property.

**Example:**

```javascript
// 1. Define the constructor function
function Car(make, model) {
  this.make = make;
  this.model = model;
}

// 2. Add methods to the prototype
// This is more memory-efficient as all instances share the same method
Car.prototype.startEngine = function() {
  console.log(`${this.make} ${this.model}'s engine is starting.`);
};

// 3. Create a new instance
const myCar = new Car('Toyota', 'Camry');

// 4. Call the inherited method
myCar.startEngine(); // Output: Toyota Camry's engine is starting.
```

-----

### 4\. What are the key keywords used with ES6 classes?

**Answer:**

The main keywords are:

* **`class`**: Used to declare a class.
* **`constructor`**: A special method for creating and initializing an object created with a class.
* **`extends`**: Used to create a subclass (a child class) that inherits from a parent class.
* **`super`**: Used to call methods on an object's parent class. It's most commonly used in the subclass's constructor to call the parent's constructor.
* **`static`**: Used to define static methods or properties that belong to the class itself, not to instances of the class.

-----

### Let's test your understanding

**Question:** Imagine you're building a new `ElectricCar` class that inherits from the `Car` class you saw earlier. How would you use `extends` and `super` to make sure the `ElectricCar`'s constructor properly calls the parent `Car` constructor? Show me a quick code snippet.

Let's move on to other keywords and concepts commonly used with classes and prototypes, specifically focusing on `bind`, `call`, and `apply`.

Here are the most important interview questions related to these topics.

-----

### 1\. What is the `this` keyword in JavaScript, and what are its common pitfalls with classes?

**Answer:**

The `this` keyword refers to the object that is currently executing the code. Its value is determined at **runtime** and depends on *how* a function is called, not where it's defined.

The main pitfall with classes is that when you pass a class method as a callback (e.g., to an event listener or a promise `.then()` block), the `this` context is lost. The method is no longer being called on the class instance, so `this` inside the method becomes `undefined` or the global object (`window` or `global`), leading to errors.

**Example of the pitfall:**

```javascript
class Counter {
  constructor() {
    this.count = 0;
    // The method is defined on the class instance
    this.increment = this.increment.bind(this); 
  }

  increment() {
    this.count++;
    console.log(this.count); // `this` is `undefined` without `.bind(this)`
  }
}

const myCounter = new Counter();
// This will throw an error because the function is called without a context.
// setTimeout(myCounter.increment, 1000); 
```

-----

### 2\. How do `bind`, `call`, and `apply` help manage the `this` context?

**Answer:**

These are three methods available on all JavaScript functions that allow you to explicitly set the value of `this` when a function is executed.

* **`bind()`**: Returns a **new function** with `this` permanently bound to a specific object. The new function can be called later, and it will always have the same `this` value. It's perfect for callbacks.

    ```javascript
    const boundIncrement = myCounter.increment.bind(myCounter);
    setTimeout(boundIncrement, 1000); // This works
    ```

* **`call()`**: Invokes a function immediately, with the first argument being the object to which `this` should be bound. Subsequent arguments are passed to the function individually.

    ```javascript
    function greet(greeting, name) {
      console.log(`${greeting}, my name is ${name}. I am a ${this.job}.`);
    }

    const person = { job: 'developer' };
    greet.call(person, 'Hello', 'Alice'); // Output: Hello, my name is Alice. I am a developer.
    ```

* **`apply()`**: Invokes a function immediately, similar to `call()`, but accepts the function's arguments as an **array-like object**.

    ```javascript
    greet.apply(person, ['Hi', 'Bob']); // Output: Hi, my name is Bob. I am a developer.
    ```

-----

### 3\. What is the main difference between `call` and `apply`?

**Answer:**

The key difference is how they handle function arguments:

* **`call`** takes arguments individually: `func.call(thisArg, arg1, arg2, ...)`
* **`apply`** takes arguments in an array: `func.apply(thisArg, [arg1, arg2, ...])`

Both methods execute the function immediately. In most modern code, `call` is often preferred for readability unless you have an array of arguments that you need to pass.

-----

### Let's test your understanding

**Question:** Why might you choose to use `bind()` instead of `call()` or `apply()`? Give me a specific scenario.
