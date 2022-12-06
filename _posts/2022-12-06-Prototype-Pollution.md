---
layout: article
title: Prototype Pollution
tags:  web JS
---

# Prototype Pollutions

    1. What is prototype pollution?
    2. How does prototype pollution occur?
    3. Examples of prototype pollution vulnerabilities
    4. How to prevent prototype pollution
    5. Conclusion

## What is prototype pollution 

Prototype pollution is a type of vulnerability that can occur in JavaScript applications. It occurs when an attacker is able to manipulate the prototype of an object, allowing them to add or modify properties on that object in a way that was not intended by the application's developer. This can potentially lead to security vulnerabilities, as the attacker may be able to access sensitive information or execute arbitrary code on the victim's system.


## How does prototype pollution occur

Prototype pollution occurs when an attacker is able to inject malicious data into an application's input, such as through a URL parameter or a form field. This data is then processed by the application, and used to modify the prototype of an object. 
For example, if an application expects a user's name as input, but an attacker is able to provide a string with a special format, such as "name.constructor.prototype.secret=42", the application may inadvertently modify the prototype of the "name" object to include a new property called "secret" with a value of 42. This can potentially allow the attacker to access sensitive information or execute arbitrary code on the victim's system.

## Examples of Prototype pollution vulnerabilities


There are many different examples of prototype pollution vulnerabilities. For instance, an attacker may be able to inject malicious data into an application's input in order to modify the prototype of an object and gain access to sensitive information. For example, an attacker may be able to modify the prototype of the "window" object in a web application, allowing them to access the victim's cookies or other sensitive data. 
Alternatively, an attacker may be able to modify the prototype of the "Object" object, allowing them to add or modify properties on any object in the application. This could potentially allow the attacker to execute arbitrary code on the victim's system.

## How to prevent prototype pollution

To prevent prototype pollution, it is important to properly validate and sanitize all user input to ensure that it conforms to the expected format. This can be done by using input validation and sanitization libraries, such as the "validator" module in Node.js. Additionally, developers should avoid using user-provided data to modify the prototypes of objects, as this can create opportunities for attackers to inject malicious data.

## Conclusion 

In conclusion, prototype pollution is a type of vulnerability that can occur in JavaScript applications. It occurs when an attacker is able to manipulate the prototype of an object, potentially allowing them to access sensitive information or execute arbitrary code on the victim's system. To prevent prototype pollution, it is important to properly validate and sanitize user input and avoid using user-provided data to modify object prototypes. By taking these precautions, developers can help protect their applications from this type of attack.

Prototype pollution is a type of vulnerability that can occur in JavaScript applications. It occurs when an attacker is able to manipulate the prototype of an object, allowing them to add or modify properties on that object in a way that was not intended by the application's developer. This can potentially lead to security vulnerabilities, as the attacker may be able to access sensitive information or execute arbitrary code on the victim's system.
Prototype pollution occurs when an attacker is able to inject malicious data into an application's input, such as through a URL parameter or a form field. This data is then processed by the application, and used to modify the prototype of an object. For example, consider the following code:

```javascript
const userInput = 'name.constructor.prototype.secret=42';
const name = userInput;
console.log(name.secret);
```

In this code, the userInput variable is set to a string that contains a special format, with <b>.constructor.prototype</b> followed by a property name and value. When this string is assigned to the name variable, the application inadvertently modifies the prototype of the name object to include a new property called secret with a value of 42. This can potentially allow the attacker to access sensitive information or execute arbitrary code on the victim's system.
There are many different examples of prototype pollution vulnerabilities. For instance, an attacker may be able to inject malicious data into an application's input in order to modify the prototype of an object and gain access to sensitive information. For example, an attacker may be able to modify the prototype of the window object in a web application, allowing them to access the victim's cookies or other sensitive data. Here is an example of how this might work:

```javascript
// Attacker's input
const userInput = 'window.constructor.prototype.secret=42';

// Inject attacker's input into the application
const name = userInput;

// Access sensitive information
console.log(window.secret); // 42
```
Alternatively, an attacker may be able to modify the prototype of the Object object, allowing them to add or modify properties on any object in the application. This could potentially allow the attacker to execute arbitrary code on the victim's system. Here is an example of how this might work:

```javascript
// Attacker's input
const userInput = 'Object.constructor.prototype.secret=function(){ alert("Prototype pollution!"); }';

// Inject attacker's input into the application
const name = userInput;

// Execute arbitrary code
secret(); // "Prototype pollution!"
```

To prevent prototype pollution, it is important to properly validate and sanitize all user input to ensure that it conforms to the expected format. This can be done by using input validation and sanitization libraries, such as the validator module in Node.js. Here is an example of how this might work:

```javascript
const validator = require('validator');

// Validate user input
if (validator.isAlpha(userInput)) {
  const name = userInput;
  console.log(name);
} else {
  // Handle invalid input
  console.log('Invalid input!');
}
```
In this example, the validator.isAlpha() method is used to check if the userInput variable contains only alphabetical characters. If it does, the

In addition to input validation and sanitization, developers can also prevent prototype pollution by avoiding using user-provided data to modify the prototypes of objects. This can help to reduce the opportunities for attackers to inject malicious data and exploit prototype pollution vulnerabilities. Here is an example of how this might be done:

```javascript
// Do not use user-provided data to modify object prototypes
const name = userInput;
console.log(name);
```

In this example, the userInput variable is not used to modify the prototype of the name object. This can help to prevent prototype pollution, as the attacker's malicious data will not be processed by the application and will not affect the prototype of the name object.


To summarize, prototype pollution is a type of vulnerability that can occur in JavaScript applications. It occurs when an attacker is able to manipulate the prototype of an object, potentially allowing them to access sensitive information or execute arbitrary code on the victim's system. To prevent prototype pollution, it is important to properly validate and sanitize user input and avoid using user-provided data to modify object prototypes. By taking these precautions, developers can help protect their applications from this type of attack.
