# Destructuring

In ES6, a new syntax was introduced to extract datas from a data structures and break down it into smaller parts, and this new syntax was known as **Destructuring**. Let's look into the what, why, how and ahaas of destructuring.

## Why destructuring?

In Javascript we heavily use data structures like `array`, `object` and it is extremely common, that we extract values from the data structure and assign to other variables. And since this is such a common operation in ES6, the destructuring syntax was included so that the extracting of values from a data structure or nested data, can be easy. Before we dig into how destructuring can be done, lets look into an example of what we will be dealing with.

````javascript
var obj = {
  'first' : 'Anirudh',
  'last' : 'Modi'
};

var firstName = obj.first,
    lastName = obj.last;

console.log(`Hello ${firstName.toUpperCase()} ${lastName}`);    // Hello ANIRUDH Modi
````

The above example is a pretty common example which we all have encountered, where we extract values from `object` and assign it to variables and use it further according to our needs, now for a more complex and nested object, it might become a little tedious to extract these values. Let's look at another example type of data where we end up extracting values.

````javascript
var arr = [1,2];

var firstNum = arr[0],
    secondNum = arr[1];

console.log(secondNum,firstNum);            // 2,1
````

Again, extracting values from array are pretty common, and every time we need to extract a value either from an `array` or an `object` we end up manually assigning index and property name for extracting the value. Another issue, we faced was handling return values! If a function return an `object` or an `array` as its return value, we had to forcefully first assign the return value to a temporary variable and then use the temporary variable to extract the desired values.

````javascript
function foo()
{
  return {
    'first' : 'Anirudh',
    'last' : 'Modi'
  }
}

var obj = foo();

var firstName = obj.first;

console.log(`Hello ${firstName.toUpperCase()}!`);    // Hello ANIRUDH!
````

So the main point of why destructuring was introduced is to make the extraction of values from data structure in a more natural and cleaner way. Let's look into an example of this can be done through destructuring, and how destructuring works!

**Destructuring way**

````javascript
var obj = {
  'first' : 'Anirudh',
  'last' : 'Modi'
};

var { 'first' : x, 'last' : y } = obj;

console.log(x, y);    // Anirudh Modi

var arr = [1,2];
var [a,b] = arr;

console.log(a,b);                   // 1,2
````

We can see, that we didn't use the dot notation `obj.first` to extract the value from the object `obj`, we used a syntax which is similar to how an object literal was defined, and also to extract value from `arr` we didn't use the braket notation `arr[0]`, we used a syntax similar to how an array is defined!! These are known as patterns, which determines how a data needs to destructured! Let's dive more into what patterns are and how is destructuring linked to it.

## How does destructuring works!

As mentioned above, any data which needs to be destructured is decided by the pattern used. In simple terms, while destructuring the pattern which appears on the left hand side of `=` operator during an assignment, is flipped and the same type of pattern is expected to be present on the data to be destructured on the right hand side of the `=`. Let's look at what is happening in the above examples. So, in the above example when we used the `array` notation on the LHS of `=`, it will essentially check whether the value on the RHS is an `iterable` or not, by checking whether, the value has a method whose key is `Symbol.iterator` that returns an object, and when a `object` pattern is used it will convert the RHS value into an Object using `ToObject()`, and essentially expects that the `object` on RHS, will have the properties mentioned in the LHS, in this case the properties were `first` and `last`. The type of the properties on the RHS could be anything which helps us in nested destructuring, which we will discuss later. Which essentially leads us to two kinds of patterns offered to destructure!

1. Object assignment pattern - `{}` used to destructure if value can be converted into `object`
2. Array assignment pattern - `[]` used to destructure any kind of `iterables`

### Object assignment pattern

An Object assignment pattern will be used if in LHS of `=` is an object literal `{}`, in that case the value on RHS of `=` will be first tried to convert into an `object` using `ToObject`, if the it returns `false` then a `TypeError` will be thrown, else the properties name named on the LHS will be looked up in the converted `Object` of value. Let's look into it what it means.

````javascript
var obj = { a : 1, b : 2 };
var { a : x, b : y } = obj;

console.log(x,y);   // 1,2
````

So what happened in the above example? It first found that an `{}` object pattern is used, then it checked internally whether `obj` is an `Object` or not, and when it found that `obj` is an `Object`, it started to look for properties `a` and `b` within the `obj` (known as the **source-property**), and when it found the `properties`, it first checks if any **variable-target** is present or not, in this case the target was `x` and `y`, when it found that a target was present, it checked whether the **variable-target** is an *object literal* or an *array literal* or *assignment target* (used to destructure nested object), and when it found it is *assignment target* it assigned the value of the properties `a` and `b` to the variable `x` and `y`.

The **varibale-target** is an optional field, and through this destructuring offers a short-hand syntax also.

````javascript
var obj = { a : 1 , b : 2};
var {a, b} = obj;

console.log(a,b);   // 1,2
````

In the above example, we just omitted the `:x` and `:y` part, yet it seemed to work! Well, here what happened was when the properties `a` and `b` were found, during the assignment of values it found that was no **variable-target** , hence it created two variable with same name as the properties and assigned the value to it.

Earlier, I mentioned that the Object assignment pattern, converts the value on the RHS of `=` to an object, so, you must be wondering what all values can be accepted? The `ToObject` method will throw `TypeError` if the value being passed is `undefined` or `null`, if it is a `string`,`number`,`symbol`,`boolean` then it will return the corresponding wrapper object which is `String`,`Number`,`Symbol`,`Boolean`, if it is a `object` which means everything else, because in `Javascript` everything is an object the it will return the `object` itself.

> For further informtation read :
* [More about ToObject](http://www.ecma-international.org/ecma-262/6.0/#sec-toobject)
* [Primitive data type in Javascript](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)
* [Object in Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Objects)

Let's see what the above statement means, and how, it can effect our destructuring!

````javascript
var obj = "This";

var { length : a } = obj;
console.log(a);  // 4
````

Now, mentioned earlier that on RHS it will convert into an `Object` using `ToObject`, and finding a string it will return the `String` wrapper object, since the, object pattern will use keys, it will try to find the key `length` within the `String` object, and we know the `String.length` stores the length of the string, in this case `4`, hence, it never throws any error.

````javascript
var { length : a } = null;          // TypeError
var { length : a } = undefined;     // TypeError
````

Since, `null` and `undefined` does not coerce to an `Object` a `TypeError` will thrown.

> The above examples were just to show how the object pattern works internally, and it is not much of a sense to destructure the native object like this.

Let's look into other syntax possible of using object pattern. Till now we have been using variable declaration, however destructuring is not just limited to decalring varibales, we can use to assign value to existing variables also, but We cannot start a statement with curly braces `{}`, it will be `SyntaxError`, we need to wrap it a parentheses `(..)`

````javascript
var a, b;

{ x : a, y : b } = { x : 1, y : 2 };    // this will lead to a syntax error

({ x : a, y : b } = { x : 1, y : 2 });  // this is ok
````

Object Destructuring is not just limited to using `var`, we can use `let`, `const` in the same way we are using `var`.

You must wondering, what happens, if the property name mentioned in destructuring is not present in the property list of RHS value? In that case, it assigns a value of `undefined`.

````javascript
var obj = {a : 1};
var {a, b} = obj;

console.log(a,b);   // 1,undefined
````

The order in the which we are destructuring really does not matter, as internally it will find the property within the object. So, there is no harm if we change the order of property name while destructuring the object.

````javascript
var obj = {a : 1 , b : 2};

var {b : y , a : x} = obj;

console.log(x, y);        // 1,2
````

### Array assignment pattern
Now, that we have looked how object pattern is used to destructure an object, ES6 has offered another pattern, which can be used to destructure any `iterable`. If on the LHS of the assignment operator it finds a array literal `[]`, then the array pattern will be used to destructure. The array pattern works on `iterable`, let's look on how it is done internally, it will take the RHS value, and before even starting to destructure, it will first check whether that value is an `iterable` or not, and to check that, it will check whether the value has a method whose key is `Symbol.iterator`, and the method must return an `object`. If it does so, it is an `iterable`. And, then it uses the `iterator.next` property to extract the value from the `iterable`, and the goes on to assign that value to the **variable-target** in LHS. The list of built-in `iterable` in JavaScript are :
1. `String`
2. `Array`
3. `Typed Array`
4. `Map`
5. `Set`

Other notable `iterables` which are `iterables` are the `arguments` object, the `nodelist` from `document.getElementByClass`.

__NOTE : The `nodelist` is not implemented as an `iterable` in all browser.__

Anything apart form this will throw an `TypeError`, which means `Object`, `numbers`, `boolean`, `null`, `undefined` values and any other values whicha re not `iterable`, are bound to throw an `TypeError`. Let's look at an example.

````javascript
var [a, b] = [1, 2];
console.log(a, b);    // 1, 2

var [c, d] = "red";
console.log(c, d);    // r, e

var set = new Set([1, 2]);
[e, f] = set;
console.log(e,f);     // 1, 2

// Below examples will throw TypeError
var [x, y] = {};              
var [x, y] = 1;               
var [x, y] = null;            
var [x, y] = undefined;       
````

What happened, is the array on the RHS, is first checked is an `iterable` or not, if `true`, then it used the `iterator.next` method, to extract the first value from the `array`, and assigned to the variable `a`, again it continued to fetch another value and assigned to `b`.

Since, the array pattern checks whether the value to be destructured is an `iterable` or not, we can use it to destructure, a `generator function`, which return an `iterator`, and whose values can be extracted sing the `iterator.next`, and hence, the same rule can be applied to destructure.

````javascript
function *createIterator() {
    yield 1;
    yield 2;
    yield 3;
}

var [a,b,c] = createIterator();
console.log(a,b,c);                 // 1 2 3
````

With Array pattern we can omit a value also, and that can be done by creating holes in LHS array literal syntax `[]`, lets look what it means.

````javascript
var arr = [1 , 2 , 3];

var [ , , c] = arr;

console.log(c);               // 3
````
The above code gave an output of `3`, now if we look closely we can see that, we used  `,` in the array, and never gave any variable name, by doing so we created a hole in the array, and while destructuring the value at `0` position of the original array, will not be assigned to any variable. This can be used to extract only a specific value from an `array`. Equivalent to, `var c = arr[2]` without destructuring.

We can also use array pattern to create a new-array from the original array. This can be achieved using the rest parameter `...` while specifying the pattern of destructuring the original array.

````javascript
var arr = [1 , 2 , 3 , 4 , 5];

var [a , b , ...c] = arr;

console.log(a , b , c);         // 1 , 2 , [3 , 4, 5]
````

What happened above is, while destructuring, it extracted the first value and second value and assigned it to corresponding variable declartion, while destructuring it found that a rest parameter `...`, is found in destructuring, it will take all the value from the original array, except the first and second element, and push into the `c` array.

> NOTE : The rest parameter `...` must be the last parameter, which means this is an invalid syntax `var [a, b, ...c, d] = arr;` because a variable `d` came after the rest parameter `c`.

## Nested destructuring

We are not limited to destructure, a single level of nesting object literal/ array, we can destructure any level of nested object/array, let's see how it can be done.


#### Nested Object
````javascript
var obj = {
  a : {
    c : 1,
    d : 3
  },
  b : 2
};

var {
  a : {
    c : x,
    d : y
  },
  b : z
} = obj;

console.log(x,y,z);     // 1,3,2
````
So how did the nesting occured? It first found that on the LHS an object pattern is used, then it watched that a property `a` is being referred, however, the value of the `a` is not assigned to a varibale instead an *object literal* is used, hence it will be sent for further destructuring, where it will take the value of `a`, coerce it to a `Object` and then again, it check that property `c` is being referred, however this time, a **varibale-target** is present, and hence it's value will be assigned to variable `x`. And this pattern can continue.

Since, the object is being coerced to an `Object` we will get a `TypeError`, if we change the value of `a` to `null`/`undefined`.

````javascript
var obj = {
  a : null,
  b : 2
};

var {
  b : z,
  a : {
    c : x,
    d : y
  }
} = obj;

console.log(x,y,z);       // TypeError

var obj2 = { a : null };

var {a : willRun } = obj2;

console.log(willRun);     // null Just to show that since, here it is an assignment, the value is assigned,
                          // it wont throw an error, but since, in earlier the null was being tried to
                          // convert into an object an error was thrown.
````

#### Nested Array

````javascript
var arr = [1, 2, [3, 4], 5];

var [a, , [b, c], d] = arr;

console.log(a, b, c, d);      // 1 3 4 5
````
So how did the nesting occured? It first found that on the LHS an array pattern is used, then it watched that the first element of the array need to be assigned to `a`, then it checked that the second element need not assigned to anything, and third element it found that an array literal is used, and hence the array is broken down and assigned to the variable `b and c`. Again, while using a nested array pattern if the value is `null` or it is not iterable in the original array it is throw a `TypeError`.

````javascript
var arr = [1, null, 2];

var [a, [b], c] = arr;      // TypeError
var [[a], b, c] = arr;      // TypeError
````

#### Mixed nesting
Till now we have only seen scenarios where either arrays are destructured or objects, however, we must know, that destructuring is not just limited to a single patter, just like during creating an object literal we can have arrays in it, with `n-level` of nesting, and vice versa, these patterns exists so that, we use nested combination to destructure a data which is with multiple pattern. lets look at an example to understand.

````javascript
var obj = {
  a : 1,
  b : [2, 3]
};

var {
  a : x1,
  b : [x2, x3]
};

console.log(x1, x2, x3);    // 1 2 3
````

We can see in the above example how we destructured an `object` and then an `array` within the `object`, similarly, we can also destructure an `object` within an `array`, and the same rules could be applied like we were applying on the `array pattern` and `object pattern` individually

````javascript
var arr = [1, 2 , {a : 3}, 4];

// Plain mixed destructuring
var [x1, x2 , {a : x3}, x4] = arr;

// destructuring using rest parameter
var [x5, ...x6] = arr;

// destructuring using rest parameter, and holes
var [x7, , ...x8] = arr;

console.log(x1, x2, x3, x4);    //  1, 2, 3, 4

console.log(x5, x6);            // 1 [2, {a : 3}, 4]

console.log(x7, x8);            // 1 [{a : 3}, 4]
````

Now, We are again not limited to using `array` within `object`, and vice versa, we can destructure an even more complex data, let's look at an example to see.

````javascript
var obj = {
  a : 1,
  b : [2, 3],
  c : {
    d : 4,
    e : ['red','blue']
  }
};

var {
  a : x1,
  b : [x2, x3],
  c : {
    e : [x4, x5],
    d : x6
  }
} = obj;

console.log(x1, x2, x3, x4, x5, x6);      // 1 2 3 'red' 'blue' 4
````

As long as we are following the patterns rules correctly, we can destructure any level of nested data, with any kind of data within it.

## Repeated assignment

In destructuring we are not just limited to assigning the value of an object to just one variable, we can assign the same property of an object to multiple variable.

````javascript
var obj = {
  a : 1,
  b : 2
};

var {
  a : x,
  a : y,
  b : z
} = obj;

console.log(x,y,z);       // 1,1,2
````

We use repeated assignment during the case of nested destructuring very efficiently, let's an example.

````javascript
var obj = {
  a : {
    b : 1,
    c : 2
  }
};

var {
  a : x1,
  a : {
    b : x,
    c : y
  }
} = obj;

console.log(x1, x, y);      // {b : 1 , c : 2}, 1, 2
````
In the above example we can see how we used nested destructuring and repeated assignment together to first extract the value of the properties `b` and `c` (to `x` an `y` respectively) in  object `a` within the object `obj`, and then how we assinged the value `a` itself to variable `x1`.

Since, in object destructuring a property name is used to extract the value from the original object, this helps us to perform repeated assignment, by using the same property name, however, in the array pattern, we can't have a repeated assignment, in the same destructuring pattern, as array values are extracted using the position, and it is an internal operation, however, we can destructure the same array multiple times to get our desired result.

````javascript
var arr = [1, 2, 3, [4, 5, 6]];

var [x1, x2 ,x3 ,[x4, x5]] = arr;
console.log(x1, x2, x3, x4, x5);    // 1 2 3 4 5

var [y1, y2 ,y3 ,y4] = arr;
console.log(y1, y2, y3, y4);    // 1 2 3 [4, 5, 6]

var [z1, ...z2] = arr;
console.log(z1, z2);    // 1 [2, 3, [4, 5, 6]
````

Repeated assignment can very useful when an array/object is used within an object. Using destructuring we can not only extract the individual values within the array, but we can also assign the entire array to a variable for reference.

````javascript
var obj = {
  a : 'colorInfo',
  b : ['red', 'blue'],
  c : {
    color : 'red',
    hex : '#ff0000'
  }
};

var {
  a : x1,
  b : [x2, x3],
  b : validColors,
  c : {
    color : colorName,
    hex : colorHexCode
  },
  c : colorObject
} = obj;

console.log(x1, x2, x3, validColors, colorName, colorHexCode, colorObject);
// 'colorInfo' 'red' 'blue' ['red', 'blue']  'red' '#ff0000' {color : 'red', hex : '#ff0000'}
````
We can see in above example of how we used array pattern to destructure the `b` array, then we again used same `b` and assigned to a new variable `vaildColor` which holds the array of valid colors, and then how we destructured the `c` object, and assgined the properties of `c` to variables `colorName` and `colorHexCode`, and then we assigned the entire object `c` to the variable colorObject for future reference.

## Default values
Earlier we landed into a situation where, a value which were trying to extract was not present in the object/array, and often it gave us a `TypeError` (if we perform nested destructuring) or it simply returned an `undefined`. In destructuring we can also set a default value for a variable, which means that if a value is not found in the original `object`/`array`, and a default value is provided, then the default value value will be assigned to the variable-target else `undefined` will be assigned, let's look into it by an example.

````javascript
var obj = {a : 1};

var {a : x , b : x1 = 10} = obj;

console.log(x, x1);     // 1, 10

var arr = [];

var [a = 5, b = 10] = arr;

console.log(a, b);   // 5 10
````

We need to careful about the fact that the default value is assigned to the newly created variable [**variable-target**], it is not assigned to the property in the original array/object. Which means if we perform a repeated assignment, we again need to provide the default value for the **variable-target**, because the value of the property in original object is still `undefined`, or that property is still not present, let's look by example

`````javascript
var obj = { a : 1 };

var { a : x, b : x1 = 10, b : x2 } = obj

console.log(x, x1, x2);     // 1 10 undefined
`````

In the above code, we can see that even though a default value was provided, during repeated assignment of `b`, since, `b` is still missing in the original object, it set the value as `undefined`.

Let's look how this can effect the nested destructuring, take a look at the code snippet below.

````javascript
var obj = {'name' : 'Goku'};

// If we try to destructure the above object using the pattern below,
// it throw `TypeError` as the 'obj'
// is not having any property 'attacks' and hence, `undefined` is assigned
// and then the same value is used to destructure which throws `TypeError`.

var { name, attacks : { specialMove } } = obj;

// We can solve this problem by using default value.
var { name, attacks : { specialMove } = { specialMove : ["Kamehameha", "Spirit Bomb"] } } = obj;

console.log(name, specialMove);    // Goku ["Kamehameha","Spirit Bomb"]
````

The above code didn't caused any issue, because, when `attacks` referred it found an `undefined` value, but watching the default value associated with it, the default value was used, and when it watched that the **variable-target** is an *object literal* it used the same default value for further destructuring, and hence never got any error. Let's look at another example.

````javascript
var obj = {'name' : 'Goku'};

var { name, attacks : { specialMove } = { specialMove : ["Kamehameha", "Spirit Bomb"] }, attacks } = obj;

console.log(name, specialMove, attacks);
// Goku ["Kamehameha","Spirit Bomb"] undefined
````

The above example, makes it clear that even though we used default when `attacks` was first referred, but during second reference, it again found that `attacks` is missing from `obj`, and hence, `undefined` was assigned. Default values are only computed if the value which we are trying to access is `undefined` or not present in the array/object. Let's look at an example.

````javascript
function getAttacksOption() {

  console.log('Getting default value')
  return {
    specialMove : ["Kamehameha", "Spirit Bomb"]
  };
}

var objWithAttacks = {
  'name' : 'Goku',
  'attacks' : {
     specialMove : ["Kamehameha"]
   }
  };

var objWithoutAttacks = {'name' : 'Goku'};

var {
  name : n1,
  attacks : a1 = getAttacksOption()
} = objWithAttacks;

var {
  name : n2,
  attacks : a2 = getAttacksOption()
} = objWithoutAttacks;
````

We can look the console `Getting default value` is coming only once, as the `objWithAttacks` already have an `attack` property in it, and hence, the default was not used, however the object `objWithoutAttacks` does not have the property, `attacks` and hence, the function `getAttacksOption` was called to get the default value. [Know more about default parameter.](https://github.com/anirudh-modi/JS-essentials/blob/master/ES2015/Functions/Default%20parameter.md)

Default values can be used for nested mixed destructuring also. It is not just limited to nested array or nested objects.

````javascript
var obj = {};

var {
  a : {
    b : [ x1, x2 ] = [ 1, 2 ]
  } = { b : undefined }
} = obj;

console.log(x1, x2);
// 1 2
````

We can see that original object was totally empty, and using default values we ended up extracting `x1` and `x2` from an array, using default values. Just to walk you through, It first used the default value `{ b : undefined }` to assign value to `a`, it then used the default of `b` `[ 1, 2 ]` and then, went on to destructure the final array.

## Destructuring parameter

Destructuring is not just limited to the regular variable declaration or destructuring multiple values returned from a function, we can also destructure the argument passed to a function. So, with destructuring if, we know our function accepts either an `object` or an `array`, then depending on the type, we can use the appropriate destructuring pattern to destructure the arguments passed to our function. Let's look at this code below and how it can be solved with destructuring.

```javascript
function foo(obj) {
  console.log(obj.a);

  console.log(obj.b);
}

foo({a : 1, b : 2});    // 1 2
```

In the above example, we know that our function `foo` is accepting a parameter `obj` which is meant to be an object. And to extract the values we are using the `.` dot operator, but, since we know that the `obj` will be ideally be an object, we could have just specify the object pattern to destructure the object. Let's see how.

````javascript
function foo({a, b}) {
  console.log(a);
  console.log(b);
}

var dummyObj = {
  a : 1,
  b : 2
}

foo(dummyObj);    // 1 2
````

We can see that, we are passing the `dummyObj` to the function `foo`, and since we are already expecting an object, we can use the object pattern to destructure the `dummyObj`. Destructuring doesn't effect the `arguments` object, even though we destructured the object, the function has only one parameter defined and the `arguments` object will show only one value.

````javascript
function foo({a, b}) {
  console.log(a);
  console.log(b);
  console.log(arguments)
}

var dummyObj = {
  a : 1,
  b : 2
}

foo(dummyObj);    // 1 2 {a : 1, b : 2}
````

Let's look at another example.

```javascript
function foo({a, b}, c) {
  console.log(a);             // 1
  console.log(b);             // 2
  console.log(c);             // 3
  console.log(arguments);     // [{a : 1, b : 2}, 3]
}

var dummyObj = {
  a : 1,
  b : 2
}

foo(dummyObj, 3);
```

If we look in the above example,, that the `arguments` object only specifies two values being passed, but we have destructured the `dummyObj`. An error might occur if value being based is not an object, which means if `null`/`undefined` is passed as first value to this `foo` a `TypeError` will occur.

We are not just restricted to objects, we can destructure an array value also, using array pattern.

```javascript
function foo([a, b]) {
  console.log(a);   // 1
  console.log(b);   // 2
}

foo([1, 2]);
```

Since, foo was now expecting array, we can use the array pattern to destructure.

### Default value while destructuring

Just like while destructuring, we can specify default values, if the property is not found in array or object, Same is applied for the parameter destructuring.

````javascript
function foo([a = 3, b = 4]) {
  console.log(a);
  console.log(b);
}

foo([1]);    // 1, 4
foo([]);     // 3, 4
foo([2, 5]); // 2, 5


function acceptFooObject({a = 'da', b = 'db'}) {
  console.log(a);
  console.log(b);
}

acceptFooObject({});                      // da, db
acceptFooObject({a : 'ca'});              // ca, db
acceptFooObject({a : 'ca', b : 'cb'});    // ca, cb
````

### Mixed destructuring + Nested + Repeated assignment + parameter

If our function is accepting both an object and array, we can destructure both using the appropriate destructuring pattern.

````javascript
function foo( { a, b, b : { c } }, [d, e] ) {
  console.log(a);
  console.log(b);
  console.log(c);
  console.log(d);
  console.log(e);
}

var dummyObj = {
  a : 1,
  b : {
    c : 2
  }
}
foo(dummyObj, [4, 5]);  //  1, {c : 2}, 2, 4, 5
````

````javascript
function foo( { a, b : [ c, d ], b } ) {
  console.log(a);
  console.log(b);
  console.log(c);
  console.log(d));
}

var dummyObj = {
  a : 1,
  b : [2, 3]
}
foo(dummyObj);   // 1, [2, 3],  2, 3
````

### Default parameter value + destructuring

Now, we must also cover the difference between the default value while destructuring and default parameter value.

````javascript
function foo( { a = 1 } = { a : 5 } ) {
  console.log(a);
}

foo();              // 5
foo({});            // 1
foo({a : 2});       // 2
````

So, what really happened? You must be confused with so many `=`, let's take one code at a time. Here, two things are happening, one is default value will assigned to parameter, if no value was passed, and the second is, if the parameter passed is not having the property `a` then, the default value of `1` will be assigned to the variable `a`. So, If, we look at the code `= { a : 5 }` this, is actually the default value for the parameter, and the code `= 1` is the default value for `a`, which will be used if the object which we are destructuring, is not having the property `a`.

Now let's look at the execution, to clear things ups.
1. The `foo()`, this gives us a console of `5`, that is because, while calling `foo`, we never passed any argument to the `foo` function, hence, the default value for the parameter is triggered, and the object `{ a : 5 }` is assigned as the first value to the parameter for `foo` and used to destructure, hence, `5`.

2. The `foo({})`, this gives a console of `1`, that is because, this time we passed an argument `{}` to `foo`, hence, the default value for the parameter will not be triggered, and the argument `{}` will be used to destructure, however, when the property `a` was not found in the object `{}`, the default value for `a` was triggered and hence, `1`.
3. The `foo({a : 2})`, this a console of `2`, which is for obvious reason, and that is because this time again we passed the argument as object `{ a : 2 }`, which is used for destructuring, and the same object is used to destructure, and while destructuring properrty `a` was found in the object, and no need was required for using the default value of `a` while destructuring.

Some, other variation for triggering of default value could.

```javascript
function foo( { a = 1 } = { a : 5 } ) {
  console.log(a);
}

foo(undefined);  // 5
foo({b : 2});    // 1
foo(null);       // TypeError
```

A note for the last example `foo(null)` this will throw an `TypeError`, because `null` is a valid value, and it will not trigger a default value for the parameter, and hence, the `null` will be used for destructuring, and since it was mentioned earlier `null` and `undefined` don't return an `object` it throw an `TypeError`.

****

**[Arrays](https://github.com/anirudh-modi/JS-essentials/blob/master/ES2015/Arrays/mainArray.md)**
