# Find Last Index

> Source: [https://www.greatfrontend.com/questions/javascript/find-last-index](https://www.greatfrontend.com/questions/javascript/find-last-index)

Implement a function findLastIndex(array, predicate, [fromIndex=array.length-1]) that takes an array of values, a function predicate, and an optional fromIndex number argument, and returns the index of the last element in the array that satisfies the provided testing function predicate. The elements of the array are iterated from right to left.

## Arguments
1. `array` (Array): The array to inspect.
2. `predicate` (Function): The function invoked per iteration. The function is invoked with three arguments: (value, index, array).
3. `[fromIndex=array.length-1]` (number): The index to search from.

## Returns
(number): Returns the index of the found element, else -1.

## Examples
```js
const arr = [5, 4, 3, 2, 1];

// Search for the last value in the array that is greater than 3 and return the index.
findLastIndex(arr, (num) => num > 3); // => 1

// Start searching from index 3 (inclusive).
findLastIndex(arr, (num) => num > 1, 3); // => 3

// Start searching from index 3 (inclusive).
findLastIndex(arr, (num) => num < 1, 3); // => -1
```

## Edge Cases
Your function should handle negative and out of bound indices:
- Negative: negative integers count back from the last item in the array. -1 means the last element in the array, -2 means the second last element, and so on.
- Negative and out of bounds: if the absolute value of the negative integer given is greater than the length of the array, then the search start defaults to the first item in the array.
- Out of bound: if index >= array.length, start from the last index.

```js
const arr = [5, 4, 3, 2, 1];

// Start searching from index 2.
findLastIndex(arr, (num) => num > 2, -3); // => 2

findLastIndex(arr, (num) => num % 2 === 0, -3); // => 1

// Start from the last index if fromIndex >= array.length.
findLastIndex(arr, (num) => num > 0, 10); // => 4

// Negative and out of bounds, start searching from the first item in the array.
findLastIndex(arr, (num) => num > 2, -10); // => 0
```

## Attempted solution:

```js
/**
 * This function returns the index of the last element in the array that satisfies the provided testing function.
 * Otherwise, it returns -1.
 *
 * @param {Array} array - The array to search.
 * @param {Function} predicate - The function invoked per iteration.
 * @param {number} [fromIndex=array.length-1] - The index to start searching from.
 * @returns The index of the found element, else -1.
 */
export default function findLastIndex(
  array,
  predicate,
  fromIndex = array.length - 1,
) {
  const startIndex =
    fromIndex < 0
      ? Math.max(array.length + fromIndex, 0)
      : Math.min(fromIndex, array.length - 1);

  let i = startIndex;

  while (i >= 0) {
    if (predicate(array[i], i, array)) {
      return i;
    }
    i--;
  }

  return -1;
}
```

## Key Insights:
What ever the from index is we need to map it to 0th index based list of array indexes to always run while loop from right to left on an array:
```js
const startIndex =
    fromIndex < 0
      ? Math.max(array.length + fromIndex, 0)
      : Math.min(fromIndex, array.length - 1);
```

## Resources
[Lodash `_.findLastIndex`](https://lodash.com/docs/#findLastIndex)