# CodeGolf contest for a ticket to the Nantes Devfest 2019

## The Goal

   You are the assistant of Jane Skillz, the rockstar of the group JayDeyJay.
   Jane wants to please her fans, and host a signing session.

   Each fan will show up one after the other.
   Jane will spend 3 minutes per fan, to talk and sign a piece of paper.

   But Jane has some quirks:
   - Jane will offer a signed picture to every fan that arrives in a prime position (2, 3, 5, 7, ..., 29, 31..)
   - If a fan's name has more vowels than consonants, then she'll spend 11 minutes with them
   - If a fan has 2 identical consecutive letters in its name, then she'll spent 7 minutes with them and offer a signed picture.

   (Note: Prime numbers and identical consecutive letters are additive. A fan can receive 2 pictures.)
   (Note 2: If a fan has 2 identical consecutive letters and more vowels, Jane will spend 15 minutes with them)

   As her assistance, you must find out the time spent, as well as the number of pictures gifted during the session.
  
   #### INPUT/OUTPUT
  
   Input: An array of names
  
   Output: An array with the total time spent and the number of pictures
  
   #### EXAMPLE
   
   ```javascript
   const seance = ["Johnn", "Paulo", "Ringo", "George"]
   // Johnn => 7min + 1picture
   // Paulo => 11min + 1picture
   // Ringo => 3min + 1picture
   // George => 3min
   console.log([24, 3]);
   ```

## Submission

```javascript
const challenge = s => (s.map(v=>{for(j=++i;i%--j;);p+=!--j+(u=/(.)\1/i.test(v));d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]|4*u|3},d=p=i=0),[d,p])
```

## Code's explaination

The first noticeable details is the usage of `()` to replace the `{}` around the body of the function. In JS, if a function return an expression such as `(a, b, c, d)`, then `a`, `b`, `c` and `d` will be computed, and the last statement will be returned.

It saves us the 6 characters from the keyword `return`.

We start with a `map`, to loop over every elements of our input. We never use the returned value from `map`, but it's 4 less characters than `forEach`.

Notice how we use the [2ed argument of map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#parameters). It is meant to define the value of `this`, but in our case we use it to initialize our variables. It doesn't save any characters, but it's fun and unusual.

```javascript
for(j=++i;i%--j;);
```

This snippet does a lot. First it increments the value `i`, which gives us the order of arrival.

It also helps us figure out if `i` is a prime number by using the stop condition of the `for` loop. It will keep going until the [remainder](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Remainder) of `i%j` is equal to 0. The decrement of `j` allows us to test all numbers from `i-1` to `1`. 

If the number is prime, then `j` will be equal to 1 at the end of the loop. Otherwise `j` will either be `0` (when `i===1`), or superior to `1`.

The `;` at the very end is there to make sure that we don't run any code in the loop.

```javascript
p+=!--j+(u=/(.)\1/i.test(v));
```

This snippet help us find out the number of pictures `p`. It has 2 distinct parts.

The first part increments the number of pictures if the user arrives in a prime spot. We simply test if `j===!`. While `j==1` would save a character, the operator `==` as a lower priority than `+` which would force us to add `()` around this statement.

The second part is using regexes. We start by adding the [flag `i`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/ignoreCase), to make sure that any Uppercase won't affect the result. The Regex itself is simple enough. We start by catching all chars, then we check if the following one is identical by using `\1` which is equal to the value of the first capture group `(.)`.
Using the [`test`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Objets_globaux/RegExp/test) instand of [`exec`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)
guarantee a boolean return value, as `exec` could return a `null` or `array`.

We also take this opportunity to store the result in `u` so we can use it later.

```javascript
d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]|4*u|3
```

We are only missing the duration `d` now. And because we already stored the condition for the consecutive letters in `u`, we only have one value left to figure out.

The easiest method is to count all the vowels, and then compare that to the total length of the name. However it is really inefficient for us, as it would force us to use the `length` property a second time, which is... lengthy.

Instead we are going to use a peculiarity of the `string` type. We can [access each char using `str[i]`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#character_access). That means we can access the char at the `2*n+1` index, where `n` is the vowel count, and it comes back as `undefined`, then we have more vowels than consonants.

All that is left for us to do is figure out the number of vowels. We can do that using the `/[aeiouy]/gi` regex, capturing all occurences in an Array and then get it's length.

Finally, we could have added the values in that statement using the `+` operator:
```javascript
d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]+4*u+3
```

But as the values are really convenient for binary operations (`0011`, `0100`, et `1000`), I had a bit of fun using `|` which is the [bitwise `OR`]([https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Op%C3%A9rateurs/Op%C3%A9rateurs_binaires#(OU_binaire)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_OR))
