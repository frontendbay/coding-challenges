# Insertion Sort

> Source: [https://www.greatfrontend.com/questions/javascript/insertion-sort?list=data-structures-algorithms](https://www.greatfrontend.com/questions/javascript/insertion-sort?list=data-structures-algorithms)

## Attempted solution:

```js
/**
 * @param {Array<number>} arr The input integer array to be sorted.
 * @return {Array<number>}
 */
export default function insertionSort(arr) {
  // Main loop through array starting from second element
  for (let i = 1; i < arr.length; i++) {
    // Track current position of our target element
    let currentPos = i;

    // Compare and shift elements just like before, but update currentPos
    for (let j = i - 1; j >= 0; j--) {
      if (arr[j] > arr[currentPos]) {
        // Same swap logic as before
        const target = arr[currentPos];
        arr[currentPos] = arr[j];
        arr[j] = target;
        // Update where our target element has moved to
        currentPos = j;
      }
    }
  }

  return arr;
}
```

## Key Insights:

### The Index Tracking Pattern

When working with arrays, one of the most subtle yet common sources of bugs comes from incorrectly tracking elements as they move within the array. The insertion sort implementation revealed this pattern clearly.

#### The Core Challenge

The fundamental issue arises when we need to:

1. Move elements within an array
2. Keep track of where specific elements are after they move
3. Continue operating on these elements in their new positions

This creates a disconnect between:

- The original position we remember
- The current position where the element actually is

#### Mental Model: The Moving Card

Think of it like tracking a specific card in a deck as you sort them. You have two choices:

1. Keep your finger on the empty space where you picked up the card (incorrect approach)
2. Move your finger along with the card as it finds its new position (correct approach)

The first approach fails because you lose track of where your target element actually is. This is what happened in the original insertion sort implementation where `i` kept pointing to the original position even after the element moved.

### Recognition Patterns: When to Watch Out

Look for these signals that you might need careful index tracking:

1. **Multiple Passes Through Array**
   When your code needs to revisit elements multiple times, especially if those elements might have moved since the last visit.

2. **Swapping Operations**
   Whenever you're swapping elements, ask yourself: "Do I need to know where these elements end up?"

3. **Position-Dependent Comparisons**
   If you're comparing elements based on their positions, ensure you're using their current positions, not where they used to be.

### Solution Strategies

#### Strategy 1: Position Variable Tracking

```javascript
let currentPos = i; // Track current position
if (needToMove) {
  swap(arr, currentPos, newPos);
  currentPos = newPos; // Update tracking
}
```

#### Strategy 2: Element Storage

```javascript
const elementToMove = arr[i]; // Store element value
// Move other elements
arr[finalPosition] = elementToMove; // Place in final spot
```

#### Strategy 3: Reference Arrays

```javascript
const positions = new Array(n).fill(0).map((_, i) => i);
// When swapping elements in main array, also swap in positions array
```

### Common Pitfalls

1. **Stale Index References**

   - Problem: Using original indices after elements have moved
   - Solution: Update index variables after each movement operation

2. **Lost Elements**

   - Problem: Overwriting elements without preserving their values
   - Solution: Store elements temporarily before moving others

3. **Infinite Loops**
   - Problem: Index updates causing endless cycles
   - Solution: Ensure index progress is consistent and bounded

### Testing Strategies

When dealing with index tracking problems, test these specific cases:

1. **Multiple Movements**
   Input that requires the same element to move multiple times
   Example: [5,4,3,2,1] for sorting

2. **Edge Position Movements**
   Elements moving to/from array boundaries
   Example: [5,1,2,3,4]

3. **No Movement Required**
   Input where elements are already in correct positions
   Example: [1,2,3,4,5]

### Related Problem Patterns

This pattern appears in many common algorithm problems:

1. **Sorting Algorithms**

   - Bubble Sort
   - Selection Sort
   - Insertion Sort

2. **Array Rearrangement**

   - Dutch National Flag Problem
   - Move Zeros to End
   - Segregate Positive and Negative Numbers

3. **Cyclic Sort Problems**
   - Find Missing Number
   - Find Duplicate Number
   - Array Permutation

### Implementation Techniques

#### Clear Variable Naming

```javascript
// Unclear
let j = i;
// Clear
let currentElementPosition = initialPosition;
```

#### State Documentation

```javascript
// Document array state at critical points
function moveElement(arr, from, to) {
  console.log(`Before move: ${arr}`);
  // movement logic
  console.log(`After move: ${arr}`);
}
```

#### Invariant Maintenance

Keep track of what should remain true throughout your algorithm:

```javascript
function assertInvariant(arr, message) {
  // Check your invariant conditions
  if (!conditionMaintained) {
    throw new Error(`Invariant violated: ${message}`);
  }
}
```

### Debugging Approach

When debugging index tracking problems:

1. Start with a small array (4-5 elements)
2. Track the position of a single element through all operations
3. Print array state after each movement
4. Verify index updates match actual element positions

### Key Takeaways

1. Always update position trackers immediately after moving elements
2. Consider using temporary storage for complex movements
3. Test with inputs requiring multiple movements
4. Document array state transitions
5. Use clear, descriptive variable names for positions and indices

Remember: The key to solving these problems is maintaining a clear mental model of where elements are at each step of your algorithm. When in doubt, draw out the array state and element movements on paper first.

## A better solution

Walkthrough a cleaner and more intuitive solution for insertion sort, explaining each part as we build it.

```javascript
/**
 * Insertion Sort Implementation
 * Time Complexity: O(n²)
 * Space Complexity: O(1)
 *
 * @param {Array<number>} arr The input array to be sorted
 * @returns {Array<number>} The sorted array
 */
export default function insertionSort(arr) {
  // Start from the second element since a single element is already "sorted"
  for (let i = 1; i < arr.length; i++) {
    // Store the current element we want to insert into the sorted portion
    let elementToInsert = arr[i];

    // This will keep track of where we're looking in the sorted portion
    let sortedIndex = i - 1;

    // Keep moving elements to the right while:
    // 1. We haven't reached the start of the array AND
    // 2. The element we're looking at is bigger than what we want to insert
    while (sortedIndex >= 0 && arr[sortedIndex] > elementToInsert) {
      // Shift this element one position to the right
      arr[sortedIndex + 1] = arr[sortedIndex];
      sortedIndex--;
    }

    // We've found where our element belongs - insert it
    arr[sortedIndex + 1] = elementToInsert;
  }

  return arr;
}
```

Let me explain why this solution is better than the previous approaches:

1. **Clear Mental Model**: Think of it like sorting cards in your hand from left to right. You pick up a new card (elementToInsert) and move larger cards right until you find where it belongs.

2. **More Efficient**: Instead of doing multiple swaps, we:

   - Save the element we're trying to insert
   - Shift larger elements right by one position
   - Place our element in the correct spot once we find it
     This reduces the number of array writes.

3. **Better Variable Names**: Variables like `elementToInsert` and `sortedIndex` clearly communicate their purpose, making the code self-documenting.

Let's see how it works with an example array [5, 2, 4, 1]:

```javascript
// Initial: [5, 2, 4, 1]

// i = 1: Looking at 2
// elementToInsert = 2
// [5, 5, 4, 1]  // Shifted 5 right
// [2, 5, 4, 1]  // Placed 2

// i = 2: Looking at 4
// elementToInsert = 4
// [2, 5, 5, 1]  // No need to shift further
// [2, 4, 5, 1]  // Placed 4

// i = 3: Looking at 1
// elementToInsert = 1
// [2, 4, 5, 5]  // Shifted 5
// [2, 4, 4, 5]  // Shifted 4
// [2, 2, 4, 5]  // Shifted 2
// [1, 2, 4, 5]  // Placed 1
```

The key improvements over the previous solutions are:

1. No tracking of moving positions needed - we save our element before we start moving things
2. More efficient - only one write operation per position instead of multiple swaps
3. Clearer logic - follows how you'd naturally sort cards in your hand
4. Better readability with meaningful variable names and comments
