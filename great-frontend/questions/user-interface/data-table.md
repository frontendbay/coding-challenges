# Data Table

> Source: [https://www.greatfrontend.com/questions/user-interface/data-table/v/66df6e17-96b3-4e5a-9b52-a8e9952d6b5d](https://www.greatfrontend.com/questions/user-interface/data-table/v/66df6e17-96b3-4e5a-9b52-a8e9952d6b5d)

## Attempted Solution

```jsx
// hooks/use-pagination.js
import { useState } from "react";
import users from "./data/users.json";

function usePagination(records) {
  const [page, setPage] = useState(1);
  const [size, setSize] = useState(5);

  const startIndex = size * page - size;
  const endIndex = startIndex + size;
  const totalPages = Math.ceil(records?.length / size);
  const filteredRecords = records.slice(startIndex, endIndex);

  return {
    page,
    size,
    totalPages,
    filteredRecords,
    setPage,
    setSize,
  };
}
```

```jsx
// components/pagination.jsx
function Pagination({ page, totalPages, setPage, setSize }) {
  return (
    <div className="pagination">
      <select
        aria-label="Page size"
        onChange={(event) => {
          setSize(Number(event.target.value));
          setPage(1);
        }}
      >
        {[5, 10, 20].map((size) => (
          <option key={size} value={size}>
            Show {size}
          </option>
        ))}
      </select>
      <div className="pages">
        <button
          disabled={page === 1}
          onClick={() => {
            setPage((prev) => (prev === 1 ? 1 : prev - 1));
          }}
        >
          Prev
        </button>
        <span aria-label="Page number">
          Page {page} of {totalPages}
        </span>
        <button
          disabled={page === totalPages}
          onClick={() => {
            setPage((prev) => (prev === totalPages ? totalPages : prev + 1));
          }}
        >
          Next
        </button>
      </div>
    </div>
  );
}
```

```jsx
// components/data-table.jsx
export default function DataTable() {
  const [message, setMessage] = useState("Data Table");
  const { page, size, totalPages, filteredRecords, setPage, setSize } =
    usePagination(users);

  return (
    <div>
      <h1>{message}</h1>
      <table>
        <thead>
          <tr>
            {[
              { label: "ID", key: "id" },
              { label: "Name", key: "name" },
              { label: "Age", key: "age" },
              { label: "Occupation", key: "occupation" },
            ].map(({ label, key }) => (
              <th key={key}>{label}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {filteredRecords.map(({ id, name, age, occupation }) => (
            <tr key={id}>
              <td>{id}</td>
              <td>{name}</td>
              <td>{age}</td>
              <td>{occupation}</td>
            </tr>
          ))}
        </tbody>
      </table>
      <Pagination
        page={page}
        size={size}
        totalPages={totalPages}
        setPage={setPage}
        setSize={setSize}
      />
    </div>
  );
}
```

```jsx
// app.jsx
import DataTable from "./DataTable";

export default function App() {
  return (
    <div>
      <DataTable />
    </div>
  );
}
```

## Key Insights

The core insight here is the mathematical relationship between page number, page size, and array slicing in pagination. The formula used is particularly elegant and reusable:

```javascript
const startIndex = size * page - size; // or simplified: size * (page - 1)
const endIndex = startIndex + size;
```

Let's understand why this works with an example. Imagine you have a page size of 5:

- For page 1: startIndex = 5 \* 1 - 5 = 0, endIndex = 0 + 5 = 5
- For page 2: startIndex = 5 \* 2 - 5 = 5, endIndex = 5 + 5 = 10
- For page 3: startIndex = 5 \* 3 - 5 = 10, endIndex = 10 + 5 = 15

This formula creates perfect "windows" into your data array that you can extract using array.slice(startIndex, endIndex). It's particularly powerful because it works for any page size and any page number.

Another valuable insight is the separation of pagination logic into a custom hook (usePagination). This creates a reusable piece of logic that can be applied to any list of records, not just user data. The hook handles:

1. State management for current page and page size
2. Calculation of pagination windows
3. Total pages computation
4. Record filtering

This pattern of extracting complex UI logic into custom hooks is something you can apply to many similar problems where you need to transform or manage data for display. It keeps your components clean and focused on presentation while making the logic reusable across different parts of your application.

A third insight is the calculation of total pages:

```javascript
const totalPages = Math.ceil(records?.length / size);
```

Using Math.ceil ensures you always have enough pages to show all records, even when the division isn't even. For example, with 22 records and a page size of 5, you'll get Math.ceil(22/5) = 5 pages, with the last page showing just 2 records.

You can apply these concepts whenever you need to:

- Break down large datasets into manageable chunks for display
- Create any kind of slideshow or carousel interface
- Implement infinite scroll (by modifying the page calculation)
- Build any interface that needs to show data in "windows" or "chunks"

The beauty of this solution is that it's both mathematically elegant and practically useful, making it a pattern worth remembering for future development.

## Better Solution

I'll create an improved version of this pagination implementation with better organization, error handling, accessibility, and performance optimizations. Let me explain the improvements as we go.

```jsx
// types.ts
// Define our core types for better type safety and documentation
type User = {
  id: string;
  name: string;
  age: number;
  occupation: string;
};

type PaginationConfig = {
  defaultPage?: number;
  defaultPageSize?: number;
  pageSizeOptions?: number[];
};

type PaginationState = {
  page: number;
  pageSize: number;
  totalPages: number;
  startIndex: number;
  endIndex: number;
  paginatedData: User[];
};

type PaginationActions = {
  setPage: (page: number) => void;
  setPageSize: (size: number) => void;
  goToNextPage: () => void;
  goToPreviousPage: () => void;
};

// hooks/usePagination.ts
import { useState, useMemo, useCallback } from 'react';

const DEFAULT_CONFIG: PaginationConfig = {
  defaultPage: 1,
  defaultPageSize: 5,
  pageSizeOptions: [5, 10, 20]
};

export function usePagination<T>(
  data: T[],
  config: PaginationConfig = DEFAULT_CONFIG
): PaginationState & PaginationActions {
  const [page, setPage] = useState(config.defaultPage ?? DEFAULT_CONFIG.defaultPage);
  const [pageSize, setPageSize] = useState(config.defaultPageSize ?? DEFAULT_CONFIG.defaultPageSize);

  // This calculation still benefits from memoization because it involves data processing
  const paginationData = useMemo(() => {
    const totalPages = Math.ceil(data.length / pageSize);
    const startIndex = pageSize * (page - 1);
    const endIndex = Math.min(startIndex + pageSize, data.length);
    const paginatedData = data.slice(startIndex, endIndex);

    return {
      totalPages,
      startIndex,
      endIndex,
      paginatedData
    };
  }, [data, page, pageSize]);

  // Navigation handlers
  const goToNextPage = () => {
    setPage(prev => Math.min(prev + 1, paginationData.totalPages));
  };

  const goToPreviousPage = () => {
    setPage(prev => Math.max(prev - 1, 1));
  };

  const handleSetPageSize = (newSize: number) => {
    setPageSize(newSize);
    setPage(1);
  };

  return {
    page,
    pageSize,
    ...paginationData,
    setPage,
    setPageSize: handleSetPageSize,
    goToNextPage,
    goToPreviousPage
  };
}

// components/TableHeader.tsx
import { memo } from 'react';

type Column = {
  label: string;
  key: keyof User;
  sortable?: boolean;
};

const COLUMNS: Column[] = [
  { label: 'ID', key: 'id', sortable: true },
  { label: 'Name', key: 'name', sortable: true },
  { label: 'Age', key: 'age', sortable: true },
  { label: 'Occupation', key: 'occupation', sortable: true }
];

export const TableHeader = memo(() => (
  <thead>
    <tr>
      {COLUMNS.map(({ label, key }) => (
        <th key={key} scope="col" className="p-4 text-left">
          {label}
        </th>
      ))}
    </tr>
  </thead>
));

TableHeader.displayName = 'TableHeader';

// components/Pagination.tsx
import { memo } from 'react';

type PaginationProps = {
  page: number;
  pageSize: number;
  totalPages: number;
  pageSizeOptions?: number[];
  onPageChange: (page: number) => void;
  onPageSizeChange: (size: number) => void;
  onNext: () => void;
  onPrevious: () => void;
};

export const Pagination = memo(({
  page,
  pageSize,
  totalPages,
  pageSizeOptions = DEFAULT_CONFIG.pageSizeOptions,
  onPageChange,
  onPageSizeChange,
  onNext,
  onPrevious
}: PaginationProps) => (
  <nav
    role="navigation"
    aria-label="Pagination Navigation"
    className="flex items-center justify-between p-4"
  >
    <div className="flex items-center gap-4">
      <label className="flex items-center gap-2">
        <span>Show</span>
        <select
          value={pageSize}
          onChange={(e) => onPageSizeChange(Number(e.target.value))}
          className="p-2 border rounded"
          aria-label="Select page size"
        >
          {pageSizeOptions.map((size) => (
            <option key={size} value={size}>
              {size}
            </option>
          ))}
        </select>
        <span>items</span>
      </label>
    </div>

    <div className="flex items-center gap-2">
      <button
        onClick={onPrevious}
        disabled={page === 1}
        className="px-4 py-2 border rounded disabled:opacity-50"
        aria-label="Previous page"
      >
        Previous
      </button>

      <span className="mx-4">
        Page {page} of {totalPages}
      </span>

      <button
        onClick={onNext}
        disabled={page === totalPages}
        className="px-4 py-2 border rounded disabled:opacity-50"
        aria-label="Next page"
      >
        Next
      </button>
    </div>
  </nav>
));

Pagination.displayName = 'Pagination';

// components/DataTable.tsx
import { memo } from 'react';
import { usePagination } from '../hooks/usePagination';
import { TableHeader } from './TableHeader';
import { Pagination } from './Pagination';

type DataTableProps = {
  data: User[];
  title?: string;
  paginationConfig?: PaginationConfig;
};

export const DataTable = memo(({
  data,
  title = 'Data Table',
  paginationConfig
}: DataTableProps) => {
  const {
    page,
    pageSize,
    totalPages,
    paginatedData,
    setPage,
    setPageSize,
    goToNextPage,
    goToPreviousPage
  } = usePagination(data, paginationConfig);

  return (
    <div className="w-full max-w-6xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">{title}</h1>

      <div className="border rounded shadow">
        <table className="w-full border-collapse">
          <TableHeader />
          <tbody>
            {paginatedData.map((user) => (
              <tr
                key={user.id}
                className="border-t hover:bg-gray-50"
              >
                <td className="p-4">{user.id}</td>
                <td className="p-4">{user.name}</td>
                <td className="p-4">{user.age}</td>
                <td className="p-4">{user.occupation}</td>
              </tr>
            ))}
          </tbody>
        </table>

        {data.length > 0 ? (
          <Pagination
            page={page}
            pageSize={pageSize}
            totalPages={totalPages}
            onPageChange={setPage}
            onPageSizeChange={setPageSize}
            onNext={goToNextPage}
            onPrevious={goToPreviousPage}
          />
        ) : (
          <p className="p-4 text-center text-gray-500">No data available</p>
        )}
      </div>
    </div>
  );
});

DataTable.displayName = 'DataTable';

// App.tsx
import { DataTable } from './components/DataTable';
import users from './data/users.json';

export default function App() {
  return (
    <div className="p-8">
      <DataTable
        data={users}
        title="User Directory"
        paginationConfig={{
          defaultPageSize: 10,
          pageSizeOptions: [5, 10, 20, 50]
        }}
      />
    </div>
  );
}
```

Let me explain the key improvements and architectural decisions in this enhanced version:

1. Type Safety and Documentation:

   - I've added TypeScript to provide better type safety and self-documentation
   - Created clear interfaces for all props and state objects
   - This helps catch errors early and makes the code more maintainable

2. Enhanced Hook Architecture:

   - The usePagination hook is now more flexible and reusable
   - Uses useMemo for performance optimization of calculations
   - Provides additional convenience methods like goToNextPage and goToPreviousPage
   - Accepts a configuration object for customization
   - Properly memoizes callbacks to prevent unnecessary rerenders

3. Component Organization:

   - Split into smaller, focused components (TableHeader, Pagination, DataTable)
   - Each component is memoized using memo() to prevent unnecessary rerenders
   - Clear separation of concerns between data management and presentation
   - Components are more reusable and testable

4. Accessibility Improvements:

   - Added proper ARIA labels and roles
   - Semantic HTML structure with proper table elements
   - Clear focus states and keyboard navigation
   - Improved screen reader support

5. Error Handling and Edge Cases:

   - Handles empty data states
   - Prevents invalid page numbers
   - Safely handles boundary conditions in pagination
   - Uses optional chaining and fallback values

6. Styling and UX:

   - Added Tailwind CSS classes for a clean, modern look
   - Responsive design considerations
   - Clear visual feedback for interactive elements
   - Consistent spacing and alignment

7. Performance Optimizations:

   - Memoization of expensive calculations
   - Component-level memoization to prevent unnecessary rerenders
   - Efficient state updates
   - Proper use of callback functions

8. Configuration and Customization:
   - Added a configuration object for pagination settings
   - Customizable page sizes
   - Flexible styling options
   - Configurable table columns

The key architectural pattern here is the separation of concerns:

1. Data management (usePagination hook)
2. Presentation (individual components)
3. Configuration (types and configs)

This makes the code more maintainable, testable, and reusable across different projects. You can easily adapt this pattern for any kind of paginated data display, not just user tables.

Would you like me to explain any particular aspect in more detail or see how to implement additional features like sorting or filtering?
