# Core Concept Flashcards

### What is the fundamental purpose of Object.keys() in emptiness checking?

`Object.keys()` provides a uniform way to check both objects and arrays because it returns an array of enumerable property names for objects and index strings for arrays. This allows for a single method to handle both data structures.

### What is the "progressive enhancement" approach in type checking?

It's a strategy where you handle cases from most specific to most general: first special cases (null/undefined), then primitive types (strings, numbers, booleans), and finally complex types (objects/arrays). This creates more robust and maintainable code.

### Why should we handle strings differently when checking for emptiness?

Strings often need special handling because users typically consider whitespace-only strings as empty. Using .trim() before checking length ensures that strings containing only spaces are considered empty, matching user expectations.

### What's the advantage of using instanceof for Map and Set?

The instanceof operator allows us to identify and properly handle special object types like Map and Set, which have their own emptiness property (size). This provides more accurate results than treating them as regular objects.

### Why is early return important in type checking functions?

Early returns for simple cases (null, undefined, primitives) make the code more efficient by avoiding unnecessary checks and more readable by handling simple cases first. It follows the principle of failing fast and handling edge cases explicitly.

# Edge Cases Flashcards

### How should isEmpty() handle null and undefined?

Both null and undefined should return true because they represent the absence of any value. Using (value == null) catches both cases in a single check.

### What should isEmpty() return for numbers and booleans?

Numbers and booleans should always return false because they are primitive values that always have meaning - they can't be "empty" in the traditional sense.

### How should isEmpty() handle whitespace strings?

Strings containing only whitespace should return true when using isEmpty(). This is achieved by using .trim() before checking the length, matching common user expectations.

# Implementation Pattern Flashcards

### What is the pattern for handling multiple data types uniformly?

First identify the type of input, then apply type-specific logic:

1. Handle special cases (null/undefined)
2. Handle primitive types (strings, numbers, booleans)
3. Handle special objects (Map, Set)
4. Fall back to general object/array handling

### What's the pattern for safe type checking in JavaScript?

Use a combination of:

1. typeof for primitives (string, number, boolean)
2. instanceof for built-in objects (Map, Set)
3. Object.keys() as a fallback for general objects/arrays

# Code Improvement Flashcards

### How can the original isEmpty implementation be improved for safety?

Replace the nullish coalescing operator (??) with explicit null checking:

- Original: return !Object.keys(obj ?? {})?.length
- Improved: if (value == null) return true

### What's the pattern for handling empty strings properly?

Combine trim() with length check:

```javascript
if (typeof value === "string") {
  return value.trim().length === 0;
}
```

# Best Practices Flashcards

### What are the key principles for writing robust type-checking functions?

1. Handle edge cases explicitly
2. Progress from specific to general cases
3. Consider user expectations
4. Use built-in type checking methods
5. Add clear documentation
6. Include comprehensive tests

### Why is it important to document type-checking functions thoroughly?

Type-checking functions often handle multiple data types and edge cases. Good documentation helps other developers understand:

1. What types are supported
2. How edge cases are handled
3. What "emptiness" means for each type
4. Expected return values
