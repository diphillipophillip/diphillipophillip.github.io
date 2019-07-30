---
layout: post
title:      "Scope, Hoisting, and this"
date:       2019-07-30 22:28:30 +0000
permalink:  scope_hoisting_and_this
---



After doing my assesment for my javascript/rails app, it was easy to tell that I still had a lot to learn! It's so easy to not really understand things and not understand what they are doing while making an app. You get into a groove where things just seem to happen. I think it is okay to not fully understand everything, after all we only have so much time to work on something before we have to learn something new. But, I think it is essential to make sure the fundamentals and building blocks are engraved in your mind. This blog post is going to be all about Scope, Hoisting, and the "this" key word. 

For starters I want to take a look at some problems, and basically break down what is happening, and why it is happening. 

When your Javascript code is being processed, in the first iteration, before actually executing it line by line, all the variable and function declarations are detected. Then they are created in memory and space is allocated for them. Only after that, the code is executed line by line. The thing is that only declarations are hoisted, not initializations. Declarations meaning something like this 

var john 

and initialization like this 

john = 'John Doe'.

Looking at the code below, we see that the console is logging the value for var a as "second Value". So what is happening in this instance. Because we are using the var keyword, the declaration of "a" is being hoisted, and stored in memory. The reason that "second Value" is the value for var a however, is because of the scope. When it comes to the var keyword if you declare it inside of a function body, its scope is the whole function's body. Therefore, the value gets changed to "second Value" when the console logs it. Of course, this can cause some serious issues so it is best to remember that the smaller the scope is, the smaller is the risk that some code somewhere else will mess with your variables. 



function scopey() {
  var a = "first Value"

  if (true) {
    var a = "second Value"
  }

// what will each statement log to the console?
  console.log("a (var) is,", a)
  
}

scopey()







Lets take a look at this next bit of code and break down what is happening. When we console.log(b), and console.log(c). Both values will come out to "first Value". The reason this is happening is because scope and hoisting affect let and const differently than they affect var. Lets talk about the differences. 

One of the differences is that their scope is not the entire function but rather only the enclosing block, or some section of code wrapped in {} braces. So, looking at the bit of code below.


function scopey() {
  let b = "first Value"
  let c = "first Value"

  if (true) {
    let b = "second Value"
    const c = "second Value"
  }

// what will each statement log to the console?
  console.log("b (let) is,", b)
  console.log("c (const) is,", c)
}

scopey()


it is easy to tell that the if statement with the values of "second Value" will not apply to where we are console.logging. Because of the way let and const work in scope, the value for both will be "first Value" and we do not have access to the if statement. The only way we could make b = "second Value" and c = "second Value" would be if we put 

 console.log("b (let) is,", b)
 console.log("c (const) is,", c)
 
 within the if statement. 
 
 let and const are pretty similiar but do have a difference. Once variable declared using const keyword is assigned a value, you cannot reassign it. You also need to initialize the variable immediately when declaring it.  
 
 
 Lets take a look at this next problem 
 
 
 
 function sayName(){
  console.log('My name is', theName)

  var theName = 'Jane Doe'
}

sayName()


What do we think is going to happen when we invoke sayName? Well, in this case, we are going to get the statement 'My name is, undefined'. The reason we are getting undefined is simple when we think about how javascript reads the information. That, and hoisting. Upon the initial sweep of this information, it sees the var key word, and hoists the declaration to the top. Meaning, it makes a spot in memory for the declaration, (var theName), but what about the initialization? ('Jane Doe').  Javascript will not hoist the value, only the declaration. So, after the first sweep of the information, there is a variable declaration "above" the console.log. But, because javascript does not also hoist the initilization, there is no value set to it. So when javascript is executing the code after the initial sweep, we end up with a varaible that has no value, therefore getting an undefined. 
 


Lets take a look at this next bit of code and analyze what will happen. 


function sayName(){

  let theName
  console.log('My name is', theName)

  theName = 'Jane Doe'
}

sayName() 



Let works a little differently than var does when it comes to hoisting. Different, as in it does not and you can only access let directly after it is declared. So what will happen when we invoke sayName? Well, we will get 'My name is, undefined'.

The only reason we are getting undefined as oppose to a reference error is because in case of let/const, the initialization to undefined does not happen until the line where the declaration actually happens. And only if there is no assignment immediately. So when javascript is running this code, it is looking at "let theName" it is seeing undefined, and is not hoisting the value of 'Jane Doe' because values do not get hoisted. 


Let's make a slight variation do the code above and see what happens. 


function sayName(){
  
  console.log('My name is', theName)

  let theName = 'Jane Doe'
}

sayName()


what do we think we will get when we run this code? Well, we will get a referenceError that sayName is not defined. Looking at this code, we can see exactly where it is defined, but the issue is, javascript does not hoist the let keyword. So, by the time javascript is console.logging, there is nothing saved in memory, and it has not idea what theName is. Therefore, we get the referenceError. 

The only way we can get our console to log 'My name is Jane Doe' is if we do this. 


function sayName(){
  let theName = 'Jane Doe'
  console.log('My name is', theName)

}

sayName()

In this example, the variable is declared before the console is logged, and there is a value.





Lets look at this example 

function sayName(){
  console.log('My name is', theName())

  var theName = function(){
    return 'Jane Doe'
  }
}
sayName()


what do we think will happen when sayName is invoked? Well in this case, we will get a TypeError saying that theName is not a function. Why is that? Well, because we are using the var keyword. theName gets hoisted and saved in memory as a variable. However, we are still trying to invoke theName as if it is a function. As we know, only the declaration gets hoisted. So by the time we are console.logging, we are invoking a variable. What will happen if we do this? 


function sayName(){
  console.log('My name is', theName)

  var theName = function(){
    return 'Jane Doe'
  }
}
sayName()


In this case, the same thing is happening in regards to hoisting, but when we are console.logging, we are not invoking theName as a function. So in this case, we will get 'My name is undefined'. 


I think that is enough examples in regards to scope and hoisting! Now it is time to talk a little bit about 'this'. 
Let's take a look at this problem and break it down a little. 



this.character = 'Daisy'

const game = {
  character: 'Mario',
  details: {
    character: 'Yoshi',
    characterName: function() {
      return this.character
    },
    arrowCharacterName: () => this.character
  }
}


Looking at the initial "this.character", we see there it set to the value of 'Daisy'. Well, in this context, 'this' is refering to the entire window, and is in global scope. So when we console.log(this), we will see all of the window information along with 

character:  'Daisy' 

at the bottom. 

Looking at the details object of the code above, we see a function called characterName that returns this.character. What do we think 

game.details.characterName will return? Well in this case, because we are not invoking it, we will see the entire function. 

When we invoke it 

game.details.characterName() we will see 'Yoshi'. So why is 'this.character' refering to Yoshi. Well, in this case, this is refering to the the object it is being called on and details is the object we are calling characterName on. 



What is going to happen when we call 

game.details.arrowCharacterName() 

Arrow functions work a litte bit differently in regards to the 'this' key word. For starters they do not bind their own this, instead, they inherit the one from the parent scope, which is called "lexical scoping".  Because of this when we invoke the function, 'this' is refering to daisy because it will always inheret from the parent scope, although it is still being called within the details object. (The parent scope is window in this case)

So after all this, we have got the name 'Daisy', and we have got the name 'Yoshi'. How do we get the name Mario? 


Well, we can use the predefined Javascript call() method and run 

game.details.characterName.call(game)

Basically whats happening is .call essentially forces the value of this in whichever function .call is applied on. 

So we still have to use 'game.details.characterName' because thats where the function is. But when we call(game), javascript knows that 'this' is refering to the game object. 


Writing about these concepts is deffinitley difficult, and can take a while. I think I changed the way I wrote this a million times, and I honestly still think it could be written better. But what is important is that you learn what it is you are writing about, and get a great understanding for the material. I can confidently say that I understand scope, hoisting, and 'this' much more after doing this blog post. 



