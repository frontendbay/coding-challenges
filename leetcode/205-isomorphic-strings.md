# 205. Isomorphic String

> Source: [https://leetcode.com/problems/isomorphic-strings](https://leetcode.com/problems/isomorphic-strings)

## Attempted Solution

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isIsomorphic = function (s, t) {
  const mapLR = {};
  const mapRL = {};

  if (s.length != t.length) return false;

  for (let i = 0; i < s.length; i++) {
    const char = s[i];
    const target = t[i];

    if (mapLR[char] && mapLR[char] !== target) return false;
    if (mapRL[target] && mapRL[target] !== char) return false;

    mapLR[char] = target;
    mapRL[target] = char;
  }

  return true;
};
```

## Key Insights

The key insight is bi-directional mapping validation. When checking if two strings are isomorphic (have a consistent one-to-one character mapping), you need to verify the mapping in both directions:

1. From s → t (mapLR): Each character in s must map to exactly one character in t
2. From t → s (mapRL): Each character in t must map to exactly one character in s

This pattern is useful for problems involving one-to-one relationships or bijective mappings between elements, like word patterns, string transformations, or validating substitution ciphers.

## Better solution

I'll help you write a more efficient and clearer version of the isomorphic strings checker. Let me explain the approach and then show you the improved code with detailed comments.

The core concept remains the same (bidirectional mapping), but we can make several improvements in terms of readability, performance, and error handling. Here's the enhanced version:

```javascript
/**
 * Determines if two strings are isomorphic - meaning they follow the same pattern
 * of character relationships. For example, 'egg' and 'add' are isomorphic because
 * both have a pattern of "first char, first char, second char"
 *
 * Time Complexity: O(n) where n is the length of input strings
 * Space Complexity: O(k) where k is the size of character set (constant for ASCII)
 *
 * @param {string} s - First input string
 * @param {string} t - Second input string
 * @return {boolean} - True if strings are isomorphic, false otherwise
 */
function isIsomorphic(s, t) {
  // Early return for strings of different lengths
  if (s.length !== t.length) {
    return false;
  }

  // Use arrays instead of objects for faster lookups
  // ASCII has 128 basic characters, so we use arrays of that size
  const sToT = new Array(128).fill(-1); // Maps s chars to t chars
  const tToS = new Array(128).fill(-1); // Maps t chars to s chars

  for (let i = 0; i < s.length; i++) {
    // Get ASCII codes for current characters
    const sChar = s.charCodeAt(i);
    const tChar = t.charCodeAt(i);

    // If current mapping doesn't match previous mapping in either direction
    // In other words, has this character been mapped before (sToT[sChar] !== -1)
    // If yes, does it map to a different character than current? (sToT[sChar] !== tChar)
    if (sToT[sChar] !== -1 && sToT[sChar] !== tChar) {
      return false;
    }
    if (tToS[tChar] !== -1 && tToS[tChar] !== sChar) {
      return false;
    }

    // Update mappings in both directions
    sToT[sChar] = tChar;
    tToS[tChar] = sChar;
  }

  return true;
}
```

Key improvements in this version:

1. **Array-based Mapping**: Instead of using objects, we use arrays indexed by ASCII character codes. This provides faster lookups and slightly better memory usage for ASCII strings.

2. **Early Validation**: We check for length mismatch right at the start to avoid unnecessary processing.

3. **Character Code Usage**: Using `charCodeAt()` instead of direct character access eliminates the need for string-to-character conversion.

4. **Clear Documentation**: The code includes a comprehensive JSDoc comment explaining the purpose, complexity, and parameters.

5. **Meaningful Variable Names**: Variables like `sToT` and `tToS` clearly indicate their purpose in mapping characters between strings.

You can use this function like this:

```javascript
console.log(isIsomorphic("egg", "add")); // true
console.log(isIsomorphic("foo", "bar")); // false
console.log(isIsomorphic("paper", "title")); // true
```

The key advantage of this implementation is that it's both more efficient (using array lookups instead of object properties) and more maintainable (with clear documentation and structure). The use of character codes also makes it more efficient for string processing.

Would you like me to explain any particular part of this implementation in more detail?
