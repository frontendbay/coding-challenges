# JavaScript Cancellable Interval Flashcards

## Basic Concepts

### Q: What is the primary purpose of `setInterval`?

`setInterval` is used to execute a function repeatedly at a specified time interval until explicitly stopped.

### Q: How do you stop a `setInterval`?

Use `clearInterval` with the interval ID that was returned by `setInterval`.

### Q: What's the difference between immediate and delayed first execution in intervals?

Immediate execution runs the function right away before starting the interval, while delayed waits for the first interval to pass.

## Implementation

### Q: What's the basic structure of a cancellable interval function?

```javascript
function createCancellableInterval(fn, args, interval) {
  fn(...args); // immediate execution
  const id = setInterval(fn, interval, ...args);
  return () => clearInterval(id);
}
```

### Q: Why store the interval ID in a closure?

To prevent external manipulation and ensure only the cancel function can access and clear the interval.

### Q: How do you prevent multiple cancellations of the same interval?

Nullify the interval ID after first cancellation:

```javascript
return function cancel() {
  if (intervalId) {
    clearInterval(intervalId);
    intervalId = null;
    return true;
  }
  return false;
};
```

## Error Handling

### Q: What are the essential parameter validations for a cancellable interval?

- Check if first parameter is a function

- Verify second parameter is an array
- Ensure interval is a positive number

### Q: How do you handle errors in periodic executions?

Wrap the function execution in try-catch and clean up the interval if an error occurs:

```javascript
intervalId = setInterval(() => {
  try {
    fn(...args);
  } catch (error) {
    clearInterval(intervalId);
    throw error;
  }
}, interval);
```

### Q: Why is immediate execution error handling important?

If the first execution fails, you need to clean up any created intervals to prevent memory leaks and invalid states.

## Best Practices

### Q: What makes a good cancel function?

- Returns boolean for success/failure feedback

- Prevents multiple cancellations
- Cleans up resources properly
- Uses closure for encapsulation

### Q: What should you document in a cancellable interval implementation?

- Parameter types and validation
- Return value and its meaning
- Error conditions and handling
- Usage examples
- Cleanup behavior

### Q: What are common real-world use cases for cancellable intervals?

- UI updates that need to be cleanable
- Polling mechanisms
- Animation loops
- Real-time data fetching
- Background tasks that need termination

## Advanced Concepts

### Q: How would you implement a maximum execution count?

Keep a counter in the closure and clear the interval when the count is reached:

```javascript
function createLimitedInterval(fn, args, interval, maxCount) {
  let count = 0;
  const id = setInterval(() => {
    if (count >= maxCount) {
      clearInterval(id);
      return;
    }
    fn(...args);
    count++;
  }, interval);
  return () => clearInterval(id);
}
```

### Q: What's the difference between cleaning up and pausing an interval?

Cleaning up completely removes the interval and requires new setup to restart, while pausing maintains the state and can resume from where it left off.

### Q: How can you implement variable intervals?

Instead of using `setInterval`, use nested `setTimeout` calls where each execution can set a different delay for the next one.
