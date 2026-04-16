---
title: 'Floating Point Numbers Explained'
slug: '/answers/floating_point_numbers'
---

# Floating point numbers explained

## Warning from PHP Manual

Floating point numbers have limited precision. Although it depends on the system, PHP typically uses the IEEE 754 double precision format, which will give a maximum relative error due to rounding in the order of 1.11e-16. Non-elementary arithmetic operations may give larger errors, and, of course, error propagation must be considered when several operations are compounded.

Additionally, rational numbers that are exactly representable as floating point numbers in base 10, like 0.1 or 0.7, do not have an exact representation as floating point numbers in base 2, which is used internally, no matter the size of the mantissa. Hence, they cannot be converted into their internal binary counterparts without a small loss of precision. This can lead to confusing results: for example, floor((0.1+0.7)\*10) will usually return 7 instead of the expected 8, because the internal representation will be something like 7.9999999999999991118....

So never trust floating point number results to the last digit, and do not compare floating point numbers directly for equality. If higher precision is necessary, the arbitrary precision math functions and gmp functions are available.

## Simple Example (from floating-point-gui.de)

In many programming languages, including PHP:

```php
echo 0.1 + 0.2; // 0.30000000000000004
```

This is because `0.1` and `0.2` cannot be represented exactly in binary.

### Why does this happen? (Basic Explanation)

Floating-point numbers are usually implemented as a **binary fraction** (base 2) rather than a decimal fraction (base 10).

- In base 10, a fraction like `1/3` cannot be represented exactly (it's `0.33333...`).
- Similarly, in base 2, fractions like `1/10` (`0.1`) or `1/5` (`0.2`) become **repeating fractions**.

**The 0.5 Exception:**
A number like `0.5` _can_ be represented exactly in binary because it is a power of 2 ($2^{-1}$ or $1/2$).

```php
var_dump(0.5 + 0.5 == 1.0); // true (0.5 is exact)
```

**Wait, what?**
When you add `0.1 + 0.2`, you are actually adding:

- `0.1` (internally: `0.1000000000000000055511151231257827021181583404541015625`)
- `0.2` (internally: `0.200000000000000011102230246251565404236316680908203125`)

The result is `0.3000000000000000444089209850062616169452667236328125`, which is why `0.1 + 0.2 == 0.3` returns `false`.

### What can I do?

1. **Never use `==` for floats.** Use a small "epsilon" (precision) value for comparison:
   ```php
   $epsilon = 0.00001;
   if (abs(($a + $b) - $c) < $epsilon) {
       // Values are "equal enough"
   }
   ```
2. **Use arbitrary precision libraries** like BCMath or GMP (see below).
3. **Use integers for money.** Store `$1.23` as `123` cents to avoid floating point issues entirely.

## How it's avoided: BC Math (Arbitrary Precision Mathematics)

For arbitrary precision mathematics PHP offers the BC Math Binary Calculator which supports numbers of any size and precision, represented as strings.

### BCMath Functions:

- **bcadd** — Add two arbitrary precision numbers
- **bcceil** — Round up arbitrary precision number
- **bccomp** — Compare two arbitrary precision numbers
- **bcdiv** — Divide two arbitrary precision numbers
- **bcdivmod** — Get the quotient and modulus of an arbitrary precision number
- **bcfloor** — Round down arbitrary precision number
- **bcmod** — Get modulus of an arbitrary precision number
- **bcmul** — Multiply two arbitrary precision numbers
- **bcpow** — Raise an arbitrary precision number to another
- **bcpowmod** — Raise an arbitrary precision number to another, reduced by a specified modulus
- **bcround** — Round arbitrary precision number
- **bcscale** — Set or get default scale parameter for all bc math functions
- **bcsqrt** — Get the square root of an arbitrary precision number
- **bcsub** — Subtract one arbitrary precision number from another

### BCMath Usage Example:

```php
<?php
// Set global scale for all BCMath functions
bcscale(3);

$a = '1.234567';
$b = '2.345678';

echo bcadd($a, $b);       // 3.580 (using default scale 3)
echo bcadd($a, $b, 6);    // 3.580245 (explicit scale)
echo bccomp($a, $b, 6);   // -1 (means $a < $b)
echo bcmul($a, '2', 2);   // 2.46
?>
```

## GMP (GNU Multiple Precision)

GMP allows you to work with arbitrary-length integers.

### Important Note on Differences between GMP and BCMath:

- **Number Type Support:** GMP works exclusively on arbitrary precision **integers**, while BCMath supports arbitrary precision **decimal** (floating-point-like) values.
- **Performance:** GMP is significantly **faster** than BCMath.
- **API:** GMP uses a resource-based (or object-based in newer versions) API, while BCMath works primarily with strings.

### Needed GMP Functions:

- **gmp_abs** — Absolute value
- **gmp_add** — Add numbers
- **gmp_cmp** — Compare numbers
- **gmp_div_q** — Divide numbers
- **gmp_div_qr** — Divide numbers and get quotient and remainder
- **gmp_div_r** — Remainder of the division of numbers
- **gmp_init** — Create GMP number from a string or integer
- **gmp_intval** — Convert GMP number to integer
- **gmp_mul** — Multiply numbers
- **gmp_pow** — Raise number into power
- **gmp_strval** — Convert GMP number to string
- **gmp_sub** — Subtract numbers

### GMP Usage Example:

```php
<?php
// Working with large integers
$a = gmp_init("12345678901234567890");
$b = gmp_init("98765432109876543210");

$sum = gmp_add($a, $b);
echo gmp_strval($sum); // 111111111011111111100

// GMP objects support operators (since PHP 5.6+)
$product = $a * $b;
echo gmp_strval($product);

// Comparison
if (gmp_cmp($a, $b) < 0) {
    echo "$a is smaller than $b";
}
?>
```
