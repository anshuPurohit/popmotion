---
title: Value Types
description: Handle special value types with collections of parsers, transformers and tests.
---

# Value Types

Popmotion actions work with simple numbers, which are sometimes composed into more complex objects with `composite`.

It provides `valueTypes` to help handle common value types, for instance `px` measurements or `hex` colours.

Each `valueType` provides three methods:
- `test`: Accepts a value and returns `true` if the provided value is of that type.
- `parse`: Accepts a value and returns that value in a usable format, either a `Number` or an `Object` of named numerical properties.
- `transform`: The reverse of parse, accepts either a number or an object and converts that into the value type.

```javascript
import { valueTypes } from 'popmotion';

const { color } = valueTypes;

// Test
color.test('#fff'); // true
color.test(0); // false

// Parse
color.parse('rgba(255, 255, 255, 8)')
/*
Returns {
  red: 255,
  blue: 255,
  green: 255,
  alpha: 0
}
*/

// Transform
color.transform({
  hue: 200,
  saturation: 100,
  lightness: 50,
  alpha: 0.5
});
// Returns 'hsla(200, 100%, 50%, 0.5)'
```

## Included value types

- `alpha`: `Number` between `0` and `1`
- `complex`: `String` containing arbitrary sequence of numbers mixed with other characters. See below.
- `color`: `String` of either `hex`, `hsla` or `rgba` type
- `degrees`: `String` ending in `deg`
- `hex`: `String` beginning with `#` and followed by 3 or 6-digit hex code
- `hsla`: `String` with valid `hsla` property
- `percent`: `String` ending in `%`
- `px`: `String` ending in `px`
- `scale`: `Number` with a `default` of `1` instead of `0`
- `rgbUnit`: Integer between `1` and `255`
- `rgba`: String in `rgba(rgbUnit, rgbUnit, rgbUnit, alpha)` format

## Complex type

The `complex` value type is slightly different to the others. Instead of a `transform` method, it has a `createTransformer` method which returns the `transform` method:

```javascript
const svgPath = 'M150 0 L75 200';
const transform = complex.createTransformer(svgPath);
```

The returned `transform` function is unique to the string given to it. When this function is provided an object of the same format as returned by `complex.parse()` (in this example `complex.parse(svgPath)`), it will use the original string as a template.

Example: 

```javascript
transform({
  '0': 300,
  '1': 0,
  '2': 100,
  '3': 200
}); // Returns 'M300 0 L100 200'
```
