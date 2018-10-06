# callbag-flat-map-lazy 👜

A callbag operator that maps items to sources and flattens them to a
single output source.

`npm install callbag-flat-map-lazy`

This is a fork of [callbag-flat-map](https://github.com/avinashcodes/callbag-flat-map).

The basic difference between the two is that the source returned by
`callbag-flat-map` is always listenable, whereas the source returned
by `callbag-flat-map` is pullable if the inner sources are pullable.
For more information on how that is achieved, see [Details](#details)

## Usage:

```js
const flatMap = require('callbag-flat-map-lazy');
const fromIter = require('callbag-from-iter');
const of = require('callbag-of');
const pipe = require('callbag-pipe');
const toIterable = require('callbag-to-iterable');

// Pullable sources
const iterable = pipe(
  fromIter([1, 2]),
  flatMap(char => fromIter(['a', 'b']), (a, b) => char + num),
  toIterable
);

for (const i of iterable) {
  console.log(i);                // Logs 1a, 1b, 2a, 2b
}

// Listenable sources
pipe(
  of(1, 2),
  flatMap(char => of('a', 'b'), (a, b) => char + num),
  forEach(x => console.log(x))   // Logs 1a, 1b, 2a, 2b
);
```

## <a id="details"></a>Details

The output source returned by `callbag-flat-map-lazy` forwards pulls to
the oldest inner source.
When the last inner source completes, the input source is pulled in
order to create the next inner source.

To support mapping a pullable source to listenable inner sources,
the input source is pulled once initially to establish the first
inner source.