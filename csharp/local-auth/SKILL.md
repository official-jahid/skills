---
name: local-auth
description: Local authentication patterns - user credentials, session management, and secure password storage.
license: MIT
metadata:
  author: csharp-skills
  version: "1.0.0"
---

# Local Authentication (C#)

Local authentication patterns for desktop and service applications.

## User Model

```csharp
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string PasswordHash { get; set; }
    public string Salt { get; set; }
    public DateTime CreatedAt { get; set; }
}
```

## Password Hashing

```csharp
using System.Security.Cryptography;

public static class PasswordHasher
{
    public static (string hash, string salt) HashPassword(string password)
    {
        byte[] salt = RandomNumberGenerator.GetBytes(16);
        byte[] hash = Rfc2898DeriveBytes.Pbkdf2(
            password, salt, 100000, HashAlgorithmName.SHA256, 32);
        
        return (Convert.ToBase64String(hash), Convert.ToBase64String(salt));
    }
    
    public static bool VerifyPassword(string password, string hash, string salt)
    {
        byte[] saltBytes = Convert.FromBase64String(salt);
        byte[] hashBytes = Convert.FromBase64String(hash);
        
        byte[] computedHash = Rfc2898DeriveBytes.Pbkdf2(
            password, saltBytes, 100000, HashAlgorithmName.SHA256, 32);
        
        return CryptographicOperations.FixedTimeEquals(hashBytes, computedHash);
    }
}
```

## User Registration

```csharp
public class AuthService
{
    public async Task<bool> Register(string username, string password)
    {
        if (await UserExists(username))
            return false;
        
        var (hash, salt) = PasswordHasher.HashPassword(password);
        
        var user = new User
        {
            Username = username,
            PasswordHash = hash,
            Salt = salt,
            CreatedAt = DateTime.UtcNow
        };
        
        return await SaveUser(user);
    }
}
```

## Login

```csharp
public async Task<User> Login(string username, string password)
{
    var user = await GetUserByUsername(username);
    if (user == null) return null;
    
    if (!PasswordHasher.VerifyPassword(password, user.PasswordHash, user.Salt))
        return null;
    
    return user;
}
```

## Session Management

```csharp
public class SessionManager
{
    private readonly ConcurrentDictionary<int, string> _sessions = new();
    
    public string CreateSession(int userId)
    {
        string token = Convert.ToBase64String(RandomNumberGenerator.GetBytes(32));
        _sessions[userId] = token;
        return token;
    }
    
    public bool ValidateSession(int userId, string token)
    {
        return _sessions.TryGetValue(userId, out var storedToken) && 
               storedToken == token;
    }
}
```

## Local Storage

```csharp
// JSON file storage
public class DataStore
{
    private readonly string _filePath;
    
    public async Task<List<T>> Load<T>()
    {
        if (!File.Exists(_filePath))
            return new List<T>();
        
        var json = await File.ReadAllTextAsync(_filePath);
        return JsonSerializer.Deserialize<List<T>>(json);
    }
    
    public async Task Save<T>(List<T> data)
    {
        var json = JsonSerializer.Serialize(data, new JsonSerializerOptions
        {
            WriteIndented = true
        });
        await File.WriteAllTextAsync(_filePath, json);
    }
}