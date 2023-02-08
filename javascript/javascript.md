# Javascript

Chrome developer tools:
cmd + opt + j
or right click and choose inspect, then switch to console tab

---
**Javascript**
is a high-level object-oriented, multi-paradigm programming language: a programming language is a tool that allows us to write code that will instruct a computer to do things. High-level means we don't have to worry about complex stuff like memory management while it runs our program. In JS there are a lot of abstractions over all these small details that we don't need to worry about. Object-oriented means is mostly based on the concept of objects for storing most types of data. Multi-paradigm means it is so versatile and flexible that we can use all kind of different programming styles such as imperative and declarative programming.

---
**The role of JS in WEB development**

Three core technologies of the web: 
* HTML -> content of page (text, button, images) -> NOUNS like `paragraph` <p></p>
* CSS -> presentation of the content (styling and laying out elements on the web page)  -> ADJECTIVE like `paragraph text is red` p{colour: red}
* JS -> real programming language of the internet and allow programmers to add dynamic and interactive effects on a web page -> VERB like `hid the paragraph` p.hide()
To create beautiful, dynamic and interactive websites or web applications.

There is nothing you can`t do with JS (well, almost..)
* JS allows building entire web application in the browser. Modern JS libraries and frameworks are REACT, VUE, ANGULAR. These are the tools that make writing modern web applications much easier and faster. These frameworks are 100% based on JS, therefore, you should learn JS first.
* JS language and the web browser are two separate things. i.e. JS can also run outside of web browsers, for instance on a web server using a very popular technology called NodeJS. This allows us to create backend applications.
* When we run JS in the browser, we create frontend applications.
* We can also use JS to create native mobile applications as well as native desktop applications for any phone or computer operating systems, that`s because of modern tools like REACT NATVE, IONIC and the ELECTRON

JS versions: There has been a huge update to JS on 2015 called ES6 (=ES2015) which was the release after ES5. ES stands for ECMAScript. After 2015, there is now a yearly release of new features. All these new release from ES2015 is called modern JS.

---
`;`

closing a line. It is not mandatory to use, but it makes the code clean.

* camelCase is the typical writing convention in JS: firstName
* The only two syntax that are allowed to be used in variables name are: `$firstName` and `_firstName`
* We should not start a variable with uppercase letter (it is not forbidden but better not to used).
* Variables that are all in uppercase are reserved for variables that we know will not change: PI = 3.1415;
* Use double quotes or single quotes for text/strings
* JS has dynamic typing, meaning that we do not have to manually define data type of the value stored in a variable. Instead, data types are determined automatically.

`//`

comment

* command + shift + 7 (i.e. command + /)

multi-line comment
```
/*
*/
```

---
**Linking a JS file**

Usually JS is attached to web pages especially when we are building frontend applications, therefore, we always need an html file.  Open the file with Chrome, and your code will be executed.

```
<head>
<script>
This is where we write our JS code
</script>
</head>
```
You can put all your code logic into separate file and then link the code and html. Usually, this is put at the end of body tag
```
<body>
<script src=“script.js”></script>
</body>
```
To explicitly see results in console: `console.log(var1, var2, var3)`

There is a bug in JS that they haven't fixed due to legacy reasons: `typeof null` -> returns an object

---
**values and variables**

declare a variable (create real variable in your computer memory): `let variable = value`

You can declare multiple variables at the same time: `let x, y;`

---
**Data types**
In JS variables are either object or if they are not objects, they are primitive. There are 7 primitive data types:
1. Number (used for decimals and integers)
2. string
3. boolean (either true or false)
4. value taken by a variable that is not yet defined, i.e. variable is defined without assigning a value (let children;)
5. null (also means empty value)
6. symbol (ES2015) value that is unique and cannot be changed
7. BigInt (ES2020) larger integers that are too big to be represented by integer type

`typeof` an operator to show the type of a variable. `typeof x`. The first time you define a variable, we need to use the `let` keyword, but to later change the value of the variable, we just assign
```
let x = 10
x = 5
```

---
**var, let, const**

Three different ways to declare variables in JS. `let` and `const` were introduced in ES6, so they are modern JS while `var` is the old way of declaring JS.
* we use `let` to declare variables that can change during the execution of the program. An empty variable is defined with this method so that it can later get its value -> called reassigning a variable or mutating the variable
* `const` is used to declare variables that won't change, i.e. JS won't allow it to change. An empty variable is not allowed to have const data type -> called an immutable variable
* `var` should be avoided. It works pretty much same as `let`, but under the hood the three ways are different.

A common rule of thumb is to always use const and only use let if you are sure your variable should change. In JS, it is possible not to declare variables and just define a variable name and assign to it. This works, but it is a terrible idea, because it doesn't create a variable in the so called scope, instead JS will create a property in the global object.
```
lastName = “Kanafi”
console.log(lastName)
```

---
**operators and precedence**
```
x += 10 -> x = x + 10
x *= 10 -> x = x * 10;
x ++; -> x =  x +1
```

search for `mdn operator precedence`, mdn stands for Mozilla Developer Network, and refer to this table for the precedence. 

---
**string and template literals**
template literal (introduced in ES6) can assemble multiple pieces into one string. The symbol for template literal is back ticks. Note that you can use back ticks also for normal strings.
```
newVariable = `I am ${firstName}, a ${year} old ${job}`
```

Another feature of template literal is that it makes writing multi-line string easy
```
console.log("string with \n\ 
multiple \n\
lines";)

// with back ticks
console.log(`string with
multiple
lines`)
```

---
**i/else statements**

---
**References**
1. [The Complete JavaScript Course 2023: From Zero to Expert!](https://www.udemy.com/share/101Wfe3@Arw3DBiQpIXETJumG-VPjLRhf8Tq_1Bt2JhZRSGj_m_HL7w2ao0IcHMxE0R_XueHfQ==/)