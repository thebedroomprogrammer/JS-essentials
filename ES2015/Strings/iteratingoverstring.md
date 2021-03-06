## Iterating over a string

Apart from using the traditional `for` loop over a string and the `for..in` loop, ES6 introduced a new operator which can be performed over `iterable`, and since, `string` are nothing but `iterable`, we can use the `for..of` loop to iterate over any string. The `for..of` loop will iterate over the string and give a value present in the `string`. The details of the `for..of` loop will be discussed later in the `iterators` chapter. Let's look at a small example though.

````javascript
let str = 'Hello';

for(let char of str) {
  console.log(char);      // H,e,l,l,o
}
````

We can also convert a string into an array using the spread operator `...` over the string, within an array context/

````javascript
var str = 'Blue';
var arr = [...str]; // ["B","l","u","e"]
````

****

**Previous Chapter : [New methods in string wrapper](https://github.com/anirudh-modi/JS-essentials/blob/master/ES2015/Strings/stringMethods.md)**

**[Destructuring](https://github.com/anirudh-modi/JS-essentials/blob/master/ES2015/Destructuring/main.md)**
