# 2727. Is Object Empty

> Source: (https://leetcode.com/problems/is-object-empty/description/?envType=study-plan-v2&envId=30-days-of-javascript)[https://leetcode.com/problems/is-object-empty/description/?envType=study-plan-v2&envId=30-days-of-javascript]

## Attempted Solution

```js
/**
 * @param {Object|Array} obj
 * @return {boolean}
 */
var isEmpty = function (obj) {
  return !Object.keys(obj ?? {})?.length;
};
```

## Key Insights

The key insight is that `Object.keys()` can uniformly handle both objects and arrays to check for emptiness - it returns an array of enumerable property names for objects and index strings for arrays. This lets you write a single check that works for both data types.

Additional detail: The nullish coalescing operator (`??`) handles null/undefined cases by defaulting to an empty object, preventing errors.

## Better Solution

A more robust and efficient version of this function with explaination of the thought process and improvements step by step.

```js
/**
 * Checks if a value is empty (object, array, string, etc.)
 *
 * Key improvements:
 * 1. Handles all data types appropriately
 * 2. Considers empty strings and spaces-only strings as empty
 * 3. Handles Map and Set objects
 * 4. Early returns for common cases
 * 5. Proper type checking for more specific handling
 *
 * @param {any} value - The value to check for emptiness
 * @return {boolean} - Returns true if the value is considered empty
 */
const isEmpty = function (value) {
  // Handle null/undefined
  if (value == null) {
    return true;
  }

  // Handle strings - consider empty or whitespace-only strings as empty
  if (typeof value === "string") {
    return value.trim().length === 0;
  }

  // Handle numbers and booleans
  if (typeof value === "number" || typeof value === "boolean") {
    return false;
  }

  // Handle Map and Set objects
  if (value instanceof Map || value instanceof Set) {
    return value.size === 0;
  }

  // Handle Arrays and Objects using Object.keys()
  // This works because Object.keys() returns:
  // - Array indices as strings for arrays
  // - Enumerable property names for objects
  return Object.keys(value).length === 0;
};

// Example usage:
console.log(isEmpty({})); // true
console.log(isEmpty([])); // true
console.log(isEmpty(null)); // true
console.log(isEmpty(undefined)); // true
console.log(isEmpty("   ")); // true
console.log(isEmpty(new Map())); // true
console.log(isEmpty(new Set())); // true
console.log(isEmpty({ key: "value" })); // false
console.log(isEmpty([1, 2, 3])); // false
console.log(isEmpty("hello")); // false
console.log(isEmpty(42)); // false
console.log(isEmpty(true)); // false
```

Let me explain the key improvements and insights that make this version more robust:

1. **Type-Specific Handling**: Instead of trying to handle everything with Object.keys(), we first identify the type of input and handle it appropriately. This is more precise and avoids potential edge cases.

2. **String Improvement**: We now handle strings properly by considering whitespace-only strings as empty (using `trim()`). This is often what users expect in real-world applications.

3. **Early Returns**: We handle simple cases (null, undefined, numbers, booleans) first. This makes the code more efficient and easier to understand.

4. **Modern Data Structures**: We properly handle Map and Set objects using their `size` property. The original version would incorrectly process these.

5. **Null Safety**: We removed the nullish coalescing operator (`??`) in favor of an explicit null check. This is more readable and handles both null and undefined in a clear way.

The most important insight here is the "progressive enhancement" approach:

1. Handle special cases first
2. Then handle primitive types
3. Finally fall back to the general Object.keys() approach for complex types

This pattern of "most specific to most general" is a valuable principle you can apply to many similar problems where you need to handle multiple data types uniformly.

Would you like me to explain any of these concepts in more detail? Or would you like to see how this function handles any specific edge cases?
