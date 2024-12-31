# 1652. Defuse the Bomb

> Source: [https://leetcode.com/problems/defuse-the-bomb/description](https://leetcode.com/problems/defuse-the-bomb/description)

## Attempted Solution

```javascript
/**
 * @param {number[]} code
 * @param {number} k
 * @return {number[]}
 */
var decrypt = function (code, k) {
  let result;
  const size = code.length;

  if (!k) return code.fill(0); // Base case: k=0 means fill with zeros

  return code.map((num, i, arr) => {
    // Create array of k elements to sum
    const sumList = new Array(Math.abs(k)).fill(0).map((item, j, list) => {
      // Calculate circular index based on k's sign
      const result = i + (k > 0 ? list.length - j : j - list.length);
      // Ensure proper circular wrapping with double modulo
      const index = ((result % size) + size) % size;
      return arr[index];
    });

    // Sum all elements to get new value
    return sumList.reduce((acc, curr) => acc + curr, 0);
  });
};
```

## Key Insights

The key insight is handling circular array indexing when summing elements. The critical line is:

```javascript
const index = ((result % size) + size) % size;
```

This double modulo pattern ensures proper wraparound for both positive and negative indices, avoiding array out-of-bounds errors. The pattern works by:

1. First modulo to handle large numbers
2. Add array length to handle negatives
3. Second modulo to wrap back within bounds

This is a reusable solution whenever you need to implement circular/cyclic array access in similar problems.

## Better Solution

```javascript
/**
 * Decrypts an array by replacing each element with the sum of k elements
 * ahead (k > 0) or behind (k < 0) it in a circular manner
 *
 * @param {number[]} code - Input array to decrypt
 * @param {number} k - Number of elements to sum (positive looks ahead, negative looks behind)
 * @return {number[]} - Decrypted array
 *
 * Time: O(n), where n is code.length
 * Space: O(n) for the result array
 */
var decrypt = function (code, k) {
  const n = code.length;

  // Base case: when k is 0, return array filled with zeros
  if (k === 0) return new Array(n).fill(0);

  // Create result array
  const result = new Array(n);

  // Calculate initial sliding window sum
  let windowSum = 0;
  for (let i = 0; i < Math.abs(k); i++) {
    // If k > 0, sum elements after index 0
    // If k < 0, sum elements before index 0 (wrap around from end)
    const index = k > 0 ? i + 1 : n + k + i;
    windowSum += code[index % n];
  }

  // Fill result array using sliding window technique
  for (let i = 0; i < n; i++) {
    // Store current window sum
    result[i] = windowSum;

    // Update window: remove element going out, add element coming in
    if (k > 0) {
      // Remove element at current index + 1
      windowSum -= code[(i + 1) % n];
      // Add element at (current index + k + 1)
      windowSum += code[(i + Math.abs(k) + 1) % n];
    } else {
      // Remove element at (current index + k)
      windowSum -= code[(i + k + n) % n];
      // Add element at current index
      windowSum += code[i];
    }
  }

  return result;
};
```

This improved version offers several advantages:

1. **Better Performance**: Instead of recalculating the sum for each index, it uses a sliding window technique. This reduces our time complexity from O(n\*k) to O(n).

2. **Memory Efficiency**: We only create one result array instead of creating temporary arrays for each element.

3. **Cleaner Logic**: The code separates the initialization of the sliding window from the main calculation loop, making it easier to understand and maintain.

4. **Better Handling of Circular Nature**: Rather than using a double modulo pattern, we use a simpler modulo operation with proper offset calculations.

Let's break down how it works:

1. First, we handle the base case where k = 0.

2. Then, we calculate an initial window sum for the first position. This sum includes |k| elements starting from the appropriate position based on whether k is positive or negative.

3. For the main calculation:
   - If k > 0: We look ahead, so we remove the element right after current position and add the element k positions ahead
   - If k < 0: We look behind, so we remove the element k positions behind and add the current element

This approach is more efficient because:

- Each element is only added/subtracted once as the window slides
- We don't create any temporary arrays
- We minimize the number of modulo operations needed

The sliding window technique is a powerful pattern that you can apply to many similar problems where you need to calculate sums of consecutive elements in a circular array.
