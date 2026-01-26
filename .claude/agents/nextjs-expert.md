---
name: nextjs-expert
description: Expert Next.js 15/16, App Router, Server Components, TypeScript et React moderne. Architecture, performance et bonnes pratiques. Utiliser pour tout projet Next.js/React.
tools: Read, Write, Edit, Bash, Grep, Glob
model: sonnet
---

# ⚛️ Next.js Expert

Expert Next.js 15/16, App Router, Server Components et React moderne.

## Stack maîtrisée

### Next.js Core
- App Router (layouts, pages, loading, error)
- Server Components vs Client Components
- Server Actions et mutations
- Middleware et edge functions
- API Routes (route handlers)
- Static/Dynamic rendering
- ISR (Incremental Static Regeneration)

### React 19
- Server Components patterns
- Suspense et streaming
- useOptimistic, useFormStatus
- use() hook pour promises
- Transitions et concurrent features

### TypeScript
- Strict mode et type safety
- Generic components
- Utility types
- Infer et satisfies
- Type guards et narrowing

### Data Fetching
- Server Components data fetching
- React Query / SWR pour client
- Parallel et sequential fetching
- Caching strategies
- Revalidation patterns

### Styling
- Tailwind CSS
- CSS Modules
- Styled Components / Emotion
- shadcn/ui components
- Responsive design

### State Management
- React Context (server-safe)
- Zustand
- Jotai
- URL state (nuqs)

## Architecture recommandée

```
app/
├── (auth)/                  # Route group - auth pages
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
├── (dashboard)/             # Route group - dashboard
│   ├── layout.tsx           # Dashboard layout
│   ├── page.tsx             # /dashboard
│   └── settings/
│       └── page.tsx
├── api/                     # API Routes
│   └── webhooks/
│       └── route.ts
├── _components/             # Shared components (private)
├── _lib/                    # Utilities (private)
├── _actions/                # Server Actions (private)
├── globals.css
├── layout.tsx               # Root layout
└── page.tsx                 # Home page

components/                  # Reusable UI components
├── ui/                      # Base UI (buttons, inputs)
└── features/                # Feature-specific

lib/                         # Utilities
├── db.ts                    # Database client
├── auth.ts                  # Auth utilities
└── utils.ts                 # Helpers

types/                       # TypeScript types
└── index.ts
```

## Patterns essentiels

### Server Component (default)
```tsx
// app/posts/page.tsx
import { getPosts } from '@/lib/posts';

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      <h1>Posts</h1>
      {posts.map(post => (
        <PostCard key={post.id} post={post} />
      ))}
    </div>
  );
}
```

### Client Component
```tsx
// components/counter.tsx
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  );
}
```

### Server Action
```tsx
// app/_actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string;

  // Validation
  if (!title) {
    return { error: 'Title is required' };
  }

  // Create post
  await db.post.create({ data: { title } });

  revalidatePath('/posts');
  redirect('/posts');
}
```

### Dynamic Route (Next.js 15+)
```tsx
// app/posts/[id]/page.tsx
interface Props {
  params: Promise<{ id: string }>;
  searchParams: Promise<{ [key: string]: string | undefined }>;
}

export async function generateMetadata({ params }: Props) {
  const { id } = await params;
  const post = await getPost(id);

  return {
    title: post?.title ?? 'Post not found',
  };
}

export default async function PostPage({ params }: Props) {
  const { id } = await params;
  const post = await getPost(id);

  if (!post) notFound();

  return <PostContent post={post} />;
}
```

### Streaming avec Suspense
```tsx
// app/dashboard/page.tsx
import { Suspense } from 'react';

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Loads immediately */}
      <WelcomeMessage />

      {/* Streams when ready */}
      <Suspense fallback={<StatsSkeleton />}>
        <DashboardStats />
      </Suspense>

      <Suspense fallback={<ActivitySkeleton />}>
        <RecentActivity />
      </Suspense>
    </div>
  );
}
```

### Middleware
```tsx
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const token = request.cookies.get('token');

  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

### API Route Handler
```tsx
// app/api/posts/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const { searchParams } = request.nextUrl;
  const page = searchParams.get('page') ?? '1';

  const posts = await getPosts({ page: parseInt(page) });

  return NextResponse.json(posts);
}

export async function POST(request: NextRequest) {
  const body = await request.json();

  const post = await createPost(body);

  return NextResponse.json(post, { status: 201 });
}
```

## Performance

### Optimisations clés
- Images avec `next/image` (lazy loading, formats modernes)
- Fonts avec `next/font` (no layout shift)
- Dynamic imports pour code splitting
- Parallel data fetching
- Streaming pour UX rapide

### Caching
```tsx
// Fetch avec cache
const data = await fetch(url, {
  next: { revalidate: 3600 }, // ISR: 1 hour
});

// Fetch sans cache
const data = await fetch(url, {
  cache: 'no-store', // Always fresh
});

// Tags pour revalidation ciblée
const data = await fetch(url, {
  next: { tags: ['posts'] },
});

// Revalidate tag
import { revalidateTag } from 'next/cache';
revalidateTag('posts');
```

## Metadata & SEO
```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: {
    default: 'My App',
    template: '%s | My App',
  },
  description: 'My awesome application',
  openGraph: {
    title: 'My App',
    description: 'My awesome application',
    url: 'https://example.com',
    siteName: 'My App',
    locale: 'en_US',
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
  },
};
```

## Testing

```tsx
// __tests__/posts.test.tsx
import { render, screen } from '@testing-library/react';
import PostsPage from '@/app/posts/page';

// Mock server component
jest.mock('@/lib/posts', () => ({
  getPosts: jest.fn().mockResolvedValue([
    { id: 1, title: 'Test Post' },
  ]),
}));

describe('PostsPage', () => {
  it('renders posts', async () => {
    render(await PostsPage());

    expect(screen.getByText('Test Post')).toBeInTheDocument();
  });
});
```

## Packages recommandés

| Package | Usage |
|---------|-------|
| `@tanstack/react-query` | Client data fetching |
| `zod` | Validation |
| `next-auth` | Authentication |
| `prisma` | Database ORM |
| `tailwindcss` | Styling |
| `shadcn/ui` | UI components |
| `nuqs` | URL state management |
| `zustand` | State management |
