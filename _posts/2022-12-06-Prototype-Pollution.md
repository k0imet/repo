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

## 1. What is prototype pollution?
Prototype pollution is a type of vulnerability that can occur in JavaScript programs when the properties of an object's prototype are manipulated in a way that allows an attacker to inject malicious values into the object. This can allow an attacker to modify the behavior of the program in unexpected ways, and potentially gain unauthorized access to sensitive data or functionality.

## 2. How does prototype pollution occur?
Prototype pollution vulnerabilities can occur in programs that use the Object.assign() or Object.defineProperty() methods to add or modify the properties of an object's prototype. These methods allow developers to specify the properties and values that should be added to the prototype, but they do not perform any validation on the input data. As a result, if an attacker is able to control the input data, they can specify arbitrary property names and values that will be added to the prototype.

## 3. Examples of prototype pollution vulnerabilities
Here are some examples of prototype pollution vulnerabilities:

Example 1
In this example, a JavaScript program uses the Object.assign() method to add a new property to an object's prototype. However, the Object.assign() method does not perform any validation on the input data, which allows an attacker to specify arbitrary property names and values that will be added to the prototype:

```javascript
// Define an object with an empty prototype
let obj = Object.create(null);

// Define an object that contains the properties to be added to the prototype
let data = {
    name: "John Doe",
    age: 30
};

// Use the Object.assign() method to add the properties from the data object to the prototype
Object.assign(obj.prototype, data);
``` 

Example 2
In this example, a JavaScript program uses the Object.defineProperty() method to add a new property to an object's prototype. However, the Object.defineProperty() method does not perform any validation on the input data, which allows an attacker to specify arbitrary property names and values that will be added to the prototype:

```javascript
// Define an object with an empty prototype
let obj = Object.create(null);

// Use the Object.defineProperty() method to add a new property to the prototype
Object.defineProperty(obj.prototype, "name", {
   value: "John Doe"
});
``` 


## 4. How to prevent prototype pollution
To prevent prototype pollution vulnerabilities, it is important to validate user input and ensure that it conforms to the expected format and values. This can be done using a variety of techniques, such as by using regular expressions to match the input data against a predefined pattern, or by using a schema to validate the structure of the input data.

Additionally, it is important to avoid using the Object.assign() and Object.defineProperty() methods to add or modify the properties of an object's prototype, unless the input data can be trusted. Instead, you should use other methods, such as the Object.defineProperties() method, which allows you to specify multiple properties and values at once, and provides built-in validation to ensure that the input data is safe. By using these methods, you can help protect your program against prototype pollution attacks.

## 5. Conclusion
Prototype pollution is a type of vulnerability that can occur in JavaScript programs when the properties of an object's prototype are manipulated in a way that allows an attacker to inject malicious values into the object. This can allow an attacker to modify the behavior of the program in unexpected ways, and potentially gain unauthorized access to sensitive data or functionality.

To prevent prototype pollution vulnerabilities, it is important to validate user input and avoid using the Object.assign() and Object.defineProperty() methods to add or modify the properties of an object's prototype, unless the input data can be trusted. Instead, you should use other methods, such as the Object.defineProperties() method, which allows you to specify multiple properties and values at once, and provides built-in validation to ensure that the input data is safe.

By following these best practices, you can help protect your JavaScript programs against prototype pollution attacks.
