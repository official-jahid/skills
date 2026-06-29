---
name: api-creation
description: API creation patterns for REST, GraphQL, and RPC endpoints - authentication, CRUD operations, and deployment.
license: MIT
metadata:
  author: typescript-skills
  version: "1.0.0"
---

# API Creation (TypeScript)

REST API patterns using Node.js, Express, and modern TypeScript.

## Express API Setup

```typescript
import express from 'express';
import { json, urlencoded } from 'express';

const app = express();
app.use(json());
app.use(urlencoded({ extended: true }));

// Routes
app.get('/api/users', async (req, res) => {
  const users = await db.user.findMany();
  res.json(users);
});

app.post('/api/users', async (req, res) => {
  const user = await db.user.create({ data: req.body });
  res.status(201).json(user);
});
```

## Authentication Middleware

```typescript
import { Request, Response, NextFunction } from 'express';

const requireAuth = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Unauthorized' });
  
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    (req as any).user = payload;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
};
```

## Error Handling

```typescript
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Internal server error' });
});

// Async handler wrapper
const asyncHandler = (fn: Function) => (req: Request, res: Response, next: NextFunction) =>
  Promise.resolve(fn(req, res, next)).catch(next);
```

## Validation with Zod

```typescript
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
});

app.post('/api/users', (req, res) => {
  const result = userSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json(result.error);
  }
  // Process validated data
});
```