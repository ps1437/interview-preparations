# 🚀 React UI Developer (7+ Years) — Interview Preparation Guide

> A comprehensive guide covering all topics, coding questions, and learning resources for senior-level React/UI developer interviews.

---

## 📋 Table of Contents

1. [JavaScript Core (Deep Level)](#1-javascript-core-deep-level)
2. [React Core Concepts](#2-react-core-concepts)
3. [React Hooks (In Depth)](#3-react-hooks-in-depth)
4. [State Management](#4-state-management)
5. [Performance Optimization](#5-performance-optimization)
6. [TypeScript with React](#6-typescript-with-react)
7. [CSS / Styling](#7-css--styling)
8. [Testing](#8-testing)
9. [Architecture & Design Patterns](#9-architecture--design-patterns)
10. [Build Tools & Toolchain](#10-build-tools--toolchain)
11. [Web APIs & Browser Internals](#11-web-apis--browser-internals)
12. [Accessibility (a11y)](#12-accessibility-a11y)
13. [Security](#13-security)
14. [System Design for Frontend](#14-system-design-for-frontend)
15. [Behavioral / Leadership Questions](#15-behavioral--leadership-questions)
16. [Common Coding Questions](#16-common-coding-questions)
17. [Top Resources & Links](#17-top-resources--links)

---

## 1. JavaScript Core (Deep Level)

> At 7+ years, you're expected to know JS inside-out, not just how to use it.

### Topics to Master

- **Execution Context & Call Stack** — how JS executes code, single-threaded model
- **Closures** — lexical scoping, closure over variables, practical uses (memoization, module pattern)
- **Prototype Chain & Inheritance** — `__proto__`, `Object.create`, class vs prototype
- **`this` keyword** — implicit, explicit (`call`, `apply`, `bind`), arrow functions, class context
- **Event Loop** — Call Stack, Web APIs, Callback Queue, Microtask Queue, `Promise` vs `setTimeout` order
- **Promises & Async/Await** — chaining, error handling, `Promise.all`, `Promise.race`, `Promise.allSettled`
- **Generators & Iterators** — `function*`, `yield`, `for...of`
- **Modules** — ESM vs CommonJS, dynamic imports `import()`, tree shaking
- **Destructuring, Spread/Rest, Optional Chaining, Nullish Coalescing**
- **WeakMap / WeakSet / WeakRef** — memory management
- **Proxy & Reflect** — meta-programming
- **Symbol** — unique keys, `Symbol.iterator`
- **Garbage Collection** — reference counting, mark & sweep
- **Functional Programming** — pure functions, immutability, currying, composition, `map/filter/reduce`

### Key Interview Questions

- What is the difference between `==` and `===`?
- Explain the difference between `var`, `let`, and `const`.
- How does the event loop work? What is the microtask queue?
- What are closures and give a real-world example?
- Explain debounce vs throttle and implement them.
- What is the difference between `Promise.all` and `Promise.allSettled`?
- How does prototypal inheritance differ from classical inheritance?

### Resources

- 📖 [You Don't Know JS (Free)](https://github.com/getify/You-Dont-Know-JS)
- 📖 [JavaScript Info](https://javascript.info/)
- 📺 [JS Visualizer — Event Loop](https://www.jsv9000.app/)
- 📖 [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)

---

## 2. React Core Concepts

### Topics to Master

- **Virtual DOM** — what it is, diffing algorithm (Reconciliation), why it exists
- **Fiber Architecture** — React 16+ rewrite, time-slicing, priority scheduling, Concurrent Mode
- **JSX** — how it compiles (`React.createElement`), Babel transformation
- **Component Types** — Functional vs Class, when to use which
- **Component Lifecycle** — mounting, updating, unmounting (class lifecycle methods & their Hook equivalents)
- **Keys** — why they matter, how React uses them during reconciliation
- **Refs** — `useRef`, `createRef`, `forwardRef`, `useImperativeHandle`
- **Context API** — `createContext`, `Provider`, `useContext`, performance pitfalls
- **Error Boundaries** — `componentDidCatch`, `getDerivedStateFromError`, no Hook equivalent yet
- **Portals** — `ReactDOM.createPortal`, use cases (modals, tooltips)
- **Strict Mode** — double invocation, detecting side effects
- **Fragments** — `<>...</>` and `React.Fragment`
- **Controlled vs Uncontrolled Components**
- **Compound Components Pattern**
- **Render Props Pattern**
- **Higher-Order Components (HOCs)**
- **Suspense & Lazy Loading** — `React.lazy`, `React.Suspense`, data fetching with Suspense

### Key Interview Questions

- Explain the React reconciliation algorithm.
- What is React Fiber and why was it introduced?
- What are the differences between `useEffect` and `componentDidMount`?
- How does React handle key props during re-renders?
- What is the difference between controlled and uncontrolled components?
- Explain the render prop pattern and its trade-offs.
- When would you use `forwardRef`?

### Resources

- 📖 [React Official Docs](https://react.dev/)
- 📖 [React Fiber Architecture (Blog)](https://github.com/acdlite/react-fiber-architecture)
- 📺 [React Reconciliation Deep Dive](https://www.youtube.com/watch?v=ZCuYPiUIONs)

---

## 3. React Hooks (In Depth)

### Topics to Master

- **`useState`** — functional updates, batching updates (React 18)
- **`useEffect`** — dependency array, cleanup function, avoid stale closures
- **`useLayoutEffect`** — synchronous, use cases (DOM measurements)
- **`useRef`** — accessing DOM, persisting values without re-render
- **`useMemo`** — memoizing expensive computations
- **`useCallback`** — memoizing function references
- **`useContext`** — consuming context, re-render behavior
- **`useReducer`** — complex state logic, alternative to useState
- **`useTransition`** — mark state updates as non-urgent (React 18)
- **`useDeferredValue`** — defer re-renders (React 18)
- **`useId`** — generate stable unique IDs (React 18)
- **`useSyncExternalStore`** — subscribe to external stores
- **Custom Hooks** — extracting reusable stateful logic
- **Rules of Hooks** — why they exist (linked list under the hood)

### Key Interview Questions

- What happens if you put an object in the `useEffect` dependency array?
- When would you use `useReducer` over `useState`?
- Explain the difference between `useMemo` and `useCallback`.
- How do you avoid stale closures in `useEffect`?
- How does React batch state updates in React 18 vs React 17?
- Build a custom hook: `useFetch`, `useDebounce`, `useLocalStorage`, `usePrevious`

### Resources

- 📖 [React Hooks Reference](https://react.dev/reference/react)
- 📖 [Dan Abramov — A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)
- 📖 [useHooks (Custom Hook Examples)](https://usehooks.com/)

---

## 4. State Management

### Topics to Master

- **Local State** — `useState`, lifting state up, prop drilling
- **Context API** — global state, when to use vs external libraries
- **Redux** — store, actions, reducers, middleware, `redux-thunk`, `redux-saga`
- **Redux Toolkit (RTK)** — `createSlice`, `createAsyncThunk`, RTK Query
- **Zustand** — lightweight, minimal boilerplate
- **Jotai / Recoil** — atomic state management
- **React Query / TanStack Query** — server state, caching, background refetching, mutations
- **SWR** — stale-while-revalidate strategy
- **When to use what** — server state vs client state, local vs global

### Key Interview Questions

- What are the trade-offs between Context API and Redux?
- Explain the flux architecture.
- What is the difference between server state and client state?
- How does RTK Query differ from React Query?
- How do you handle optimistic updates?
- When would you choose Zustand over Redux?

### Resources

- 📖 [Redux Toolkit Docs](https://redux-toolkit.js.org/)
- 📖 [TanStack Query Docs](https://tanstack.com/query/latest)
- 📖 [Zustand GitHub](https://github.com/pmndrs/zustand)
- 📺 [Theo — State Management in 2024](https://www.youtube.com/watch?v=5-1LM2NySR0)

---

## 5. Performance Optimization

### Topics to Master

- **React.memo** — prevents re-renders when props haven't changed
- **`useMemo` & `useCallback`** — when they actually help (vs adding overhead)
- **Code Splitting** — `React.lazy`, dynamic imports, route-based splitting
- **Virtualization** — `react-window`, `react-virtual`, rendering large lists
- **Avoiding Unnecessary Re-renders** — component structure, state colocation
- **Profiler API** — `React.Profiler`, React DevTools Profiler
- **Web Vitals** — LCP, FID/INP, CLS, FCP, TTFB
- **Lazy Loading Images** — `loading="lazy"`, IntersectionObserver
- **Bundle Analysis** — `webpack-bundle-analyzer`, tree shaking, dead code elimination
- **HTTP Caching** — `Cache-Control`, ETag, service workers
- **Debouncing & Throttling**
- **SSR / SSG / ISR** — Next.js rendering strategies
- **Concurrent Features** — `useTransition`, `useDeferredValue`, Suspense
- **Memory Leaks** — event listeners, timers, subscriptions in useEffect cleanup

### Key Interview Questions

- How would you optimize a React app that is rendering slowly?
- Explain when NOT to use `useMemo` and `useCallback`.
- What are Core Web Vitals and how do you measure them?
- How does virtualization work and when would you implement it?
- How would you implement infinite scroll efficiently?
- What causes unnecessary re-renders and how do you debug them?

### Resources

- 📖 [React Performance Docs](https://react.dev/learn/render-and-commit)
- 📖 [web.dev Performance](https://web.dev/performance/)
- 📺 [Jack Herrington — React Performance](https://www.youtube.com/watch?v=VYRNdFLob0U)
- 📖 [react-window Docs](https://react-window.vercel.app/)

---

## 6. TypeScript with React

### Topics to Master

- **Basic Types** — `string`, `number`, `boolean`, `any`, `unknown`, `never`, `void`
- **Interfaces vs Types** — differences, declaration merging, extending
- **Generics** — `Array<T>`, custom generic functions and components
- **Union & Intersection Types**
- **Utility Types** — `Partial`, `Required`, `Pick`, `Omit`, `Record`, `ReturnType`, `Parameters`
- **Typing React Props** — `React.FC`, `ReactNode`, `ReactElement`, `PropsWithChildren`
- **Typing Events** — `React.ChangeEvent<HTMLInputElement>`, `MouseEvent`
- **Typing Refs** — `RefObject<HTMLDivElement>`
- **Typing Hooks** — `useState<User | null>(null)`, custom hook return types
- **Typing Redux/Context**
- **Conditional Types** — `T extends U ? X : Y`
- **Discriminated Unions** — type narrowing with `switch`/`if`
- **Mapped Types**
- **Template Literal Types**
- **Declaration Files** — `.d.ts`

### Key Interview Questions

- What is the difference between `interface` and `type`?
- How do you type a React component that accepts children?
- Explain `unknown` vs `any`.
- How do you use generics in a custom hook?
- What are discriminated unions and why are they useful?
- How do you type an async function that returns data?

### Resources

- 📖 [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- 📖 [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- 📖 [Total TypeScript (Free Exercises)](https://www.totaltypescript.com/tutorials)

---

## 7. CSS / Styling

### Topics to Master

- **CSS Box Model** — `content-box` vs `border-box`
- **Flexbox** — all properties, use cases
- **CSS Grid** — `grid-template`, `fr` units, `auto-fit`, `minmax`, named areas
- **Positioning** — `relative`, `absolute`, `fixed`, `sticky`, stacking context
- **CSS Variables (Custom Properties)**
- **CSS Specificity & Cascade**
- **Responsive Design** — media queries, mobile-first, container queries
- **CSS Animations & Transitions** — `@keyframes`, `will-change`, hardware acceleration
- **CSS Modules** — scoped styles in React
- **CSS-in-JS** — styled-components, Emotion: pros & cons, runtime vs zero-runtime
- **Tailwind CSS** — utility-first, purging, theming
- **BEM Naming Convention**
- **SCSS / SASS** — nesting, mixins, variables, partials
- **Stacking Context** — when `z-index` doesn't work

### Key Interview Questions

- Explain the difference between `display: none`, `visibility: hidden`, and `opacity: 0`.
- How does CSS specificity work?
- When would you use CSS Grid vs Flexbox?
- What are the trade-offs between CSS-in-JS and CSS Modules?
- How do you create a responsive layout without media queries?
- Explain `position: sticky` and its gotchas.

### Resources

- 📖 [CSS Tricks Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- 📖 [CSS Tricks Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
- 🎮 [Flexbox Froggy](https://flexboxfroggy.com/)
- 🎮 [CSS Grid Garden](https://cssgridgarden.com/)
- 📖 [Tailwind CSS Docs](https://tailwindcss.com/docs)

---

## 8. Testing

### Topics to Master

- **Testing Pyramid** — unit, integration, e2e
- **Jest** — `describe`, `it/test`, `expect`, `beforeEach`, `afterEach`, mocking
- **React Testing Library (RTL)** — philosophy (`getByRole`, `getByText`), `userEvent`, `render`, `screen`
- **Mocking** — `jest.fn()`, `jest.spyOn()`, mocking modules, mocking API calls (`msw`)
- **Async Testing** — `waitFor`, `findBy*` queries
- **Snapshot Testing** — when to use and when to avoid
- **Coverage** — what it means, not a metric for quality
- **Cypress / Playwright** — e2e testing, component testing
- **Storybook** — visual testing, `@storybook/test`
- **Mock Service Worker (MSW)** — intercepting network requests in tests
- **Testing Custom Hooks** — `renderHook`

### Key Interview Questions

- What is the difference between `getBy`, `queryBy`, and `findBy` in RTL?
- How do you test a component that makes API calls?
- What is MSW and why is it preferred over mocking fetch directly?
- How do you write a test for a custom hook?
- What makes a good unit test?
- How would you test a form with validation?

### Resources

- 📖 [React Testing Library Docs](https://testing-library.com/docs/react-testing-library/intro/)
- 📖 [Jest Docs](https://jestjs.io/docs/getting-started)
- 📖 [MSW Docs](https://mswjs.io/)
- 📺 [Kent C. Dodds — Testing React Apps](https://testingjavascript.com/)

---

## 9. Architecture & Design Patterns

### Topics to Master

- **Component Design Principles** — Single Responsibility, DRY, separation of concerns
- **Atomic Design** — atoms, molecules, organisms, templates, pages
- **Feature-based Folder Structure** — organizing by feature vs type
- **Compound Components** — `<Select>`, `<Accordion>` with context
- **Render Props** — passing functions as props for dynamic rendering
- **HOCs** — enhancing components without modifying them
- **Container / Presentational Pattern** — logic vs UI separation
- **Slots Pattern** — children-based composition
- **Observer Pattern** — event emitters, pub/sub
- **Module Pattern** — encapsulation, IIFE
- **Factory Pattern** — creating objects/components dynamically
- **Facade Pattern** — simplifying complex subsystems (API client wrappers)
- **Strategy Pattern** — algorithm families (sorting, formatting)
- **Micro-Frontend Architecture** — Module Federation, single-spa
- **Monorepo** — Turborepo, Nx, shared packages

### Key Interview Questions

- How would you structure a large React application?
- Explain the compound component pattern with an example.
- How would you implement a design system from scratch?
- What is micro-frontend architecture and when would you use it?
- How do you handle shared logic between components?
- Explain the difference between HOCs and render props.

### Resources

- 📖 [patterns.dev](https://www.patterns.dev/)
- 📖 [Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/)
- 📖 [Bulletproof React](https://github.com/alan2207/bulletproof-react)
- 📖 [Module Federation Docs](https://module-federation.io/)

---

## 10. Build Tools & Toolchain

### Topics to Master

- **Webpack** — entry, output, loaders, plugins, code splitting, `optimization.splitChunks`
- **Vite** — ESM-based dev server, Rollup for production, HMR
- **Babel** — transpilation, presets (`@babel/preset-env`, `@babel/preset-react`), plugins
- **ESLint** — configuration, custom rules, `eslint-plugin-react-hooks`
- **Prettier** — code formatting
- **Husky & lint-staged** — pre-commit hooks
- **npm / yarn / pnpm** — lockfiles, workspaces, dependency resolution
- **CI/CD** — GitHub Actions, automated testing and deployment
- **Docker** — containerizing React apps
- **Env Variables** — `.env`, `REACT_APP_*`, Vite's `import.meta.env`
- **Tree Shaking** — ES modules, side effects
- **Source Maps**

### Key Interview Questions

- What is the difference between Webpack and Vite?
- How does tree shaking work?
- How do you analyze and reduce bundle size?
- What is Hot Module Replacement (HMR)?
- How do you configure code splitting in Webpack?
- What does Babel do and why do we need it?

### Resources

- 📖 [Webpack Docs](https://webpack.js.org/concepts/)
- 📖 [Vite Docs](https://vitejs.dev/guide/)
- 📖 [Babel Docs](https://babeljs.io/docs/)

---

## 11. Web APIs & Browser Internals

### Topics to Master

- **How Browsers Work** — parsing HTML/CSS, render tree, layout, paint, compositing
- **Critical Rendering Path** — blocking resources, render-blocking CSS/JS
- **Reflow vs Repaint** — what triggers them, how to avoid them
- **DOM API** — `querySelector`, `addEventListener`, event delegation, event bubbling/capturing
- **Storage APIs** — `localStorage`, `sessionStorage`, `IndexedDB`, `cookies`
- **Fetch API & XMLHttpRequest**
- **WebSockets** — real-time communication
- **Service Workers** — offline caching, background sync, push notifications
- **Web Workers** — offloading computation to background threads
- **IntersectionObserver** — lazy loading, infinite scroll
- **ResizeObserver** — element size changes
- **MutationObserver** — DOM changes
- **History API** — SPA routing, `pushState`, `popstate`
- **CORS** — same-origin policy, preflight requests, headers
- **HTTP/2 & HTTP/3** — multiplexing, header compression
- **Browser Caching** — `Cache-Control`, ETags, `max-age`

### Key Interview Questions

- Explain event bubbling and capturing.
- What is the critical rendering path?
- How do service workers work?
- What is CORS and how do you configure it?
- How does the browser render a webpage?
- What is the difference between `reflow` and `repaint`?

### Resources

- 📖 [MDN Web Docs](https://developer.mozilla.org/en-US/)
- 📖 [web.dev — How Browsers Work](https://web.dev/articles/howbrowserswork)
- 📖 [Google — Critical Rendering Path](https://web.dev/articles/critical-rendering-path)

---

## 12. Accessibility (a11y)

### Topics to Master

- **WCAG Guidelines** — Level A, AA, AAA
- **Semantic HTML** — correct use of `<header>`, `<nav>`, `<main>`, `<button>`, `<a>`
- **ARIA Attributes** — `role`, `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-hidden`, `aria-live`
- **Keyboard Navigation** — focus management, `tabIndex`, focus trapping in modals
- **Screen Readers** — how they work, testing with NVDA/VoiceOver
- **Color Contrast** — WCAG 4.5:1 ratio for normal text
- **Focus Visible** — `:focus-visible`, never remove outlines without replacement
- **Skip Links** — "Skip to main content"
- **Forms** — associated labels, error messages, `aria-invalid`
- **React Accessibility** — `eslint-plugin-jsx-a11y`

### Key Interview Questions

- What is ARIA and when should you use it?
- How do you make a custom dropdown accessible?
- What is focus trapping and when is it needed?
- How do you test accessibility?
- What are the most common accessibility mistakes in React apps?

### Resources

- 📖 [web.dev Accessibility](https://web.dev/accessibility/)
- 📖 [MDN Accessibility Guide](https://developer.mozilla.org/en-US/docs/Learn/Accessibility)
- 📖 [a11yproject.com](https://www.a11yproject.com/)
- 🔧 [axe DevTools](https://www.deque.com/axe/)

---

## 13. Security

### Topics to Master

- **XSS (Cross-Site Scripting)** — how it happens, `dangerouslySetInnerHTML`, sanitization
- **CSRF (Cross-Site Request Forgery)** — tokens, `SameSite` cookies
- **Content Security Policy (CSP)** — `Content-Security-Policy` header
- **HTTPS & HSTS**
- **SQL Injection** (frontend awareness)
- **Dependency Vulnerabilities** — `npm audit`, Snyk
- **Sensitive Data Exposure** — don't store tokens in localStorage
- **Clickjacking** — `X-Frame-Options` header
- **CORS Misconfigurations**
- **Subresource Integrity (SRI)**

### Key Interview Questions

- How does React protect against XSS by default?
- When is it safe to use `dangerouslySetInnerHTML`?
- Where should you store JWT tokens?
- What is CSRF and how do you prevent it?
- How does Content Security Policy work?

### Resources

- 📖 [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- 📖 [web.dev Security](https://web.dev/secure/)

---

## 14. System Design for Frontend

### Topics to Master

- **Component API Design** — prop naming, composability, extensibility
- **Design System Architecture** — tokens, theming, component library
- **Scalable Folder Structure** — monorepo vs polyrepo
- **Client-Side Routing** — React Router, history API, code splitting by route
- **Authentication Flow** — JWT, OAuth, session management, protected routes
- **Real-Time Features** — WebSockets, polling, SSE
- **Caching Strategies** — HTTP caching, SWR, React Query
- **CDN & Asset Optimization**
- **SSR / SSG / CSR / ISR** — Next.js rendering modes
- **Internationalization (i18n)** — `react-i18next`, RTL support
- **Feature Flags**
- **Error Monitoring** — Sentry, error boundaries
- **Analytics Integration**
- **Micro-frontend Architecture**

### Common System Design Questions

- Design a reusable component library from scratch.
- Design a scalable e-commerce product listing page.
- How would you implement real-time notifications?
- Design an autocomplete/search component.
- How would you implement infinite scroll?
- Design a dashboard with multiple data visualizations.
- How would you handle authentication in a React SPA?

### Resources

- 📖 [GreatFrontEnd System Design](https://www.greatfrontend.com/system-design)
- 📖 [Frontend System Design Guide](https://github.com/donnemartin/system-design-primer) *(backend-focused but useful)*

---

## 15. Behavioral / Leadership Questions

> At 7+ years, you'll be evaluated heavily on soft skills, mentorship, and ownership.

### Questions to Prepare For

- Tell me about a time you made a significant technical decision. What was the outcome?
- How do you mentor junior developers?
- Describe a time you had a disagreement with a team member on a technical approach.
- How do you handle technical debt?
- Tell me about a performance issue you solved in production.
- How do you stay up to date with the React ecosystem?
- Describe your process for a frontend architecture decision.
- How do you handle scope creep or changing requirements mid-sprint?
- What has been your biggest technical failure and what did you learn?

### Tips

- Use the **STAR method** (Situation, Task, Action, Result)
- Quantify your impact when possible (e.g., "reduced load time by 40%")
- Show ownership and proactiveness
- Demonstrate cross-team collaboration

---

## 16. Common Coding Questions

### JavaScript Coding Challenges

```javascript
// 1. Implement debounce
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

// 2. Implement throttle
function throttle(fn, limit) {
  let lastCall = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      return fn.apply(this, args);
    }
  };
}

// 3. Deep clone an object
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (Array.isArray(obj)) return obj.map(deepClone);
  return Object.fromEntries(Object.entries(obj).map(([k, v]) => [k, deepClone(v)]));
}

// 4. Flatten a nested array
function flatten(arr) {
  return arr.reduce((flat, item) =>
    Array.isArray(item) ? flat.concat(flatten(item)) : [...flat, item], []);
}

// 5. Implement Promise.all from scratch
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    const results = [];
    let count = 0;
    promises.forEach((p, i) => {
      Promise.resolve(p).then(val => {
        results[i] = val;
        if (++count === promises.length) resolve(results);
      }).catch(reject);
    });
  });
}

// 6. Curry a function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) return fn(...args);
    return (...more) => curried(...args, ...more);
  };
}

// 7. Memoize a function
function memoize(fn) {
  const cache = new Map();
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}
```

### React Coding Challenges

```jsx
// 1. Custom useFetch hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    fetch(url)
      .then(res => res.json())
      .then(data => { if (!cancelled) { setData(data); setLoading(false); }})
      .catch(err => { if (!cancelled) { setError(err); setLoading(false); }});
    return () => { cancelled = true; };
  }, [url]);

  return { data, loading, error };
}

// 2. Custom useDebounce hook
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debouncedValue;
}

// 3. Custom usePrevious hook
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; });
  return ref.current;
}

// 4. Custom useLocalStorage hook
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch { return initialValue; }
  });

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) { console.error(error); }
  };

  return [storedValue, setValue];
}

// 5. Infinite Scroll Hook
function useInfiniteScroll(callback) {
  const observer = useRef();
  const lastElementRef = useCallback(node => {
    if (observer.current) observer.current.disconnect();
    observer.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting) callback();
    });
    if (node) observer.current.observe(node);
  }, [callback]);
  return lastElementRef;
}
```

### Must-Practice Component Implementations

- `<Accordion>` with keyboard navigation
- `<Modal>` with focus trap and portal
- `<Autocomplete>` with debouncing and keyboard support
- `<VirtualList>` for large datasets
- `<Tabs>` accessible component
- `<Toast/Notification>` system
- `<DataTable>` with sorting, filtering, pagination
- `<Form>` with custom validation using `useReducer`

---

## 17. Top Resources & Links

### Official Documentation

| Resource | Link |
|---|---|
| React Official Docs | https://react.dev |
| TypeScript Handbook | https://www.typescriptlang.org/docs |
| MDN Web Docs | https://developer.mozilla.org |
| web.dev | https://web.dev |

### Books & Guides (Free)

| Resource | Link |
|---|---|
| You Don't Know JS | https://github.com/getify/You-Dont-Know-JS |
| Bulletproof React | https://github.com/alan2207/bulletproof-react |
| JavaScript Info | https://javascript.info |
| patterns.dev | https://www.patterns.dev |
| React TypeScript Cheatsheet | https://react-typescript-cheatsheet.netlify.app |

### Interview Prep Platforms

| Resource | Link |
|---|---|
| GreatFrontEnd | https://www.greatfrontend.com |
| Frontend Expert | https://www.frontendexpert.io |
| BigFrontEnd.dev | https://bigfrontend.dev |
| LeetCode (JS category) | https://leetcode.com |
| InterviewBit Frontend | https://www.interviewbit.com/react-interview-questions |

### YouTube Channels

| Channel | Focus |
|---|---|
| Theo (t3.gg) | React, TypeScript, Modern Stack |
| Jack Herrington | Advanced React Patterns |
| Fireship | Quick concept overviews |
| Web Dev Simplified | React, CSS, JS concepts |
| ByteGrad | Next.js, React |

### Tools & Playgrounds

| Tool | Link |
|---|---|
| CodeSandbox | https://codesandbox.io |
| StackBlitz | https://stackblitz.com |
| React Playground | https://playcode.io/react |
| JS Visualizer (Event Loop) | https://www.jsv9000.app |
| CSS Grid Generator | https://cssgrid-generator.netlify.app |

---

## ⏱️ Recommended Study Plan

| Week | Focus |
|---|---|
| Week 1 | JS Deep Dive + React Core + Hooks |
| Week 2 | Performance + TypeScript + State Management |
| Week 3 | Testing + Architecture + Build Tools |
| Week 4 | System Design + Accessibility + Security |
| Week 5 | Coding Practice + Mock Interviews |

---

## 💡 Final Tips

- **Practice coding out loud** — interviewers want to hear your thought process
- **Know your trade-offs** — never say "X is always better than Y"
- **Review your past projects** — be ready to dive deep into decisions you made
- **Read release notes** — React 18, React 19 features show you're current
- **Build something** — a side project you can talk about always impresses
- **Ask clarifying questions** — especially in system design rounds

---

*Good luck with your interviews! 🎯*
