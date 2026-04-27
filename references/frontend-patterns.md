# Frontend Advanced Patterns

## Server State with TanStack Query

The most common mistake: storing server data in useState. Use TanStack Query instead.

```ts
// hooks/useUsers.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { getUsers, createUser } from '../services/users.service';

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}
```

## Typed API Service Layer

Never call fetch directly in components. Create a typed service layer.

```ts
// services/users.service.ts
import { apiClient } from '../lib/apiClient';
import type { User, CreateUserInput } from '../types/user';

export async function getUsers(): Promise<User[]> {
  const res = await apiClient.get<{ success: true; data: User[] }>('/users');
  return res.data.data;
}

export async function createUser(input: CreateUserInput): Promise<User> {
  const res = await apiClient.post<{ success: true; data: User }>('/users', input);
  return res.data.data;
}

// lib/apiClient.ts
import axios from 'axios';

export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  withCredentials: true,
});

apiClient.interceptors.response.use(
  (res) => res,
  (err) => {
    if (err.response?.status === 401) {
      // redirect to login
    }
    return Promise.reject(err);
  }
);
```

## Form Pattern with React Hook Form + Zod

```tsx
// components/features/CreateUserForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(2, 'Name too short'),
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Min 8 characters'),
});

type FormData = z.infer<typeof schema>;

export function CreateUserForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting } } = useForm<FormData>({
    resolver: zodResolver(schema),
  });

  const createUser = useCreateUser();

  return (
    <form onSubmit={handleSubmit((data) => createUser.mutate(data))}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      <button disabled={isSubmitting}>Submit</button>
    </form>
  );
}
```

## Zustand for Client State

Only for UI state, preferences, and auth session. Not for server data.

```ts
// stores/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  token: string | null;
  setAuth: (user: User, token: string) => void;
  clearAuth: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      setAuth: (user, token) => set({ user, token }),
      clearAuth: () => set({ user: null, token: null }),
    }),
    { name: 'auth-storage' }
  )
);
```

## Loading / Error / Empty States

Every data-fetching component must handle all three. This is non-negotiable.

```tsx
export function UserList() {
  const { data: users, isLoading, error } = useUsers();

  if (isLoading) return <LoadingSkeleton />;
  if (error) return <ErrorMessage message="Failed to load users" onRetry={refetch} />;
  if (!users?.length) return <EmptyState message="No users yet" />;

  return <ul>{users.map(u => <UserCard key={u.id} user={u} />)}</ul>;
}
```

## Component Boundaries

- `components/ui/` — dumb, reusable, no business logic (Button, Input, Modal, Badge)
- `components/features/` — smart, knows about domain (UserCard, OrderSummary)
- Keep components small. If a component is over ~150 lines, split it.
- Co-locate: test file next to component, types in the same file or a sibling `.types.ts`
