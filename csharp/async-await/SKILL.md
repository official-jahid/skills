---
name: csharp-async-await
description: C# async/await patterns - Task-based asynchronous programming, ConfigureAwait, cancellation tokens, and async best practices.
license: MIT
metadata:
  author: csharp-skills
  version: "1.0.0"
---

# C# Async/Await

Task-based asynchronous programming patterns in C#.

## Basic Async Pattern

```csharp
public async Task<string> FetchDataAsync()
{
    using var client = new HttpClient();
    string result = await client.GetStringAsync("https://api.example.com");
    return result;
}
```

## Async Void - Only for Event Handlers

```csharp
// Correct - event handler
public async void Button_Click(object sender, EventArgs e)
{
    await DoWorkAsync();
}

// Wrong - use Task instead
public async Task DoWorkAsync()
{
    await Task.Delay(1000);
}
```

## ConfigureAwait

```csharp
// Library code - use ConfigureAwait(false)
public async Task<string> GetDataAsync()
{
    var result = await client.GetStringAsync("url").ConfigureAwait(false);
    return result.ToUpper();
}

// App code (UI/context aware) - default ConfigureAwait(true)
public async Task<string> GetDataAsync()
{
    var result = await client.GetStringAsync("url");
    return result.ToUpper();
}
```

## Cancellation Tokens

```csharp
public async Task<string> FetchWithCancellationAsync(
    CancellationToken cancellationToken = default)
{
    using var client = new HttpClient();
    var response = await client.GetAsync("url", cancellationToken);
    cancellationToken.ThrowIfCancellationRequested();
    return await response.Content.ReadAsStringAsync(cancellationToken);
}

// Usage
var cts = new CancellationTokenSource(TimeSpan.FromSeconds(5));
try
{
    var data = await FetchWithCancellationAsync(cts.Token);
}
catch (OperationCanceledException)
{
    Console.WriteLine("Request timed out");
}
```

## Parallel Async Operations

```csharp
// Sequential (slower)
var results = new List<string>();
foreach (var url in urls)
{
    results.Add(await client.GetStringAsync(url));
}

// Parallel (faster)
var tasks = urls.Select(url => client.GetStringAsync(url));
var results = await Task.WhenAll(tasks);
```

## Best Practices

- Always `await` async methods
- Don't `async void` except for event handlers
- Use `ConfigureAwait(false)` in library code
- Implement cancellation when appropriate
- Use `Task.WhenAll` for parallel operations
- Handle `OperationCanceledException`