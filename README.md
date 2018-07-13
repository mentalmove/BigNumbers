# Big Numbers

In Javascript, arithmetic operations are feasible - as long as the used numbers don't exceed some limitations.
Beyond the maximum numbers, calculations don't work at all, but some operations
are also not reliable for medium size numbers.
This library allows arithmetic operations with unsigned integer numbers of unlimited size.

&nbsp;

### Abstract

An ArrayBuffer of a defined size is built and interpreted as optionally Uint8Array or Uint16Array.
**These two classifications are not compatible.**
Organisatoric steps are realised in a conventional way while all operations that alter numbers
avoid `==`, `<`, `>`, `<=`, `>=`, `+`, `-`, `*`, `/`, `%` as well as methods of the _Math_ - library;
for the sake of performance, binary operations (i.e. `!`, `~`, `|`, `^` and `&`) are used instead.
It could be discussed if storage and logic should be separated; the decision pro an object oriented
style was made in the hope the library could be easier expansible.

&nbsp;

## Usage

### Construction
A **BigNumber** can be built using the constructor, by copying an existing BigNumber, or as a result
of an operation. Due to general Javascript limitations of ArrayBuffers,
BigNumbers cannot be altered in memory size.

```
// Creates a new BigNumber
// All values set to 0
// @param byte_length Number
// @param bytes_per_entry Number
// @param src_typed_array Uint8Array or Uint16Array
// @return BigNumber
new BigNumber(byte_length[, bytes_per_entry[, src_typed_array]])
    byte_length         - Mandatory. Length, always measured in bytes,
                            independendent of the second paramter.
    bytes_per_entry     - Optional, but recommended. Possible values '1' (Uint8Array) or '2' (Uint16Array).
                            BigNumbers with different bytes_per_entry are not compatible.
    src_typed_array     - Optional. To build a BigNumber based on the value of another BigNumber.
                            Should be handled with care.
```
&nbsp;
```
// Creates a (real) duplicate
// Underlying ArrayBuffer is copied; dynamic properties are ignored
// @param bytes Number
// @return BigNumber
.copy([bytes])
    bytes               - Optional. If provided and greater than original BigNumber's byte_length,
                            the copy is filled with leading zero entries.
```
&nbsp;

### Assigning values
Technically, **Binary OR** is used; those who are not familiar with this operation should do
only one assignment per BigNumber. **Existing values cannot be cleared by writing**
`repective_big_number.from_number(0)`**.**

```
// @param number Number
.from_number (number)
    number              - Something that can be interpreted as unsigned integer number by javascript,
                            independent of its spelling.
```
```js
// Builds a BigNumber with (decimal) value 1234
var test = new BigNumber(2, 1);
test.from_number(0x04D2);   // Equivalent to 'test.from_number(1234)' or 'test.from_number(02322)'
```
&nbsp;
```
// @param mixed Array or String
.from_hex (mixed)
    mixed               - Array or String with or without separators
```
[Number Conversion](https://github.com/mentalmove/BigNumbers/blob/master/docs/number_conversion.html)    
[Division and Modulo](https://github.com/mentalmove/BigNumbers/blob/master/docs/div_mod.html)
&nbsp;  
&nbsp;
```
// @param string String
.from_decimal (string)
    string              - String representing an unsigned integer decimal number
```
```js
// Builds a BigNumber with value 1234
var test = new BigNumber(2, 2); // bytes_per_entry does not matter as long as only one BigNumber is used
test.from_decimal("1234");
```
&nbsp;
```
// Javascript's own encoding method btoa() should be used
// @param string String
.from_base64 (string)
    string              - Base64 encoded string
```
[Modulo-Exponentiation](https://github.com/mentalmove/BigNumbers/blob/master/docs/mod_pow.html)

&nbsp;

### Displaying results

```
// Generates a Javascript number or a decimal string, depending on the value's size
// @return Number | String
.to_number ()
```
&nbsp;
```
// Generates a string, representing a hexadecimal number
// @return String
.to_hex ()
```
&nbsp;
```
// Generates a base64-encoded string using Javascript's btoa()
// @return String
.to_base64 ()
```
&nbsp;
```
// Reference on the underlying Uint8Array or Uint16Array
// When altered, value will change
// @return Typed Array
.to_collection ()
```
```js
// Calculation is equivalent to 'parseInt(parseInt(1234 / 256) / 2) * 256 + 1234 % 256 + 43'
var test = new BigNumber(2, 1); // bytes_per_entry does matter this time
test.from_number( 1234 );
var collection = test.to_collection();
collection[0] /= 2;
collection[1] += 43;
console.log( test.to_number() ); // 765
```
&nbsp;

### Operations

#### Bit shift
Moves all bits to left (positive argument) or right (negative argument).
Alters the original value; the original number therefore should have a suitable size.
```
// @param bit Number
// @return void
.shift_by (bit)
    bit                 - Positive or negative integer number
```
[Example: All bits (Shift left)](https://mentalmove.github.io/BigNumbers/shift_left.html)  
[Example: Collatz (Shift right)](https://mentalmove.github.io/BigNumbers/shift_right.html)
&nbsp;  
&nbsp;
#### Addition
`increase_by()` and `add()`work almost identical; the difference is that `increase_by()` alters the original value
while `add()` returns a new _BigNumber_. Therefore, when using `increase_by()`, the original number must have
a suitable size, or the result will be truncated; `add()` will always return a _BigNumber_ of correct byte length.
```
// Increases one BigNumber by one (ore more) BigNumbers
// @param argumentX BigNumber
// @return void
.increase_by (argument1[, argument2[, argument3[, ...]]])
```
```
// Adds one (ore more) BigNumbers to a BigNumber
// @param argumentX BigNumber
// @return BigNumber
.add (argument1[, argument2[, argument3[, ...]]])
```
[Example: Fibonacci (Addition)](https://mentalmove.github.io/BigNumbers/addition.html)
&nbsp;  
&nbsp;
#### Subtraction
`decrease_by()` and `subtract()`work almost identical; the difference is that `decrease_by()` alters the original value
while `subtract()` returns a new _BigNumber_. Results lower than zero will automatically default to zero.
```
// Decreases one BigNumber by one (ore more) BigNumbers
// @param argumentX BigNumber
// @return void
.decrease_by (argument1[, argument2[, argument3[, ...]]])
```
```
// Subtracts one (ore more) BigNumbers from a BigNumber
// @param argumentX BigNumber
// @return BigNumber
.subtract (argument1[, argument2[, argument3[, ...]]])
```
[Example: Fibonacci (Subtraction)](https://mentalmove.github.io/BigNumbers/subtraction.html)
&nbsp;  
&nbsp;
#### Multiplication
Should be named 'multiplicate_by()' from a semantic point of view but leaves the original untouched.
Is intended to be used with only one argument.
Result will always have the correct (i.e. increased) byte length.
```
// Multiplicates the repective BigNumber with another BigNumber
// @param multiplicator BigNumber
// @return BigNumber
.multiplicate (multiplicator[, FOR_INTERNAL_USAGE_ONLY])
```
[Example: Faculty (Multiplication)](https://mentalmove.github.io/BigNumbers/multiplication.html)
&nbsp;  
&nbsp;
#### Division
Should be named 'divide_by()' from a semantic point of view but leaves the original untouched.
Used with one argument, result will always have the correct (i.e. decreased) byte length.
```
// Divides the repective BigNumber by another BigNumber
// @param divisor BigNumber
// @return BigNumber
.divide (divisor)
```
[Example: Faculty (Division)](https://mentalmove.github.io/BigNumbers/division.html)
&nbsp;  
&nbsp;
#### Modulo
```
// Division of a BigNumber by another BigNumber; only remainder is returned
// @param divisor BigNumber
// @return BigNumber
.mod (divisor)
```
[Example: Fibonacci (Modulo)](https://mentalmove.github.io/BigNumbers/modulo.html)
&nbsp;  
&nbsp;
#### Division and Modulo
To obtain result and remainder of a division in one step, `divide()` has to be used
with two arguments. Result will not be shortened but contain a property `modulo`.
```
// Divides the repective BigNumber by another BigNumber; remembers remainder
// @param divisor BigNumber
// @param do_not_reduce Boolean or anything that evaluates to 'true'
// @return BigNumber
//      containing property
//      'modulo' BigNumber
.divide (bignum, do_not_reduce)
```
```js
// Simple approximation to PI
var dividend = new BigNumber(1, 1);
dividend.from_number( 22 );
var divisor = new BigNumber(1, 1);
divisor.from_number( 7 );
var result = dividend.divide(divisor, true);        // Two parameters
var extend = new BigNumber(1, 1);
extend.from_number( 100 );
var extended_remainder = result.modulo.multiplicate(extend);
var fractal = extended_remainder.divide(divisor);   // Only one parameter
console.log( dividend.to_number() + " / " + divisor.to_number() + " = "
    + result.to_number() + "." + fractal.to_number() );
```
[Example: Chinese remainder theorem (Division and Modulo)](https://mentalmove.github.io/BigNumbers/div_mod.html)
&nbsp;  
&nbsp;
#### Square Root
Should be used without arguments to leave the original untouched.
```
// @return BigNumber
.sqrt ([FOR_INTERNAL_USAGE_ONLY])
    FOR_INTERNAL_USAGE_ONLY - Not recommended
```
[Example: Golden ratio (Square root)](https://mentalmove.github.io/BigNumbers/sqrt.html)
&nbsp;  
&nbsp;
#### Exponentiation
Since exponents usually are small, the argument can optionally be
an Unsigned Integer Javascript Number or a BigNumber.
Result is possibly _really_ large.
```
// @param exp Number or BigNumber
// @return BigNumber
.pow (exp)
    exp                 - Must be unsigned integer
```
[Example: Exponentiation](https://mentalmove.github.io/BigNumbers/exponentiation.html)
&nbsp;  
&nbsp;
#### Modulo-Exponentiation
Exponentiation and afterwards Modulo-Division, but with much lower effort.
Widely used in cryptography.
Both arguments can be optionally Numbers or BigNumbers.
```
// Result is never greater than (mod - 1)^2
// @param exp Number or BigNumber
// @param mod Number or BigNumber
// @return BigNumber
.mod_pow (exp, mod)
```
[Example: RSA (Modulo-Exponentiation)](https://mentalmove.github.io/BigNumbers/mod_pow.html)

&nbsp;

## Suggestions for extensions

### Negative Values
Negative numbers do not differ much of positive (or unsigned) numbers. Since BigNumbers
are objetcs, a user-defined property (e.g. `negative`) can be assigned. Calculations
can easily be done when the following rules are respected:
```
a + -b = a - b
-a + b = b - a
-a + -b = -(a + b)

a - -b = a + b
-a - b = -(a + b)
-a - -b = -a + b = b - a

a * -b = -(a * b)
-a * b = -(a * b)
-a * -b = a * b

a / -b = -(a / b)
-a / b = -(a / b)
-a / -b = a / b
```
The only possible problem is caused by `a - b` in case `b` is greater than `a`. This leads to two possible solutions
to circumvent the problem:
- It it always known if `a >= b` or not. An example would be the _Extended Euclidian Algorithm_:
```js
// Simple Javascript; assuming numbers are small
function extended_euclid (a, b) {
    if ( !b )
        return [a, 1, 0];
    var x = extended_euclid(b, a % b);
    return [ x[0], x[2], x[1] - x[2] * parseInt(a / b) ];
}
```
If initially `a` and `b` are positive and `a > b` (what should be the case for reasonable results),
it is always known if the return values are greater than 0 or not.
[*slow_chinese_euclid()* in **Chinese remainder theorem**](https://github.com/mentalmove/BigNumbers/blob/master/docs/div_mod.html)
- `a` and `b` have to be compared to know if `a - b` or `-(b - a)` is preferable. Since the _BigNumber_ - library already has a compare function,
it only has to be set publicly available, e.g. by writing
```js
this.compare = compare;
```
inside `function BigNumber() { /* ... */ }`.

[Example: Negative values](https://mentalmove.github.io/BigNumbers/negative.html)
