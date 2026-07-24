<!--

@license Apache-2.0

Copyright (c) 2026 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# incrnanmpcorrdist

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> Compute a moving [sample Pearson product-moment correlation distance][pearson-correlation] incrementally, ignoring `NaN` values.

<section class="intro">

The [sample Pearson product-moment correlation distance][pearson-correlation] is defined as

<!-- <equation class="equation" label="eq:pearson_distance" align="center" raw="d_{x,y} = 1 - r_{x,y} = 1 - \frac{\operatorname{cov_n(x,y)}}{\sigma_x \sigma_y}" alt="Equation for the Pearson product-moment correlation distance."> -->

```math
d_{x,y} = 1 - r_{x,y} = 1 - \frac{\mathop{\mathrm{cov_n(x,y)}}}{\sigma_x \sigma_y}
```

<!-- </equation> -->

where `r` is the [sample Pearson product-moment correlation coefficient][pearson-correlation], `cov(x,y)` is the sample covariance, and `σ` corresponds to the sample standard deviation. As `r` resides on the interval `[-1,1]`, `d` resides on the interval `[0,2]`.

</section>

<!-- /.intro -->

<section class="installation">

## Installation

```bash
npm install @stdlib/stats-incr-nanmpcorrdist
```

Alternatively,

-   To load the package in a website via a `script` tag without installation and bundlers, use the [ES Module][es-module] available on the [`esm`][esm-url] branch (see [README][esm-readme]).
-   If you are using Deno, visit the [`deno`][deno-url] branch (see [README][deno-readme] for usage intructions).
-   For use in Observable, or in browser/node environments, use the [Universal Module Definition (UMD)][umd] build available on the [`umd`][umd-url] branch (see [README][umd-readme]).

The [branches.md][branches-url] file summarizes the available branches and displays a diagram illustrating their relationships.

To view installation and usage instructions specific to each branch build, be sure to explicitly navigate to the respective README files on each branch, as linked to above.

</section>

<section class="usage">

## Usage

```javascript
var incrnanmpcorrdist = require( '@stdlib/stats-incr-nanmpcorrdist' );
```

#### incrnanmpcorrdist( window\[, mx, my] )

Returns an accumulator `function` which incrementally computes a moving [sample Pearson product-moment correlation distance][pearson-correlation], ignoring `NaN` values. The `window` parameter defines the number of values over which to compute the moving [sample Pearson product-moment correlation distance][pearson-correlation].

```javascript
var accumulator = incrnanmpcorrdist( 3 );
```

If means are already known, provide `mx` and `my` arguments.

```javascript
var accumulator = incrnanmpcorrdist( 3, 5.0, -3.14 );
```

#### accumulator( \[x, y] )

If provided input values `x` and `y`, the accumulator function returns an updated [sample Pearson product-moment correlation distance][pearson-correlation]. If not provided input values `x` and `y`, the accumulator function returns the current [sample Pearson product-moment correlation distance][pearson-correlation].

```javascript
var accumulator = incrnanmpcorrdist( 3 );

var r = accumulator();
// returns null

// Fill the window...
r = accumulator( 2.0, 1.0 ); // [(2.0, 1.0)]
// returns 1.0

r = accumulator( NaN, 3.0 ); // [(2.0, 1.0)]
// returns 1.0

r = accumulator( 4.0, NaN ); // [(2.0, 1.0)]
// returns 1.0

r = accumulator( NaN, NaN ); // [(2.0, 1.0)]
// returns 1.0

r = accumulator( 5.0, 2.0 ); // [(2.0, 1.0), (5.0, 2.0)]
// returns 0.0

r = accumulator();
// returns 0.0
```

</section>

<!-- /.usage -->

<section class="notes">

## Notes

-   Input values are **not** type checked. If provided `NaN`, the value is ignored and the accumulated value is the current accumulated value. If non-numeric inputs are possible, you are advised to type check and handle accordingly **before** passing the value to the accumulator function.
-   As `W` (x,y) pairs are needed to fill the window buffer, the first `W-1` returned values are calculated from smaller sample sizes. Until the window is full, each returned value is calculated from all provided values.
-   Due to limitations inherent in representing numeric values using floating-point format (i.e., the inability to represent numeric values with infinite precision), the [sample correlation distance][pearson-correlation] between perfectly correlated random variables may **not** be `0` or `2`. In fact, the [sample correlation distance][pearson-correlation] is **not** guaranteed to be strictly on the interval `[0,2]`. Any computed distance should, however, be within floating-point roundoff error.

</section>

<!-- /.notes -->

<section class="examples">

## Examples

<!-- eslint no-undef: "error" -->

```javascript
var randu = require( '@stdlib/random-base-randu' );
var incrnanmpcorrdist = require( '@stdlib/stats-incr-nanmpcorrdist' );

var accumulator;
var x;
var y;
var i;

// Initialize an accumulator:
accumulator = incrnanmpcorrdist( 5 );

// For each simulated datum, update the moving sample correlation distance...
for ( i = 0; i < 100; i++ ) {
    if ( randu() < 0.2 ) {
        x = NaN;
    }
    else {
        x = randu() * 100.0;
    }
    if ( randu() < 0.2 ) {
        y = NaN;
    }
    else {
        y = randu() * 100.0;
    }
    accumulator( x, y );
}
console.log( accumulator() );
```

</section>

<!-- /.examples -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

</section>

<!-- /.related -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library for JavaScript and Node.js, with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2026. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/stats-incr-nanmpcorrdist.svg
[npm-url]: https://npmjs.org/package/@stdlib/stats-incr-nanmpcorrdist

[test-image]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/actions/workflows/test.yml/badge.svg?branch=main
[test-url]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/actions/workflows/test.yml?query=branch:main

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/stats-incr-nanmpcorrdist/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/stats-incr-nanmpcorrdist?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/stats-incr-nanmpcorrdist.svg
[dependencies-url]: https://david-dm.org/stdlib-js/stats-incr-nanmpcorrdist/main

-->

[chat-image]: https://img.shields.io/badge/zulip-join_chat-brightgreen.svg
[chat-url]: https://stdlib.zulipchat.com

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/tree/deno
[deno-readme]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/blob/deno/README.md
[umd-url]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/tree/umd
[umd-readme]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/blob/umd/README.md
[esm-url]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/tree/esm
[esm-readme]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/blob/esm/README.md
[branches-url]: https://github.com/stdlib-js/stats-incr-nanmpcorrdist/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/stats-incr-nanmpcorrdist/main/LICENSE

[pearson-correlation]: https://en.wikipedia.org/wiki/Pearson_correlation_coefficient

</section>

<!-- /.links -->
