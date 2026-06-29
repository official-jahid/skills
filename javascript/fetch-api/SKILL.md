---
name: javascript-fetch-api
description: JavaScript Fetch API patterns - HTTP requests, error handling, async/await, and JSON handling for browser and Node.js environments.
license: MIT
metadata:
  author: javascript-skills
  version: "1.0.0"
---

# JavaScript Fetch API

Modern HTTP request patterns using the Fetch API.

## Basic GET Request

```javascript
async function fetchData(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Fetch failed:', error);
    throw error;
  }
}
```

## POST Request with JSON

```javascript
async function postData(url, data) {
  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(data),
  });
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  return await response.json();
}
```

## GET Request with Headers

```javascript
async function fetchWithHeaders(url, token) {
  const response = await fetch(url, {
    headers: {
      'Authorization': `Bearer ${token}`,
      'Accept': 'application/json',
    },
  });
  return response.json();
}
```

## Error Handling

```javascript
async function robustFetch(url) {
  try {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), 5000);
    
    const response = await fetch(url, {
      signal: controller.signal,
    });
    
    clearTimeout(timeoutId);
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    return await response.json();
  } catch (error) {
    if (error.name === 'AbortError') {
      throw new Error('Request timeout');
    }
    throw error;
  }
}
```

## Multiple Concurrent Requests

```javascript
async function fetchMultiple(urls) {
  const promises = urls.map(url => fetch(url).then(res => res.json()));
  const results = await Promise.allSettled(promises);
  
  return results.map((result, index) => ({
    url: urls[index],
    data: result.status === 'fulfilled' ? result.value : null,
    error: result.status === 'rejected' ? result.reason : null,
  }));
}
```

## File Upload

```javascript
async function uploadFile(url, file) {
  const formData = new FormData();
  formData.append('file', file);
  
  const response = await fetch(url, {
    method: 'POST',
    body: formData,
  });
  
  return await response.json();
}
```

## Best Practices

- Always check `response.ok` before parsing
- Use `AbortController` for timeout handling
- Handle network errors with try/catch
- Use `Promise.allSettled` for multiple requests when you want all results
- Set appropriate headers for Content-Type and Accept
- In Node.js 18+, fetch is built-in; for older versions use `node-fetch`