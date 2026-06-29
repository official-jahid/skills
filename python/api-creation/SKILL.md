---
name: api-creation
description: API creation patterns for Flask and FastAPI - REST endpoints, authentication, validation, and deployment.
license: MIT
metadata:
  author: python-skills
  version: "1.0.0"
---

# API Creation (Python)

REST API patterns using Flask and FastAPI.

## Flask API Setup

```python
from flask import Flask, jsonify, request
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/api/users', methods=['GET'])
def get_users():
    users = [{'id': 1, 'name': 'John'}]
    return jsonify(users)

@app.route('/api/users', methods=['POST'])
def create_user():
    data = request.get_json()
    # Validate and create user
    return jsonify({'created': True}), 201
```

## FastAPI Setup

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List

app = FastAPI()

class User(BaseModel):
    name: str
    email: str

@app.get("/users", response_model=List[User])
async def get_users():
    return [{"name": "John", "email": "john@example.com"}]

@app.post("/users", response_model=User)
async def create_user(user: User):
    # Create user logic
    return user
```

## Authentication

```python
from functools import wraps
import jwt

def token_required(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization')
        if not token:
            return jsonify({'error': 'Missing token'}), 401
        try:
            payload = jwt.decode(token.split()[1], app.config['SECRET'])
        except:
            return jsonify({'error': 'Invalid token'}), 401
        return f(*args, **kwargs)
    return decorated
```

## Validation with Pydantic

```python
from pydantic import BaseModel, EmailStr

class UserCreate(BaseModel):
    name: str
    email: EmailStr
    
    class Config:
        schema_extra = {
            "example": {"name": "John", "email": "john@example.com"}
        }
```

## Background Tasks

```python
from fastapi import BackgroundTasks

def send_email(email: str, message: str):
    # Send email logic
    pass

@app.post("/notify")
async def notify(email: str, background: BackgroundTasks):
    background.add_task(send_email, email, "Hello")
    return {"message": "Notification sent"}
```

## Deployment

```bash
# WSGI server
pip install waitress
waitress-serve --port=8000 app:app

# Uvicorn for FastAPI
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```