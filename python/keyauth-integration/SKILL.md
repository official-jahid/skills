---
name: keyauth-integration
description: KeyAuth authentication integration for Python applications, particularly Flask-based projects. Covers license verification, login/register flows, and user data management.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# KeyAuth Integration for Python

Skill for integrating KeyAuth authentication into Python applications, particularly Flask-based projects.

## Setup

```python
from keyauth import *

keyauthapp = api(
    name = "YOUR_APP_NAME",
    ownerid = "YOUR_OWNER_ID",
    secret = "YOUR_SECRET",
    version = "1.0",
    hash_to_check = getchecksum()
)
```

## Configuration

### Required Credentials
- Application Name
- Owner ID (10 characters)
- Application Secret (64 characters)
- Version

### Get Hardware ID

```python
from keyauth import others
hwid = others.get_hwid()
```

## Authentication Methods

### Login
```python
result = keyauthapp.login(user="username", password="password")
if result:
    print("Login successful")
```

### Register
```python
result = keyauthapp.register(user="username", password="password", license="key")
```

### License Verification
```python
keyauthapp.license(key="license-key")
```

## User Data

```python
username = keyauthapp.user_data.username
ip = keyauthapp.user_data.ip
hwid = keyauthapp.user_data.hwid
expires = keyauthapp.user_data.expires
```

## Session Management

```python
# Check session
online_users = keyauthapp.fetchOnline()

# Logout
keyauthapp.logout()
```

## File and Webhook Operations

```python
# Download file
file_data = keyauthapp.file(fileid="file-id")

# Trigger webhook
keyauthapp.webhook(webid="web-id", param={}, body="", conttype="")
```

## Security Notes

- Never expose your secret key in client-side code
- Use environment variables for sensitive credentials
- Implement proper HWID validation
- Handle failed authentication gracefully