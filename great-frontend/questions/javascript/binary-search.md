# Binary Search

> Source: [https://www.greatfrontend.com/questions/javascript/binary-search?list=data-structures-algorithms](https://www.greatfrontend.com/questions/javascript/binary-search?list=data-structures-algorithms)

## Attempted solution:

```js
/**
 * @param {Array<number>} arr The input integer array to be searched.
 * @param {number} target The target integer to search within the array.
 * @return {number} The index of target element in the array, or -1 if not found.
 */
export default function binarySearch(arr, target) {
  let midIndex = Math.ceil(arr.length / 2);

  if (arr.length === 0) return -1;
  if (arr[0] === target) return 0;
  if (arr[arr.length - 1] === target) return arr.length - 1;

  if (arr[midIndex] === target) return midIndex;

  if (target < arr[midIndex])
    return binarySearch(arr.slice(0, midIndex), target);

  if (target > arr[midIndex]) {
    const rightResult = binarySearch(arr.slice(midIndex), target);
    return rightResult === -1 ? -1 : rightResult + midIndex;
  }

  return -1;
}
```

## Key Insights:

### Index Management in Recursion

When slicing arrays in recursive calls:

- Sliced arrays reset indices to 0
- Must track position relative to original array
- For right half searches: add offset of skipped elements
- Offset = elements removed from left side
- Example: `rightResult + (midIndex + 1)` when slicing after midIndex

### Base Cases Strategy

1. Start with emptiness check

   - Handle empty array first (e.g., return -1 or [])
   - Prevents unnecessary processing
   - Guards against invalid slicing

2. Check objective immediately after
   - Test target condition (e.g., found element)
   - Return result if found
   - No extra checks needed for single elements

### Recursive Logic Flow

1. Validate input/base cases
2. Process current state (e.g., check middle element)
3. Choose recursive direction (e.g., left vs right)
4. Adjust returned values from recursive calls
5. Include catch-all return for edge cases

### Avoiding Common Pitfalls

- Double-checking elements unnecessarily
- Over-complicated base cases
- Missing index adjustments after slicing
- Forgetting catch-all return statement
- Not handling empty array cases first

### Pattern Template

```javascript
function recursiveArrayFunc(arr, target) {
  // 1. Base case - emptiness
  if (arr.length === 0) return baseValue;

  // 2. Process current state
  let midIndex = Math.floor(arr.length / 2);
  if (foundTarget) return result;

  // 3. Choose direction & recurse
  if (goLeft) {
    return recursiveArrayFunc(arr.slice(0, midIndex));
  }

  // 4. Handle right recursion with offset
  const rightResult = recursiveArrayFunc(arr.slice(midIndex + 1));
  return rightResult === baseValue ? baseValue : rightResult + offset;

  // 5. Catch-all
  return baseValue;
}
```

## Testing Strategy

Test cases should cover:

- Empty array
- Single element array
- Two element array
- Even vs odd length arrays
- Target at start/middle/end
- Target not present
- Multiple occurrences of target

## A better solution

We can create a more elegant and efficient binary search solution. Let's build it step by step with a clear understanding of why each part matters.

```javascript
/**
 * Binary Search Implementation
 * Time Complexity: O(log n)
 * Space Complexity: O(1) without recursion, O(log n) with recursion due to call stack
 *
 * Instead of slicing arrays, we'll use pointers to track our search space.
 * This is more efficient as it avoids creating new arrays.
 *
 * @param {Array<number>} arr - Sorted array to search in
 * @param {number} target - Value to find
 * @return {number} - Index of target or -1 if not found
 */
function binarySearch(arr, target) {
  // Helper function that does the actual searching
  // We use this to avoid redefining left and right in recursive calls
  const search = (left, right) => {
    // Base case: search space is exhausted
    if (left > right) return -1;

    // Calculate middle index
    // Using (left + right) >>> 1 instead of Math.floor((left + right) / 2)
    // This avoids potential integer overflow and is more efficient
    const mid = (left + right) >>> 1;

    // Found target - return its position
    if (arr[mid] === target) return mid;

    // Target must be in left half if it exists
    if (target < arr[mid]) {
      return search(left, mid - 1);
    }

    // Target must be in right half if it exists
    return search(mid + 1, right);
  };

  // Start the search using the entire array
  return search(0, arr.length - 1);
}

// Alternative iterative version - often preferred in practice
function binarySearchIterative(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = (left + right) >>> 1;

    if (arr[mid] === target) return mid;

    if (target < arr[mid]) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  return -1;
}
```

Let me explain the key improvements and why they matter:

1. **Pointer-based Approach**
   Instead of slicing arrays, we use pointers (`left` and `right`) to track our search space. This is more efficient because:

   - We avoid creating new arrays with each recursive call
   - We maintain constant space complexity in the iterative version
   - We don't need to manage index offsets like we did with slicing

2. **Efficient Middle Index Calculation**

   ```javascript
   const mid = (left + right) >>> 1;
   ```

   This uses the unsigned right shift operator (`>>>`) which is both more efficient than `Math.floor()` and handles potential integer overflow. In JavaScript, this is a minor optimization, but it's good practice and common in other languages.

3. **Cleaner Base Case**
   We have just one simple base case: `if (left > right)`. This naturally handles all scenarios where the target isn't found, making our code more concise and easier to understand.

4. **Helper Function Pattern**
   Using a helper function with closure over the array and target means:

   - We don't need to pass these unchanged values in every recursive call
   - The interface stays clean with just the essential parameters
   - We encapsulate the implementation details

5. **Alternative Iterative Version**
   I've included an iterative version because:
   - It's often preferred in practice due to better space complexity
   - It's easier for the JavaScript engine to optimize
   - It avoids potential stack overflow with very large arrays

The iterative version is especially useful when working with large datasets where stack space might be a concern. Both versions maintain the same O(log n) time complexity, but the iterative version achieves O(1) space complexity compared to the recursive version's O(log n).
