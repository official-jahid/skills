---
name: website-creation
description: Web application patterns - HTML, CSS, JavaScript, frameworks, and deployment strategies.
license: MIT
metadata:
  author: typescript-skills
  version: "1.0.0"
---

# Website Creation (TypeScript)

Modern web development with TypeScript and frameworks.

## Next.js Setup

```typescript
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <body className="min-h-screen bg-background">
        <Navigation />
        <main>{children}</main>
      </body>
    </html>
  );
}

// app/page.tsx
import { Metadata } from 'next';
export const metadata: Metadata = {
  title: 'My App',
  description: 'Built with Next.js',
};
```

## Static Site Generation

```typescript
// getStaticProps / getStaticPaths (Pages Router)
export async function getStaticProps({ params }) {
  const data = await fetchData(params.id);
  return { props: { data }, revalidate: 60 };
}

// Dynamic routes
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map(post => ({ id: post.id }));
}
```

## API Routes

```typescript
// app/api/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const data = await getData();
  return NextResponse.json(data);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  const result = await createItem(body);
  return NextResponse.json(result, { status: 201 });
}
```

## Client-Side Data Fetching

```typescript
// SWR hook
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(r => r.json());

function Profile({ id }: { id: string }) {
  const { data, error, isLoading } = useSWR(`/api/user/${id}`, fetcher);
  
  if (error) return <div>Error loading</div>;
  if (isLoading) return <div>Loading...</div>;
  return <div>{data.name}</div>;
}
```

## Deployment

```bash
# Vercel
npm run build && vercel --prod

# Docker
FROM node:20-alpine
WORKDIR /app
COPY . .
RUN npm ci && npm run build
EXPOSE 3000
CMD ["npm", "start"]
```