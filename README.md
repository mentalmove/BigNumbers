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

### Real numbers
In computer science, non-integer numbers usually are realised as base-mantissa-exponent - combination.
This is extremely fast and more or less reliable for fixed memory sizes what stands in contrast to
the idea behind _BigNumbers_. Floating point numbers of variable size (i.e. integer part left of the point,
fractal part right of the point) can be realised using strings; this is an easy approach for humans,
but unfortunately slow.     
A third method would be pairs of numbers or BigNumbers; this can be done without touching the library, i.e.
```js
// @param numerator BigNumber
// @param denominator BigNumber
// numerator and denominator must have the same bytes_per_entry
function Rational (numerator, denominator) {

    function euclid (a, b) {
        var b_collection = b.to_collection();
        if ( b_collection.length == 1 && !b_collection[0] )
            return a;
        return euclid(b, a.mod(b));
    }

    this.decimal = function (precision) {
        var integer_part = numerator.divide(denominator).to_number().toString();
        if ( is_integer )
            return integer_part;
        if ( !precision || isNaN(precision) )
            return integer_part;
        precision = parseInt(precision);
        if ( precision < 1 )
            return integer_part;

        var ten = new BigNumber(1, denominator.to_collection().BYTES_PER_ELEMENT);
        ten.from_number(10);
        var extend = ten.pow(precision);
        var tmp_numerator = mod.multiplicate(extend);
        var fractal = tmp_numerator.divide(denominator).to_number().toString();
        while ( fractal.length < precision )
            fractal = "0" + fractal;
        while ( fractal.length && fractal.substr(fractal.length - 1) == "0" )
            fractal = fractal.substr(0, fractal.length - 1);

        return integer_part + "." + fractal;
    };

    this.add = function (rational) {
        var divisor = euclid(denominator, rational.denominator);
        var tmp1 = numerator.multiplicate( rational.denominator.divide(divisor) );
        var tmp2 = rational.numerator.multiplicate( denominator.divide(divisor) );
        var num = tmp1.add(tmp2);
        var den = denominator.multiplicate( rational.denominator.divide(divisor) );
        return new Rational(num, den);
    };
    this.subtract = function (rational) {
        var divisor = euclid(denominator, rational.denominator);
        var tmp1 = numerator.multiplicate( rational.denominator.divide(divisor) );
        var tmp2 = rational.numerator.multiplicate( denominator.divide(divisor) );
        var num = tmp1.subtract(tmp2);
        var den = denominator.multiplicate( rational.denominator.divide(divisor) );
        return new Rational(num, den);
    };
    this.multiplicate = function (rational) {
        return new Rational(numerator.multiplicate(rational.numerator), denominator.multiplicate(rational.denominator));
    };
    this.divide = function (rational) {
        return new Rational(numerator.multiplicate(rational.denominator), denominator.multiplicate(rational.numerator));
    };

    var divisor = euclid(numerator, denominator);
    var divisor_collection = divisor.to_collection();
    if ( divisor_collection.length > 1 || divisor_collection[0] > 1 ) {
        numerator = numerator.divide(divisor);
        denominator = denominator.divide(divisor);
    }
    this.numerator = numerator;
    this.denominator = denominator;

    var mod = numerator.mod(denominator);
    var mod_collection = mod.to_collection();
    var is_integer = (mod_collection.length == 1 && !mod_collection[0]);
}
```
To keep it readable, the above function is not performance optimised.     
[Example: Rational Numbers](https://mentalmove.github.io/BigNumbers/rational_numbers.html)

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
See [*slow_chinese_euclid()* in **Chinese remainder theorem**](https://github.com/mentalmove/BigNumbers/blob/master/docs/div_mod.html)
- `a` and `b` have to be compared to know if `a - b` or `-(b - a)` is preferable. Since the _BigNumber_ - library already has a compare function,
it only has to be set publicly available, e.g. by writing
```js
this.compare = compare;
```
inside `function BigNumber() { /* ... */ }`.

[Example: Negative values](https://mentalmove.github.io/BigNumbers/negative.html)

&nbsp;

### Different Number Bases
The library already includes conversions between number systems based on `10` and the most common two potencies
`16` and `64` (and, of course, its own number system `256` or `65536`). This can be used to convert between these
systems, e.g. for [converting hexadecimal PI to decimal PI](https://mentalmove.github.io/BigNumbers/number_conversion.html).     
The underlying methods can be extended for other bases. Internally, computers know only two types
of integer numbers: _Potencies of two_ and all the others. Therefore, a [possibly extended library](https://github.com/mentalmove/BigNumbers/blob/master/docs/BigNumberDiffBaseExtended.js) should act differently
if the respective bases have other prime factors than two or not. Number bases `> 36`would also be possible,
but that would require a consistent mapping between numbers and symbols.

[Example: Different number bases](https://mentalmove.github.io/BigNumbers/different_bases.html)

&nbsp;

### Binary Operations

#### Set single bit
```js
// Adds a two potency by setting a single bit to 1
// If already set, tries the next
this.set_bit = set_bit;
```
```js
var num1 = new BigNumber(2, 1);
num1.from_number(996);
num1.set_bit(3);                    // Efficient because relevant bit is 0
console.log( num1.to_number() );

var num2 = new BigNumber(2, 1);
num2.from_number(1023);
num2.set_bit(1);                    // Not efficient because num2 is odd; needs 10 steps
console.log( num2.to_number() );
```

#### Unset single bit
```js
// Subtracts a two potency by setting a single bit to 0
// If already unset, tries the next
this.unset_bit = unset_bit;
```
```js
var num1 = new BigNumber(3, 1);
num1.from_number(65537);
num1.unset_bit(1);                  // Efficient
console.log( num1.to_number() );

var num2 = new BigNumber(3, 1);
num2.from_number(65536);
num2.unset_bit(1);                  // Not efficient
console.log( num2.to_number() );
```

#### Toggle all bits
```js
// Reversable by repeating
this.negate = function () {
    for ( var i = 0; i < typed_array.length; i++ )
        typed_array[i] = (~typed_array[i]) & MAX_VALUE;
};
```
```js
var num = new BigNumber(4, 1);
num.from_number(3060399405);
num.negate();
console.log( num.to_number() );     // 1234567890
```

#### Two's complement
In computer science usually used to toggle the sign. **Not recommended for** _BigNumbers_**.**
```js
// Reversable by repeating
this.twos_complement = function () {
    for ( var i = 0; i < typed_array.length; i++ )
        typed_array[i] = (~typed_array[i]) & MAX_VALUE;
    set_bit(1);
};
```
```js
var A = new BigNumber(3, 1);
A.from_number(15777216);
console.log( A.to_number() );       // 15777216
A.twos_complement();
console.log( A.to_number() );       // 1000000
A.twos_complement();
console.log( A.to_number() );       // 15777216

// Please compare:
var a = 15777216;
console.log( a );                   // 15777216
a = ~a;
a++;
console.log( a );                   // -15777216
a = ~a;
a++;
console.log( a );                   // 15777216
```

#### OR
```js
this.or = function (bignum) {
    var bignum_collection = bignum.to_collection();
    var length = Math.max(bignum_collection.length, typed_array.length);
    var result = myself.copy(length);
    var result_collection = result.to_collection();
    for ( var i = 0; i < length; i++ ) {
        if ( i < bignum_collection.length && i < typed_array.length )
            result_collection[length - i - 1] = bignum_collection[bignum_collection.length - i - 1] | typed_array[typed_array.length - i - 1];
        else {
            if ( length == typed_array.length )
                break;
            result_collection[length - i - 1] = bignum_collection[bignum_collection.length - i - 1];
        }
    }
    return reduce(result);
};
```
```js
var num1 = new BigNumber(2, 1);
num1.from_number(645);
var num2 = new BigNumber(2, 1);
num2.from_number(358);
console.log( num1.to_number() + " OR " + num2.to_number() + " = " + num1.or(num2).to_number() );    // 999
console.log( num1.to_number() + " + " + num2.to_number() + " = " + num1.add(num2).to_number() );    // 1003
```
