# Component Development Guidelines

## File Naming Convention
- Components: `PascalCase.tsx` (e.g., `ReservationList.tsx`)
- Utils: `camelCase.ts` (e.g., `formatters.ts`)
- Types: `index.ts` or descriptive name

## Component Template

### Basic Component
```tsx
import { useState } from 'react';
import type { YourType } from '@/types';

interface YourComponentProps {
  // Define props with TypeScript
  title: string;
  onAction?: () => void;
}

export function YourComponent({ title, onAction }: YourComponentProps) {
  const [state, setState] = useState<YourType | null>(null);

  return (
    <div className="space-y-4">
      <h2 className="text-2xl font-bold text-gray-900">{title}</h2>
      {/* Your JSX */}
    </div>
  );
}
```

### Page Component
```tsx
import { useState, useEffect } from 'react';
import { Button } from '@/components/ui/Button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/Card';
import { yourService } from '@/services';
import type { YourType } from '@/types';

export function YourPage() {
  const [data, setData] = useState<YourType[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    loadData();
  }, []);

  const loadData = async () => {
    setIsLoading(true);
    setError(null);
    try {
      const result = await yourService.getAll();
      setData(result);
    } catch (err) {
      setError('Failed to load data');
    } finally {
      setIsLoading(false);
    }
  };

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div className="text-red-600">{error}</div>;
  }

  return (
    <div className="space-y-6">
      <div className="flex items-center justify-between">
        <h1 className="text-3xl font-bold text-gray-900">Your Page</h1>
        <Button onClick={() => {}}>Action</Button>
      </div>

      {data.length === 0 ? (
        <Card>
          <CardContent className="py-12 text-center text-gray-500">
            No data available
          </CardContent>
        </Card>
      ) : (
        <div className="grid gap-6">
          {data.map((item) => (
            <Card key={item.id}>
              <CardContent>
                {/* Render item */}
              </CardContent>
            </Card>
          ))}
        </div>
      )}
    </div>
  );
}
```

### Form Component with Validation
```tsx
import { useState } from 'react';
import type { FormEvent } from 'react';
import { Button } from '@/components/ui/Button';
import { Input } from '@/components/ui/Input';

interface FormData {
  name: string;
  email: string;
}

interface YourFormProps {
  onSubmit: (data: FormData) => Promise<void>;
  initialData?: Partial<FormData>;
}

export function YourForm({ onSubmit, initialData }: YourFormProps) {
  const [formData, setFormData] = useState<FormData>({
    name: initialData?.name || '',
    email: initialData?.email || '',
  });
  const [errors, setErrors] = useState<Partial<Record<keyof FormData, string>>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const validate = (): boolean => {
    const newErrors: Partial<Record<keyof FormData, string>> = {};

    if (!formData.name.trim()) {
      newErrors.name = 'Name is required';
    }

    if (!formData.email.trim()) {
      newErrors.email = 'Email is required';
    } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.email)) {
      newErrors.email = 'Invalid email format';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();

    if (!validate()) return;

    setIsSubmitting(true);
    try {
      await onSubmit(formData);
    } catch (error) {
      // Handle error
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <Input
        label="Name"
        value={formData.name}
        onChange={(e) => setFormData({ ...formData, name: e.target.value })}
        error={errors.name}
        required
      />

      <Input
        label="Email"
        type="email"
        value={formData.email}
        onChange={(e) => setFormData({ ...formData, email: e.target.value })}
        error={errors.email}
        required
      />

      <div className="flex gap-3 justify-end">
        <Button type="button" variant="outline">
          Cancel
        </Button>
        <Button type="submit" isLoading={isSubmitting}>
          Submit
        </Button>
      </div>
    </form>
  );
}
```

## Service Template

### Basic Service
```typescript
import type { YourType } from '@/types';

// Mock data
const mockData: YourType[] = [];

export const yourService = {
  getAll: async (): Promise<YourType[]> => {
    await new Promise(resolve => setTimeout(resolve, 500));
    return mockData;
  },

  getById: async (id: string): Promise<YourType | null> => {
    await new Promise(resolve => setTimeout(resolve, 300));
    return mockData.find(item => item.id === id) || null;
  },

  create: async (data: Omit<YourType, 'id'>): Promise<YourType> => {
    await new Promise(resolve => setTimeout(resolve, 800));
    const newItem: YourType = {
      id: `ID${Date.now()}`,
      ...data,
    } as YourType;
    mockData.push(newItem);
    return newItem;
  },

  update: async (id: string, data: Partial<YourType>): Promise<YourType> => {
    await new Promise(resolve => setTimeout(resolve, 600));
    const index = mockData.findIndex(item => item.id === id);
    if (index === -1) throw new Error('Not found');
    mockData[index] = { ...mockData[index], ...data };
    return mockData[index];
  },

  delete: async (id: string): Promise<void> => {
    await new Promise(resolve => setTimeout(resolve, 400));
    const index = mockData.findIndex(item => item.id === id);
    if (index !== -1) mockData.splice(index, 1);
  },
};
```

## Zustand Store Template

```typescript
import { create } from 'zustand';

interface YourState {
  items: YourType[];
  selectedItem: YourType | null;
  isLoading: boolean;
  
  // Actions
  setItems: (items: YourType[]) => void;
  selectItem: (item: YourType | null) => void;
  addItem: (item: YourType) => void;
  updateItem: (id: string, data: Partial<YourType>) => void;
  removeItem: (id: string) => void;
}

export const useYourStore = create<YourState>((set) => ({
  items: [],
  selectedItem: null,
  isLoading: false,

  setItems: (items) => set({ items }),
  
  selectItem: (item) => set({ selectedItem: item }),
  
  addItem: (item) => set((state) => ({ 
    items: [...state.items, item] 
  })),
  
  updateItem: (id, data) => set((state) => ({
    items: state.items.map((item) =>
      item.id === id ? { ...item, ...data } : item
    ),
  })),
  
  removeItem: (id) => set((state) => ({
    items: state.items.filter((item) => item.id !== id),
  })),
}));
```

## Best Practices

### 1. Component Organization
```
YourFeature/
├── index.ts                    # Export all components
├── YourFeaturePage.tsx         # Main page component
├── YourFeatureList.tsx         # List component
├── YourFeatureItem.tsx         # Item component
├── YourFeatureForm.tsx         # Form component
└── YourFeatureFilters.tsx      # Filter component
```

### 2. Import Order
```typescript
// 1. React imports
import { useState, useEffect } from 'react';
import type { ReactNode } from 'react';

// 2. Third-party libraries
import { useNavigate } from 'react-router-dom';
import { Users } from 'lucide-react';

// 3. Internal UI components
import { Button } from '@/components/ui/Button';

// 4. Local components
import { YourLocalComponent } from './YourLocalComponent';

// 5. Stores
import { useAuthStore } from '@/stores/authStore';

// 6. Services
import { yourService } from '@/services';

// 7. Types
import type { YourType } from '@/types';

// 8. Utils
import { formatCurrency } from '@/utils';
```

### 3. TypeScript Tips
- Always define prop interfaces
- Use `type` for unions and primitives
- Use `interface` for object shapes
- Prefer explicit return types for functions
- Use `Partial<>`, `Omit<>`, `Pick<>` utility types

### 4. State Management
- Use local state for UI-only state
- Use Zustand for global/shared state
- Keep state close to where it's used
- Avoid prop drilling (use context or store)

### 5. Error Handling
```typescript
const [error, setError] = useState<string | null>(null);

try {
  await someAsyncOperation();
} catch (err) {
  setError(err instanceof Error ? err.message : 'An error occurred');
  console.error('Operation failed:', err);
}
```

### 6. Loading States
```typescript
const [isLoading, setIsLoading] = useState(true);

// Always include loading UI
if (isLoading) {
  return (
    <div className="flex items-center justify-center min-h-[400px]">
      <div className="text-center">
        <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-primary-600 mx-auto" />
        <p className="mt-4 text-gray-600">Loading...</p>
      </div>
    </div>
  );
}
```

### 7. Empty States
```typescript
if (data.length === 0) {
  return (
    <Card>
      <CardContent className="py-12 text-center text-gray-500">
        <Icon className="h-12 w-12 mx-auto mb-3 text-gray-400" />
        <p>No items found</p>
        <Button className="mt-4" onClick={() => {}}>
          Add New Item
        </Button>
      </CardContent>
    </Card>
  );
}
```

### 8. Responsive Design
```tsx
// Use Tailwind responsive classes
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  {/* Mobile: 1 col, Tablet: 2 cols, Desktop: 4 cols */}
</div>

<div className="hidden lg:block">
  {/* Only show on desktop */}
</div>

<div className="lg:hidden">
  {/* Only show on mobile/tablet */}
</div>
```

## Common Patterns

### List with Actions
```tsx
{items.map((item) => (
  <Card key={item.id}>
    <CardContent className="flex items-center justify-between">
      <div>
        <h3 className="font-medium">{item.name}</h3>
        <p className="text-sm text-gray-500">{item.description}</p>
      </div>
      <div className="flex gap-2">
        <Button size="sm" variant="ghost" onClick={() => handleEdit(item)}>
          Edit
        </Button>
        <Button size="sm" variant="danger" onClick={() => handleDelete(item.id)}>
          Delete
        </Button>
      </div>
    </CardContent>
  </Card>
))}
```

### Modal/Dialog Pattern
```tsx
const [isOpen, setIsOpen] = useState(false);

return (
  <>
    <Button onClick={() => setIsOpen(true)}>Open Modal</Button>
    
    {isOpen && (
      <div className="fixed inset-0 z-50 flex items-center justify-center">
        <div className="fixed inset-0 bg-black/50" onClick={() => setIsOpen(false)} />
        <Card className="relative z-10 max-w-md w-full mx-4">
          <CardHeader>
            <CardTitle>Modal Title</CardTitle>
          </CardHeader>
          <CardContent>
            {/* Content */}
          </CardContent>
        </Card>
      </div>
    )}
  </>
);
```

### Tabs Pattern
```tsx
const [activeTab, setActiveTab] = useState('tab1');

return (
  <div>
    <div className="border-b border-gray-200">
      <nav className="flex gap-4">
        <button
          onClick={() => setActiveTab('tab1')}
          className={cn(
            'py-2 px-1 border-b-2 font-medium text-sm',
            activeTab === 'tab1'
              ? 'border-primary-600 text-primary-600'
              : 'border-transparent text-gray-500 hover:text-gray-700'
          )}
        >
          Tab 1
        </button>
        <button
          onClick={() => setActiveTab('tab2')}
          className={cn(
            'py-2 px-1 border-b-2 font-medium text-sm',
            activeTab === 'tab2'
              ? 'border-primary-600 text-primary-600'
              : 'border-transparent text-gray-500 hover:text-gray-700'
          )}
        >
          Tab 2
        </button>
      </nav>
    </div>
    
    <div className="mt-4">
      {activeTab === 'tab1' && <div>Tab 1 Content</div>}
      {activeTab === 'tab2' && <div>Tab 2 Content</div>}
    </div>
  </div>
);
```

---

Follow these templates and patterns to maintain consistency across the codebase!
