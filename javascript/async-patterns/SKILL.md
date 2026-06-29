---
name: javascript-async-patterns
description: JavaScript async/await patterns - Promises, error handling, concurrency, and modern async programming in browser and Node.js.
license: MIT
metadata:
  author: javascript-skills
  version: "1.0.0"
---

# JavaScript Async Patterns

Modern async programming patterns in JavaScript.

## Promise Basics

```javascript
// Creating promises
const delayedValue = (value, ms) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (value < 0) reject(new Error('Invalid value'));
      else resolve(value);
    }, ms);
  });
};

// Using promises
delayedValue(42, 1000)
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

## Async/Await

```javascript
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Failed:', error);
    throw error;
  }
}
```

## Sequential vs Parallel

```javascript
// Sequential (slower)
async function sequential() {
  const a = await fetch('/api/a');
  const b = await fetch('/api/b');
  const c = await fetch('/api/c');
  return [a, b, c];
}

// Parallel (faster)
async function parallel() {
  const [a, b, c] = await Promise.all([
    fetch('/api/a'),
    fetch('/api/b'),
    fetch('/api/c'),
  ]);
  return [a, b, c];
}

// Failed fast - all or nothing
async function parallelStrict() {
  const results = await Promise.all([
    fetch('/api/a'),
    fetch('/api/b'),
  ]);
  // If any fails, all fail
}

// Partial success - some may fail
async function parallelLoose(urls) {
  const results = await Promise.allSettled(
    urls.map(url => fetch(url))
  );
  return results.filter(r => r.status === 'fulfilled');
}
```

## Retry Pattern

```javascript
async function retry(fn, retries = 3, delay = 1000) {
  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Usage
const data = await retry(() => fetch('/api/data'), 3, 1000);
```

## Timeout Wrapper

```javascript
async function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) => {
    setTimeout(() => reject(new Error('Timeout')), ms);
  });
  
  return Promise.race([promise, timeout]);
}

// Usage
try {
  const data = await withTimeout(fetch('/api/data'), 5000);
} catch (error) {
  console.error('Request timed out');
}
```

## Debouncing Async

```javascript
function debounce(fn, delay) {
  let timeoutId;
  let pendingPromise;
  
  return (...args) => {
    clearTimeout(timeoutId);
    
    if (pendingPromise) {
      return pendingPromise;
    }
    
    timeoutId = setTimeout(async () => {
      pendingPromise = fn(...args);
      try {
        await pendingPromise;
      } finally {
        pendingPromise = null;
      }
    }, delay);
    
    return pendingPromise;
  };
}
```

## Best Practices

- Always `await` async functions
- Handle errors with try/catch in async functions
- Use `Promise.all` for parallel operations (when all must succeed)
- Use `Promise.allSettled` when partial success is OK
- Use `AbortController` for cancellable requests
- Prefer async/await over `.then()` chains for readability
- Don't forget `await` in loops - use `Promise.all` instead