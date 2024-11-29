# Isomorphic Strings - Study Cards

## Concept Cards

### Q: What does it mean for two strings to be isomorphic?

A: Two strings are isomorphic when they follow the same pattern of character relationships. Each character in the first string must map to exactly one unique character in the second string, and vice versa. For example, 'egg' and 'add' are isomorphic because they both follow the pattern "first char, first char, second char".

### Q: What is the key principle for checking if strings are isomorphic?

A: The key principle is bi-directional mapping validation. You must verify the mapping in both directions: from first string to second string AND from second string to first string. Each character must have a consistent, one-to-one relationship with its corresponding character.

### Q: Why do we need to check mapping in both directions?

A: Checking both directions ensures that no two different characters map to the same character. For example, with strings "badc" and "baba", checking only from first to second string wouldn't catch that both 'd' and 'c' are trying to map to 'a'.

## Implementation Cards

### Q: What is the time complexity for checking if two strings are isomorphic?

A: The time complexity is O(n), where n is the length of the input strings. We need to traverse each character exactly once to establish and verify the mappings.

### Q: What is the space complexity for the array-based implementation?

A: The space complexity is O(k), where k is the size of the character set (constant for ASCII = 128). We use two fixed-size arrays to store the character mappings, regardless of input string length.

### Q: Why use arrays instead of objects for character mapping?

A: Arrays provide faster lookups and better memory usage for ASCII strings. By using character codes as indices, we get O(1) access time and avoid the overhead of object property lookups.

### Q: What is the purpose of initializing arrays with -1?

A: The value -1 serves as a sentinel value indicating that no mapping has been established yet for that character. This helps distinguish between unmapped characters and actual character code mappings.

## Code Pattern Cards

### Q: What's the pattern for early validation in isomorphic checking?

A: Check for length mismatch at the start:

```javascript
if (s.length !== t.length) {
  return false;
}
```

This prevents unnecessary processing of strings that cannot be isomorphic.

### Q: What's the pattern for establishing and verifying character mappings?

A: For each character pair:

1. Check if current mapping contradicts existing mapping in either direction
2. If no contradiction, establish new mapping in both directions

```javascript
if (sToT[sChar] !== -1 && sToT[sChar] !== tChar) return false;
if (tToS[tChar] !== -1 && tToS[tChar] !== sChar) return false;
sToT[sChar] = tChar;
tToS[tChar] = sChar;
```

## Application Cards

### Q: Where else can the bi-directional mapping pattern be useful?

A: This pattern is valuable in:

- Word pattern matching
- Substitution cipher validation
- Character encoding validation
- Symbol mapping in compiler design
- Any problem requiring one-to-one relationships between elements

### Q: What are some variations of the isomorphic pattern?

A: Common variations include:

- Case-sensitive vs case-insensitive mapping
- Multi-character mapping instead of single-character
- Mapping with additional constraints (e.g., preserving alphabetical order)
- Mapping across different character sets or encodings

## Debug and Edge Cases Cards

### Q: What are important edge cases to consider for isomorphic strings?

A: Key edge cases include:

- Empty strings
- Single-character strings
- Strings with repeated characters
- Strings with special characters
- Strings of different lengths
- Unicode characters beyond ASCII

### Q: What are common bugs to watch for in implementation?

A: Common pitfalls include:

- Forgetting to check mapping in both directions
- Not handling character encoding properly
- Incorrect initialization of mapping storage
- Not considering case sensitivity requirements
- Memory inefficiency with large character sets
