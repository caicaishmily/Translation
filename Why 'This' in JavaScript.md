Why 'This' in JavaScript
While JavaScript is a fun and powerful language, it can be tricky and requires a proper understanding of its underlying principles to mitigate common errors.

In this post, we shall be introducing you to the this keyword, its behaviour and the hard choices behind it. These will be detailed using appropriate examples to better drive home the point.

What is this?
Developers make the mistake of thinking that this refers to the “scope” of the function within which it's encountered. This isn’t true because whenever a function is invoked, it runs in a new execution context until its execution is completed. Each execution context usually references an object and the value of that object translates to the value of this.

Table of Contents
For us to properly understand what this is, we would first need to go over two important concepts:

Context

Call-Site

Context
Commonly in the English language, once a noun is defined in a sentence, subsequent references to the subject noun will be done using a pronoun. Understand?

First Statement: Brad is a good boy. Second Statement: He is a very brilliant boy!

Who is a brilliant boy? Brad... obviously!

This is quite the same in JavaScript, the first statement creates the context, while this functions like 'he' in the second statement and is used to reference the subject in the first statement.

this refers to the context of an executing function.

This would mean that during every function execution, the focus is on the new execution context which is redetermined whenever a function is invoked, and since the context holds a reference to an object, the value of this is updated accordingly.

Related Course: Getting Started with JavaScript for Web Development

It is impractical to assume that the value of this is fixed and always refers to the function’s scope, the value of this is, in fact, dynamic and determined by how a function is called and where it is called:

Different sections in a JavaScript program run in an execution context and maintain that context until there is a switch to a new context. The context of an executing function references the object that invoked it, therefore, a call to this within a function refers to the invoking object.

Since the context is really important to our understanding of this, we need to know how/why the context switches during runtime:

How does the context change?
The current execution context in a JavaScript program usually points to the object that invokes a function and can be switched to another object by invoking the function with another object.

Call-Site
The place where a function is invoked in a JavaScript program is called the call-site. This is also important in determining the value of this because if the function isn’t invoked by any visible object, the current context of the call-site would be responsible for setting the new execution context of the function.

In simpler terms, the value of this represents the context of the initial executing function in a call-site and remains the same in any other call-site an invoking function doesn't provide context.

We can finally define this based on all of the explanations presented above:

The value of the this keyword is dependent on the object upon which it is invoked and not the function itself or the function’s scope.

The call-site of the function is also important in determining what this refers to.

This bindings
There are four bindings that are important in determining the value of this in a JavaScript program. We will look at them in this section.

Default binding
Let’s say we were writing a program and defined a function where we used the this keyword, then we invoked the function in the global scope (outside of any function) of the program. What object do you think the this keyword would reference runtime? Let’s see:

let randomFunction = () => {
  this.attribute = 'This is a new attribute to be created on the invoking object';
  console.log(this);
}

randomFunction();
Good thinking! This references the global object in the output below:

In the code above, we created a random function that attaches a new property “attribute” to whichever object invokes it. Because the call-site of the function invocation had its context referencing the global object (this is default), we see that in the output, the console logs a lot of properties that are attached to the global object (Window object) including the property that was created by the random function.

This is the default binding of this in JavaScript. When a function is called without an object, this is by default bound to the current execution context, we will look at the other bindings below.

By default, this is bound to the global object when invoked by a global function.

It is noteworthy that when we are coding in strict mode, this holds the value of undefined in global functions and in anonymous functions that are not bound to any object.

Explicit Binding
Remember how we said that the execution context can be changed by invoking a function on an object? Well, there are explicit ways to easily achieve the same result, let’s look at them briefly:

Bind: This method creates a new function and when invoked sets its 'this' keyword to a provided value (passed as an argument).

Call - This method calls a function and allows us to specify the context its this value should be bound to as an argument. This method accepts optional arguments.

Apply - This function is similar to call() with a difference of allowing us to pass in arguments as an array.

Let’s look at an example:

function randomFunction(){
  console.log(this);
}

let newObj = {
  description : "This is a new Object"
}

console.log(randomFunction.bind(newObj)());
console.log(randomFunction.call(newObj));
console.log(randomFunction.apply(newObj));
In the code above, we have explicitly bound this in the random function to the newObj variable and we can see that the call, bind and apply methods are available on the Function prototype in JavaScript. They are all correctly bound in the output below:

Implicit Binding
When an object defines a method (that calls this) as a property and calls that method somewhere in a program, the this within the method will be implicitly bound to that object. Let’s look at this example:

let newObj = {
  description : "This is a new Object",
  randomFunction(){
  console.log(this);
  }
}

newObj.randomFunction();
This is a straightforward example that complies with our definition of this above; the invoking object is implicitly bound to the method here and it logs the object when invoked:

New Binding
If we have an object constructor and create a new object with it, the new object will have its this value as a reference to the constructor from which it was created. This is the this binding that happens when a new object is created using a constructor. Let’s look at an example:

function newObj() {
  this.description = "This is an object constructor"

  this.randomFunction = () => {
  console.log(this);
  }  
}

let anotherFunction = new newObj()
anotherFunction.randomFunction()
In the snippet above, we defined a new constructor function and gave it description and randomFunction property and method respectively. We created an instance with the anotherFunction variable and invoked its randomFunction method, here’s the output:

Here, the logged object has the description property that was defined on the object constructor to prove that this references the constructor function.

This and That
The takeaway from everything that has been shown above is:

Wherever this is defined in a function, it usually isn’t assigned a value until an object invokes the containing function.

You might think that the paragraph above is all you would ever need to refer to when you work with this but there are scenarios where this behaves in a weird way (cus JavaScript).

Let’s look at a code snippet where we define a closure that references this in JavaScript:

let newObj = {
  description : "This is a new Object",

  randomFunction(){
    let a = 1

    return function() {
      console.log(this);
    }
  }
}

newObj.randomFunction()();
You would reasonably expect that the closure returns the value of this as the object that invoked randomFunction but let’s see what the output says:

Wait, what? How does this refer to the global object here? Wasn’t the outer function called by the newObj object? Shouldn’t that have switched the execution context and updated the this reference?

These are all valid questions, however, here’s something to note:

Closures cannot access their outer function’s this value by calling this within themselves.

This is because the this value of a function is only accessible by the function within which it is encountered and not its inner functions. Hence the this of an anonymous closure will be bound to the global object where strict mode is not being used.

How can we get the preferred behavior?

Here's a trick. Consider this code snippet:

var newObj = {
  description : "This is a new Object",
  randomFunction(){
    var that = this;

    return function() {
      console.log(that);
    }
  }
}

newObj.randomFunction()();
Here, in randomFunction we declare a new variable called that and assign it the value of this. This way, we can reference the this value of the outer function within the closure since the scope of an outer function is always accessible by its inner functions.

Here’s the output:

Great! We have referenced the this value of the outer function in the closure by creating a that variable.

It should be noted that we could have called the variable anything at all but chose that because so many JavaScript developers already find it convenient to call it that. I sometimes choose cat or thanos hehe.

Now you know this... and that.

A better way to handle this without declaring a new variable will be by using an arrow function. We have:

var newObj = {
  description : "This is a new Object",
  randomFunction(){
    return () =>  console.log(this)
  }
}

newObj.randomFunction()();
This produces the same result as declaring that.

Conclusion
We have explored the this keyword in JavaScript clarifying its behavior and use in the process. We also looked at the scenario of closures where the this logic may be elusive and we went over a fitting solution, however, there are situations where this can still prove tricky, the best way to figure it out is to identify which of the bindings is in operation during the function execution and trace back the context carefully. Feel free to leave your feedback and comments. Happy coding!!
