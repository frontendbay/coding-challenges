# 2725. Interval Cancellation

> Source: [https://leetcode.com/problems/interval-cancellation/description/?envType=study-plan-v2&envId=30-days-of-javascript](https://leetcode.com/problems/interval-cancellation/description/?envType=study-plan-v2&envId=30-days-of-javascript)

## Attempted Solution

```javascript
/**
 * @param {Function} fn
 * @param {Array} args
 * @param {number} t
 * @return {Function}
 */
var cancellable = function (fn, args, t) {
  fn(...args);
  let intervalId = setInterval(fn, t, ...args);

  return () => clearInterval(intervalId);
};
```

## Key Insights

The key insight from this problem is understanding how to create a cancellable periodic function using JavaScript's timing functions. Here are the main points:

1. `setInterval` returns an ID that can be used later with `clearInterval` to stop the periodic execution. This forms a clean cancel mechanism.

2. For periodic tasks that need to be cancellable:
   - Use `setInterval` to set up the recurring execution
   - Return a cancel function that calls `clearInterval` with the saved interval ID
   - Execute the function once immediately before setting up the interval (if immediate execution is needed)

The pattern looks like this:

```javascript
function createCancellableTask(fn) {
  fn(); // immediate execution if needed
  const intervalId = setInterval(fn, delay);
  return () => clearInterval(intervalId);
}
```

This is a common pattern in JavaScript, especially in:

- UI updates that need to be cleanable
- Polling mechanisms that need to be stoppable
- Animation loops that need to be cancellable
- Real-time data fetching that needs to be terminated

Understanding this pattern helps you implement cancellable behaviors cleanly while avoiding memory leaks by properly cleaning up intervals when they're no longer needed.

## Better Solution

I'll help create a more robust and maintainable version of this code with better error handling, type checking, and clearer documentation. Let me walk you through the improvements:

```javascript
/**
 * Creates a cancellable periodic function execution with immediate first call
 *
 * @param {Function} fn - The function to execute periodically
 * @param {Array} args - Arguments to pass to the function
 * @param {number} intervalMs - Interval between executions in milliseconds
 * @returns {Function} Cancel function that stops future executions
 * @throws {TypeError} If invalid parameter types are provided
 *
 * @example
 * const logNumber = (x) => console.log(x);
 * const cancel = createCancellableInterval(logNumber, [42], 1000);
 * // Later: cancel() to stop execution
 */
function createCancellableInterval(fn, args, intervalMs) {
  // Validate parameters to catch errors early
  if (typeof fn !== "function") {
    throw new TypeError("First parameter must be a function");
  }
  if (!Array.isArray(args)) {
    throw new TypeError("Second parameter must be an array of arguments");
  }
  if (typeof intervalMs !== "number" || intervalMs <= 0) {
    throw new TypeError("Third parameter must be a positive number");
  }

  // Store interval ID in closure to prevent external manipulation
  let intervalId;

  try {
    // Execute immediately for consistent behavior
    fn(...args);

    // Set up periodic execution
    intervalId = setInterval(() => {
      try {
        fn(...args);
      } catch (error) {
        // Clean up interval if execution fails
        clearInterval(intervalId);
        throw error; // Re-throw to notify caller
      }
    }, intervalMs);

    // Return cancel function with clear success/failure feedback
    return function cancel() {
      if (intervalId) {
        clearInterval(intervalId);
        intervalId = null; // Prevent multiple cancellations
        return true; // Indicate successful cancellation
      }
      return false; // Indicate already cancelled
    };
  } catch (error) {
    // Clean up if initial execution fails
    if (intervalId) {
      clearInterval(intervalId);
    }
    throw error;
  }
}

// Example usage with error handling
try {
  const logTime = () => console.log(new Date().toISOString());
  const cancel = createCancellableInterval(logTime, [], 1000);

  // Cancel after 5 seconds
  setTimeout(() => {
    const cancelled = cancel();
    console.log(`Execution cancelled: ${cancelled}`);
  }, 5000);
} catch (error) {
  console.error("Error in periodic execution:", error);
}
```

Let me explain the key improvements:

1. **Better Error Handling**:

   - Parameter validation at the start prevents cryptic errors later
   - Try-catch blocks handle errors in both initial and periodic executions
   - Proper cleanup when errors occur to prevent memory leaks

2. **Clearer Documentation**:

   - Detailed JSDoc comments explain parameters, return values, and usage
   - Example code shows how to use the function
   - Comments explain the reasoning behind implementation choices

3. **Improved Cancel Function**:

   - Returns boolean to indicate success/failure of cancellation
   - Prevents multiple cancellations by nullifying the interval ID
   - Closure prevents external manipulation of the interval ID

4. **Better Naming**:

   - More descriptive function name `createCancellableInterval`
   - Clear parameter names that indicate their purpose
   - Consistent naming conventions throughout

5. **Robust Implementation**:
   - Handles edge cases like immediate execution errors
   - Prevents memory leaks by cleaning up intervals on error
   - Uses closure for better encapsulation

This implementation is more production-ready because it:

- Fails fast with clear error messages
- Prevents resource leaks
- Provides better feedback to callers
- Is easier to maintain and debug
- Has clear documentation for future developers

You could expand this further by adding features like:

- Maximum execution count
- Variable intervals
- Pause/resume functionality
- Error retry logic

Would you like me to explain any of these improvements in more detail?
