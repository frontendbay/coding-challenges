# Accordion

> Source: [https://www.greatfrontend.com/questions/user-interface/accordion/react](https://www.greatfrontend.com/questions/user-interface/accordion/react)

## Attempted Solution

```jsx
// src/App.jsx
import Accordion from "./components/accordian";

export default function App() {
  return (
    <Accordion
      sections={[
        {
          value: "html",
          title: "HTML",
          content:
            "The HyperText Markup Language or HTML is the standard markup language for documents designed to be displayed in a web browser.",
        },
        {
          value: "css",
          title: "CSS",
          content:
            "Cascading Style Sheets is a style sheet language used for describing the presentation of a document written in a markup language such as HTML or XML.",
        },
        {
          value: "javascript",
          title: "JavaScript",
          content:
            "JavaScript, often abbreviated as JS, is a programming language that is one of the core technologies of the World Wide Web, alongside HTML and CSS.",
        },
      ]}
    />
  );
}
```

```jsx
// src/components/accordian.jsx
import { useState } from "react";

export default function Accordion({ sections }) {
  const [activeSection, setActiveSection] = useState("");

  function toggleSection(name) {
    setActiveSection((prev) => (prev === name ? "" : name));
  }

  return (
    <div className="accordian">
      {sections.map((section) => (
        <div className="accordian-item">
          <div
            className="accordian-header"
            onClick={() => toggleSection(section.value)}
          >
            {section.title}
            <span
              aria-hidden={true}
              className={`accordion-icon ${activeSection === section.value ? "" : "accordion-icon--rotated"}`}
            />
          </div>
          <div
            className={`accordian-body ${activeSection === section.value ? "show" : "hide"}`}
          >
            {section.content}
          </div>
        </div>
      ))}
    </div>
  );
}
```

## Key Insights

Let me break down the key architectural insight from this accordion component example. The core concept here revolves around state management for UI toggling, which is a pattern you'll frequently encounter in interactive components.

The fundamental insight is how we're using a single piece of state (`activeSection`) to control multiple toggleable sections. Rather than using multiple boolean flags, we store just the currently active section's identifier. This approach has several advantages:

1. Memory Efficiency: Instead of maintaining a boolean for each section (which would require an object or array of booleans), we only need to track a single string value.

2. Mutual Exclusivity: By design, this pattern ensures that only one section can be open at a time. When you click a section:
   - If it's already active (`prev === name`), it closes (sets to `""`)
   - If it's not active, it becomes the new active section (sets to `name`)

Here's a comparison to illustrate why this is clever:

```javascript
// Less efficient approach with multiple flags
const [sections, setSections] = useState({
  html: false,
  css: false,
  javascript: false,
});

// More efficient single-value approach
const [activeSection, setActiveSection] = useState("");
```

You can reuse this pattern in many similar UI components where you need mutual exclusivity, such as:

- Tab panels
- Navigation menus with expandable submenus
- Modal dialogs where only one can be open
- Select/dropdown components
- Sidebar navigation with collapsible sections

The key is to identify situations where you need "one active item out of many" rather than "multiple independent toggles." In such cases, storing a single identifier rather than multiple boolean flags will often lead to cleaner, more maintainable code.

This pattern also scales well - you can add or remove sections without modifying the state management logic, making it highly reusable across different contexts.

## Better Solution

```jsx
import React, { useState, useCallback, useMemo } from 'react';
import { ChevronDown } from 'lucide-react';

// Define types for our props and section structure
type AccordionSection = {
  value: string;
  title: string;
  content: React.ReactNode;
};

interface AccordionProps {
  sections?: AccordionSection[];
  defaultOpen?: string;
  allowMultiple?: boolean;
}

const HtmlContent = () => (
  <div className="space-y-4">
    <p>
      HTML (HyperText Markup Language) forms the foundation of web content. It's a semantic language that defines the structure and meaning of web content through elements like:
    </p>
    <div className="pl-4 border-l-4 border-blue-200">
      <p><strong>Document Structure:</strong> html, head, body</p>
      <p><strong>Content Sections:</strong> header, nav, main, article, section, aside, footer</p>
      <p><strong>Text Content:</strong> h1-h6, p, ul/ol, blockquote</p>
    </div>
    <p className="text-sm bg-gray-50 p-3 rounded">
      Pro Tip: Focus on semantic HTML to improve accessibility and SEO. For example, use button for interactive elements and article for self-contained content.
    </p>
  </div>
);

const CssContent = () => (
  <div className="space-y-4">
    <p>
      CSS (Cascading Style Sheets) controls the visual presentation of HTML elements. Modern CSS encompasses several key concepts:
    </p>
    <div className="pl-4 border-l-4 border-green-200">
      <p><strong>Box Model:</strong> margin, border, padding, content</p>
      <p><strong>Layout Systems:</strong> Flexbox for 1D layouts, Grid for 2D layouts</p>
      <p><strong>Responsive Design:</strong> Media queries, relative units (rem, em, vh, vw)</p>
      <p><strong>Modern Features:</strong> Custom properties, logical properties, container queries</p>
    </div>
    <p className="text-sm bg-gray-50 p-3 rounded">
      Pro Tip: Use CSS custom properties (variables) for consistent theming and easier maintenance. Example: --primary-color: #3b82f6;
    </p>
  </div>
);

const JavaScriptContent = () => (
  <div className="space-y-4">
    <p>
      JavaScript brings interactivity and dynamic behavior to web applications. Key modern JavaScript concepts include:
    </p>
    <div className="pl-4 border-l-4 border-yellow-200">
      <p><strong>Modern Syntax:</strong> Arrow functions, destructuring, spread/rest operators</p>
      <p><strong>Asynchronous Programming:</strong> Promises, async/await, fetch API</p>
      <p><strong>Core Concepts:</strong> Closures, prototypes, modules</p>
      <p><strong>DOM Manipulation:</strong> Event handling, element creation/modification</p>
    </div>
    <div className="bg-gray-50 p-3 rounded space-y-2">
      <p className="text-sm font-medium">Example of modern JavaScript:</p>
      <code className="text-sm block bg-gray-100 p-2 rounded">
        const getData = async () => {"{"}
          try {"{"}
            const response = await fetch('/api/data');
            const {"{"} items {"}"} = await response.json();
            return items;
          {"}"} catch (error) {"{"}
            console.error(error);
          {"}"}
        {"}"};
      </code>
    </div>
  </div>
);

const ReactContent = () => (
  <div className="space-y-4">
    <p>
      React is a JavaScript library for building user interfaces through components. Essential React concepts include:
    </p>
    <div className="pl-4 border-l-4 border-purple-200">
      <p><strong>Components:</strong> Function components, props, children</p>
      <p><strong>Hooks:</strong> useState, useEffect, useContext, custom hooks</p>
      <p><strong>State Management:</strong> Component state, context, external stores</p>
      <p><strong>Performance:</strong> Memoization, lazy loading, code splitting</p>
    </div>
    <div className="bg-gray-50 p-3 rounded space-y-2">
      <p className="text-sm font-medium">Modern React Hook Example:</p>
      <code className="text-sm block bg-gray-100 p-2 rounded">
        const useCounter = (initialValue = 0) => {"{"}
          const [count, setCount] = useState(initialValue);
          const increment = () => setCount(prev => prev + 1);
          const decrement = () => setCount(prev => prev - 1);
          return {"{"} count, increment, decrement {"}"};
        {"}"};
      </code>
    </div>
  </div>
);

// Define comprehensive content about web development fundamentals
const defaultSections = [
  {
    value: "html",
    title: "HTML - Structure of the Web",
    content: <HtmlContent />
  },
  {
    value: "css",
    title: "CSS - Styling and Layout",
    content: <CssContent />
  },
  {
    value: "javascript",
    title: "JavaScript - Dynamic Interactions",
    content: <JavaScriptContent />
  },
  {
    value: "react",
    title: "React - Component-Based UI",
    content: <ReactContent />
  }
];

export default function Accordion({
  sections = defaultSections,
  defaultOpen = '',
  allowMultiple = false
}: AccordionProps) {
  const [activeSections, setActiveSections] = useState<Set<string>>(() =>
    new Set(defaultOpen ? [defaultOpen] : [])
  );

  const toggleSection = useCallback((value: string) => {
    setActiveSections(prev => {
      const newSet = new Set(prev);
      if (newSet.has(value)) {
        newSet.delete(value);
      } else {
        if (!allowMultiple) {
          newSet.clear();
        }
        newSet.add(value);
      }
      return newSet;
    });
  }, [allowMultiple]);

  const handleKeyDown = useCallback((
    e: React.KeyboardEvent,
    value: string
  ) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      toggleSection(value);
    }
  }, [toggleSection]);

  const accordionSections = useMemo(() => sections.map((section) => {
    const isActive = activeSections.has(section.value);
    const headerId = `accordion-header-${section.value}`;
    const panelId = `accordion-panel-${section.value}`;

    return (
      <div
        key={section.value}
        className="border-b border-gray-200 last:border-b-0"
      >
        <h3>
          <button
            id={headerId}
            aria-controls={panelId}
            aria-expanded={isActive}
            className="flex w-full items-center justify-between py-4 px-6 text-left text-lg font-medium text-gray-900 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-all duration-200"
            onClick={() => toggleSection(section.value)}
            onKeyDown={(e) => handleKeyDown(e, section.value)}
          >
            <span>{section.title}</span>
            <ChevronDown
              className={`h-5 w-5 text-gray-500 transition-transform duration-200 ${isActive ? 'rotate-180' : 'rotate-0'}`}
              aria-hidden="true"
            />
          </button>
        </h3>
        <div
          id={panelId}
          role="region"
          aria-labelledby={headerId}
          className={`overflow-hidden transition-all duration-200 ${isActive ? 'max-h-[800px]' : 'max-h-0'}`}
        >
          <div className="p-6 text-base text-gray-700">
            {section.content}
          </div>
        </div>
      </div>
    );
  }), [sections, activeSections, toggleSection, handleKeyDown]);

  if (!sections || sections.length === 0) {
    return null;
  }

  return (
    <div
      className="divide-y divide-gray-200 rounded-lg border border-gray-200 bg-white shadow-sm"
      role="presentation"
    >
      {accordionSections}
    </div>
  );
}
```

Highlighting key differences between attempted implementation and the better solution, and explaining why certain changes can make the code more robust and production-ready.

1. **Type Safety and Props Interface**
   Attempted solution:

```javascript
export default function Accordion({ sections }) {
```

Better solution:

```typescript
type AccordionSection = {
  value: string;
  title: string;
  content: React.ReactNode;
};

interface AccordionProps {
  sections?: AccordionSection[];
  defaultOpen?: string;
  allowMultiple?: boolean;
}

export default function Accordion({
  sections = defaultSections,
  defaultOpen = '',
  allowMultiple = false
}: AccordionProps) {
```

The addition of TypeScript types and interfaces makes the code more maintainable and safer. When other developers use this component, they'll immediately know what props they can pass and what shape the data should have. The TypeScript compiler will catch errors before they reach runtime, like passing incorrectly shaped section objects.

2. **State Management**
   Attempted solution:

```javascript
const [activeSection, setActiveSection] = useState("");
```

Better solution:

```typescript
const [activeSections, setActiveSections] = useState<Set<string>>(
  () => new Set(defaultOpen ? [defaultOpen] : []),
);
```

Better solution has made two important improvements here:

- Using a `Set` instead of a string allows for multiple sections to be open simultaneously (if `allowMultiple` is true)
- The state is initialized with a function that checks for a `defaultOpen` prop, allowing users to specify which section should be open initially

3. **Performance Optimization**
   Attempted solution didn't include any memoization. Better solution uses `useCallback` and `useMemo`:

```typescript
const toggleSection = useCallback(
  (value: string) => {
    setActiveSections((prev) => {
      const newSet = new Set(prev);
      if (newSet.has(value)) {
        newSet.delete(value);
      } else {
        if (!allowMultiple) {
          newSet.clear();
        }
        newSet.add(value);
      }
      return newSet;
    });
  },
  [allowMultiple],
);

const accordionSections = useMemo(
  () =>
    sections.map((section) => {
      // ... section rendering logic
    }),
  [sections, activeSections, toggleSection, handleKeyDown],
);
```

These optimizations prevent unnecessary re-renders, especially important when the accordion contains complex content or is part of a larger application.

4. **Accessibility**
   Attempted solution had basic structure, but Better solution adds comprehensive accessibility features:

```typescript
<button
  id={headerId}
  aria-controls={panelId}
  aria-expanded={isActive}
  className="..."
  onClick={() => toggleSection(section.value)}
  onKeyDown={(e) => handleKeyDown(e, section.value)}
>
```

Better solution has added:

- ARIA attributes for screen readers
- Keyboard navigation support
- Proper semantic HTML structure
- Clear focus states for better keyboard accessibility

5. **Content Structure and Styling**
   Instead of simple strings, Better solution supports rich content through React components:

```typescript
const HtmlContent = () => (
  <div className="space-y-4">
    <p>HTML (HyperText Markup Language) forms the foundation...</p>
    <div className="pl-4 border-l-4 border-blue-200">
      <p><strong>Document Structure:</strong> html, head, body</p>
      // ...
    </div>
  </div>
);
```

This approach:

- Makes content more maintainable by separating it into components
- Allows for rich formatting and styling
- Supports complex content structures including nested components
- Uses Tailwind's utility classes effectively for consistent styling

6. **Error Handling and Defaults**
   Better solution includes better error handling and fallbacks:

```typescript
if (!sections || sections.length === 0) {
  return null;
}
```

And provides meaningful default content:

```typescript
const defaultSections = [
  {
    value: "html",
    title: "HTML - Structure of the Web",
    content: <HtmlContent />
  },
  // ...
];
```

7. **Animations and Transitions**
   While attempted solution had basic show/hide functionality, Better solution adds smooth transitions:

```typescript
className={`
  overflow-hidden transition-all duration-200
  ${isActive ? 'max-h-[800px]' : 'max-h-0'}
`}
```

This creates a more polished user experience with smooth animations when sections open and close.

All these improvements make the component more:

- Maintainable (through TypeScript and better code organization)
- Performant (through memoization and optimized state management)
- Accessible (through ARIA attributes and keyboard support)
- User-friendly (through animations and better styling)
- Flexible (through support for rich content and multiple open sections)

## Writing Tests

This section is about how to setup testing for your React components and write comprehensive unit tests. Let's break this down into manageable steps.

First, let's set up the testing environment for your Vite + TypeScript + React project.

1. First, let's install the necessary dependencies. In your project directory, run:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

Let me explain what each package does:

- `vitest`: A test runner built on top of Vite, providing fast and native testing experience
- `@testing-library/react`: Provides utilities for testing React components
- `@testing-library/jest-dom`: Extends Jest with custom DOM element matchers
- `@testing-library/user-event`: Simulates user interactions in tests
- `jsdom`: Provides a browser-like environment for running tests

2. Now, let's configure Vitest. Create or update your `vite.config.ts`:

```typescript
/// <reference types="vitest" />
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/test/setup.ts",
  },
});
```

3. Create a test setup file at `src/test/setup.ts`:

```typescript
import "@testing-library/jest-dom";

// This adds custom matchers like 'toBeInTheDocument'
expect.extend(window.jestDOM);
```

Now, let's write tests for your Accordion component. I'll create a comprehensive test suite that covers all the important functionality:

```typescript
// src/components/Accordion.test.tsx
import { describe, it, expect, beforeEach } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Accordion from './Accordion';

// Let's create some test data that we'll use across our tests
const testSections = [
  {
    value: 'section1',
    title: 'First Section',
    content: 'Content for first section'
  },
  {
    value: 'section2',
    title: 'Second Section',
    content: 'Content for second section'
  }
];

describe('Accordion Component', () => {
  // This function helps us create a fresh instance of userEvent before each test
  const setup = () => {
    return {
      user: userEvent.setup(),
      ...render(<Accordion sections={testSections} />)
    };
  };

  it('renders all section titles', () => {
    render(<Accordion sections={testSections} />);

    // Check if all section titles are rendered
    testSections.forEach(section => {
      expect(screen.getByText(section.title)).toBeInTheDocument();
    });
  });

  it('shows content when section is clicked', async () => {
    const { user } = setup();

    // Initially, content should be hidden
    expect(screen.queryByText(testSections[0].content)).not.toBeVisible();

    // Click the first section
    await user.click(screen.getByText(testSections[0].title));

    // Content should now be visible
    expect(screen.getByText(testSections[0].content)).toBeVisible();
  });

  it('handles keyboard navigation', async () => {
    const { user } = setup();

    // Find the first section button
    const firstSectionButton = screen.getByText(testSections[0].title);

    // Focus and press Enter
    firstSectionButton.focus();
    await user.keyboard('{Enter}');

    // Content should be visible
    expect(screen.getByText(testSections[0].content)).toBeVisible();

    // Press Space to close
    await user.keyboard(' ');

    // Content should be hidden
    expect(screen.getByText(testSections[0].content)).not.toBeVisible();
  });

  it('supports multiple sections open when allowMultiple is true', async () => {
    const { user } = setup();

    // Render with allowMultiple prop
    render(<Accordion sections={testSections} allowMultiple={true} />);

    // Open first section
    await user.click(screen.getByText(testSections[0].title));

    // Open second section
    await user.click(screen.getByText(testSections[1].title));

    // Both sections should be visible
    expect(screen.getByText(testSections[0].content)).toBeVisible();
    expect(screen.getByText(testSections[1].content)).toBeVisible();
  });

  it('closes previously opened section when allowMultiple is false', async () => {
    const { user } = setup();

    // Open first section
    await user.click(screen.getByText(testSections[0].title));

    // Open second section
    await user.click(screen.getByText(testSections[1].title));

    // First section should be hidden, second visible
    expect(screen.getByText(testSections[0].content)).not.toBeVisible();
    expect(screen.getByText(testSections[1].content)).toBeVisible();
  });

  it('opens default section when defaultOpen prop is provided', () => {
    render(<Accordion sections={testSections} defaultOpen="section1" />);

    // First section should be visible initially
    expect(screen.getByText(testSections[0].content)).toBeVisible();
    // Second section should be hidden
    expect(screen.getByText(testSections[1].content)).not.toBeVisible();
  });

  it('maintains proper ARIA attributes', async () => {
    const { user } = setup();

    // Get the first section button
    const firstSectionButton = screen.getByText(testSections[0].title);

    // Check initial ARIA states
    expect(firstSectionButton).toHaveAttribute('aria-expanded', 'false');

    // Click to open
    await user.click(firstSectionButton);

    // Check updated ARIA states
    expect(firstSectionButton).toHaveAttribute('aria-expanded', 'true');
  });
});
```

Let's add a script to your `package.json` to run the tests:

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage"
  }
}
```

Now you can run your tests with:

```bash
pnpm test
```

Let me explain the key concepts in our test suite:

1. **Test Structure**: We use `describe` to group related tests and `it` for individual test cases. This creates a clear hierarchy and makes test output easy to read.

2. **Setup Function**: The `setup` function creates a fresh instance of `userEvent` and renders the component before each test. This ensures each test starts with a clean slate.

3. **Testing User Interactions**: We use both `fireEvent` and `userEvent`:

   - `userEvent` is preferred for most interactions as it more closely simulates real user behavior
   - `fireEvent` is used for lower-level DOM events when needed

4. **Assertions**: We use various Jest matchers enhanced by `@testing-library/jest-dom`:

   - `toBeInTheDocument()`: Checks if an element is in the DOM
   - `toBeVisible()`: Checks if an element is visible
   - `toHaveAttribute()`: Checks element attributes

5. **Testing Accessibility**: We verify ARIA attributes and keyboard navigation, ensuring the component is accessible.

To run specific test suites or tests, you can use the `.only` modifier:

```typescript
it.only("shows content when section is clicked", async () => {
  // This test will run exclusively
});
```

For debugging tests, you can use the `screen.debug()` function:

```typescript
it('debugging example', () => {
  render(<Accordion sections={testSections} />);
  screen.debug(); // Prints the current DOM state
});
```
