---
name: cloud-auth
description: Cloud authentication patterns - OAuth2, JWT tokens, refresh tokens, and multi-provider authentication.
license: MIT
metadata:
  author: csharp-skills
  version: "1.0.0"
---

# Cloud Authentication (C#)

Cloud authentication with OAuth2 and JWT for web services.

## JWT Token Generation

```csharp
using System.IdentityModel.Tokens.Jwt;
using Microsoft.IdentityModel.Tokens;

public class JwtService
{
    private readonly string _secret;
    
    public string GenerateToken(User user, TimeSpan expires)
    {
        var tokenHandler = new JwtSecurityTokenHandler();
        var key = Encoding.ASCII.GetBytes(_secret);
        
        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(new[]
            {
                new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
                new Claim(ClaimTypes.Name, user.Username)
            }),
            Expires = DateTime.UtcNow.Add(expires),
            SigningCredentials = new SigningCredentials(
                new SymmetricSecurityKey(key), 
                SecurityAlgorithms.HmacSha256Signature)
        };
        
        var token = tokenHandler.CreateToken(tokenDescriptor);
        return tokenHandler.WriteToken(token);
    }
}
```

## OAuth2 with Google

```csharp
services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = "Cookies";
    options.DefaultSignInScheme = "Cookies";
})
.AddCookie("Cookies")
.AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Google:ClientSecret"];
    googleOptions.CallbackPath = "/signin-google";
});
```

## Refresh Tokens

```csharp
public class TokenService
{
    public async Task<string> RefreshToken(string refreshToken)
    {
        var storedToken = await _db.RefreshTokens
            .FirstOrDefaultAsync(rt => rt.Token == refreshToken);
        
        if (storedToken == null || storedToken.Expires < DateTime.UtcNow)
            throw new InvalidOperationException("Invalid refresh token");
        
        var user = await _db.Users.FindAsync(storedToken.UserId);
        return _jwtService.GenerateToken(user, TimeSpan.FromHours(1));
    }
    
    public async Task<string> GenerateRefreshToken(int userId)
    {
        var token = Convert.ToBase64String(RandomNumberGenerator.GetBytes(64));
        
        var refreshToken = new RefreshToken
        {
            Token = token,
            UserId = userId,
            Expires = DateTime.UtcNow.AddDays(7)
        };
        
        _db.RefreshTokens.Add(refreshToken);
        await _db.SaveChangesAsync();
        return token;
    }
}
```

## Two-Factor Authentication

```csharp
public class TwoFactorService
{
    public async Task<string> GenerateQrCodeUri(string email, string secret)
    {
        return $"otpauth://totp/MyApp:{email}?secret={secret}&issuer=MyApp";
    }
    
    public bool VerifyCode(string code, string secret)
    {
        var totp = new Totp(Base32Encoding.ToBytes(secret));
        return totp.VerifyTotp(code, out _);
    }
    
    public string GenerateSecret()
    {
        return Base32Encoding.ToString(RandomNumberGenerator.GetBytes(20));
    }
}
```

## Email Verification

```csharp
public class EmailVerificationService
{
    public async Task SendVerificationEmail(User user)
    {
        var token = Convert.ToBase64String(RandomNumberGenerator.GetBytes(32));
        var expiry = DateTime.UtcNow.AddHours(24);
        
        var verification = new EmailVerification
        {
            UserId = user.Id,
            Token = token,
            Expires = expiry
        };
        
        _db.EmailVerifications.Add(verification);
        await _db.SaveChangesAsync();
        
        var link = $"{_config["AppUrl"]}/verify?token={token}";
        await _emailService.Send(user.Email, "Verify Email", $"<a href='{link}'>Verify</a>");
    }
}
```

## Password Reset

```csharp
public async Task RequestPasswordReset(string email)
{
    var user = await _db.Users.FirstOrDefaultAsync(u => u.Email == email);
    if (user == null) return;
    
    var token = Convert.ToBase64String(RandomNumberGenerator.GetBytes(32));
    var reset = new PasswordReset
    {
        UserId = user.Id,
        Token = token,
        Expires = DateTime.UtcNow.AddHours(1)
    };
    
    _db.PasswordResets.Add(reset);
    await _db.SaveChangesAsync();
    
    var link = $"{_config["AppUrl"]}/reset?token={token}";
    await _emailService.Send(user.Email, "Reset Password", $"<a href='{link}'>Reset</a>");
}
```