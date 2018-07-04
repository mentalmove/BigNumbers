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
for the sake of performance, binary operations are used instead.

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
Technically, **XOR** is used; those who are not familiar with this operation should do
only one assignment per BigNumber.

```
// @param number Number
.from_number (number)
    number              - Something that can be interpreted as unsigned integer number by javascript,
                            independent of its spelling.
```
```
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
```
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
```
// Calculation is equivalent to 'parseInt(parseInt(1234 / 256) / 2) * 256 + 1234 % 256 + 43'
var test = new BigNumber(2, 1); // bytes_per_entry does matter this time
test.from_number( 1234 );
var collection = test.to_collection();
collection[0] /= 2;
collection[1] += 43;
console.log( test.to_number() );    // 765
```
&nbsp;

### Operations

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
Should be name 'multiplicate_by()' from a semantic point of view but leaves the original untouched.
Should be used with only one argument.
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
Should be name 'divide_by()' from a semantic point of view but leaves the original untouched.
Used with one argument, result will always have the correct (i.e. decreased) byte length.
```
// Divides the repective BigNumber by another BigNumber
// @param divisor BigNumber
// @return BigNumber
.divide (divisor)
```
[Example: Faculty (Division)](https://mentalmove.github.io/BigNumbers/division.html)
