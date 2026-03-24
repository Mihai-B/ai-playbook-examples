---
description: "Use when writing React components, hooks, JSX templates, or working with frontend UI code"
applyTo: "src/components/**/*.tsx,src/hooks/**/*.ts"
---
# React Component Guidelines

## Component Structure

Follow this order within components:

```tsx
export function UserProfile({ userId }: UserProfileProps) {
  // 1. Hooks
  const { data, isLoading } = useUser(userId);
  const [isEditing, setIsEditing] = useState(false);

  // 2. Derived state
  const fullName = `${data?.firstName} ${data?.lastName}`;

  // 3. Effects
  useEffect(() => { ... }, [userId]);

  // 4. Handlers
  const handleSave = () => { ... };

  // 5. Early returns
  if (isLoading) return <Skeleton />;

  // 6. Render
  return <div>...</div>;
}
```

## Rules

- Prefer `interface` over `type` for component props
- Extract hooks when component logic exceeds ~30 lines
- Use `React.memo()` only when profiling shows a performance need
- Colocate styles: use CSS modules (`Component.module.css`)
- Never use `any` — prefer `unknown` with type guards
