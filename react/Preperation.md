# \ud83d\ude80 React UI Developer (7+ Years) \u2014 Interview Preparation Guide

> A comprehensive guide covering all topics, coding questions, and learning resources for senior-level React/UI developer interviews.

---

## \ud83d\udccb Table of Contents

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

- **Execution Context & Call Stack** \u2014 how JS executes code, single-threaded model
- **Closures** \u2014 lexical scoping, closure over variables, practical uses (memoization, module pattern)
- **Prototype Chain & Inheritance** \u2014 `__proto__`, `Object.create`, class vs prototype
- **`this` keyword** \u2014 implicit, explicit (`call`, `apply`, `bind`), arrow functions, class context
- **Event Loop** \u2014 Call Stack, Web APIs, Callback Queue, Microtask Queue, `Promise` vs `setTimeout` order
- **Promises & Async/Await** \u2014 chaining, error handling, `Promise.all`, `Promise.race`, `Promise.allSettled`
- **Generators & Iterators** \u2014 `function*`, `yield`, `for...of`
- **Modules** \u2014 ESM vs CommonJS, dynamic imports `import()`, tree shaking
- **Destructuring, Spread/Rest, Optional Chaining, Nullish Coalescing**
- **WeakMap / WeakSet / WeakRef** \u2014 memory management
- **Proxy & Reflect** \u2014 meta-programming
- **Symbol** \u2014 unique keys, `Symbol.iterator`
- **Garbage Collection** \u2014 reference counting, mark & sweep
- **Functional Programming** \u2014 pure functions, immutability, currying, composition, `map/filter/reduce`

### Key Interview Questions

- What is the difference between `==` and `===`?
- Explain the difference between `var`, `let`, and `const`.
- How does the event loop work? What is the microtask queue?
- What are closures and give a real-world example?
- Explain debounce vs throttle and implement them.
- What is the difference between `Promise.all` and `Promise.allSettled`?
- How does prototypal inheritance differ from classical inheritance?

### Resources

- \ud83d\udcd6 [You Don't Know JS (Free)](https://github.com/getify/You-Dont-Know-JS)
- \ud83d\udcd6 [JavaScript Info](https://javascript.info/)
- \ud83d\udcfa [JS Visualizer \u2014 Event Loop](https://www.jsv9000.app/)
- \ud83d\udcd6 [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)

---

## 2. React Core Concepts

### Topics to Master

- **Virtual DOM** \u2014 what it is, diffing algorithm (Reconciliation), why it exists
- **Fiber Architecture** \u2014 React 16+ rewrite, time-slicing, priority scheduling, Concurrent Mode
- **JSX** \u2014 how it compiles (`React.createElement`), Babel transformation
- **Component Types** \u2014 Functional vs Class, when to use which
- **Component Lifecycle** \u2014 mounting, updating, unmounting (class lifecycle methods & their Hook equivalents)
- **Keys** \u2014 why they matter, how React uses them during reconciliation
- **Refs** \u2014 `useRef`, `createRef`, `forwardRef`, `useImperativeHandle`
- **Context API** \u2014 `createContext`, `Provider`, `useContext`, performance pitfalls
- **Error Boundaries** \u2014 `componentDidCatch`, `getDerivedStateFromError`, no Hook equivalent yet
- **Portals** \u2014 `ReactDOM.createPortal`, use cases (modals, tooltips)
- **Strict Mode** \u2014 double invocation, detecting side effects
- **Fragments** \u2014 `<>...</>` and `React.Fragment`
- **Controlled vs Uncontrolled Components**
- **Compound Components Pattern**
- **Render Props Pattern**
- **Higher-Order Components (HOCs)**
- **Suspense & Lazy Loading** \u2014 `React.lazy`, `React.Suspense`, data fetching with Suspense

### Key Interview Questions

- Explain the React reconciliation algorithm.
- What is React Fiber and why was it introduced?
- What are the differences between `useEffect` and `componentDidMount`?
- How does React handle key props during re-renders?
- What is the difference between controlled and uncontrolled components?
- Explain the render prop pattern and its trade-offs.
- When would you use `forwardRef`?

### Resources

- \ud83d\udcd6 [React Official Docs](https://react.dev/)
- \ud83d\udcd6 [React Fiber Architecture (Blog)](https://github.com/acdlite/react-fiber-architecture)
- \ud83d\udcfa [React Reconciliation Deep Dive](https://www.youtube.com/watch?v=ZCuYPiUIONs)

---

## 3. React Hooks (In Depth)

### Topics to Master

- **`useState`** \u2014 functional updates, batching updates (React 18)
- **`useEffect`** \u2014 dependency array, cleanup function, avoid stale closures
- **`useLayoutEffect`** \u2014 synchronous, use cases (DOM measurements)
- **`useRef`** \u2014 accessing DOM, persisting values without re-render
- **`useMemo`** \u2014 memoizing expensive computations
- **`useCallback`** \u2014 memoizing function references
- **`useContext`** \u2014 consuming context, re-render behavior
- **`useReducer`** \u2014 complex state logic, alternative to useState
- **`useTransition`** \u2014 mark state updates as non-urgent (React 18)
- **`useDeferredValue`** \u2014 defer re-renders (React 18)
- **`useId`** \u2014 generate stable unique IDs (React 18)
- **`useSyncExternalStore`** \u2014 subscribe to external stores
- **Custom Hooks** \u2014 extracting reusable stateful logic
- **Rules of Hooks** \u2014 why they exist (linked list under the hood)

### Key Interview Questions

- What happens if you put an object in the `useEffect` dependency array?
- When would you use `useReducer` over `useState`?
- Explain the difference between `useMemo` and `useCallback`.
- How do you avoid stale closures in `useEffect`?
- How does React batch state updates in React 18 vs React 17?
- Build a custom hook: `useFetch`, `useDebounce`, `useLocalStorage`, `usePrevious`

### Resources

- \ud83d\udcd6 [React Hooks Reference](https://react.dev/reference/react)
- \ud83d\udcd6 [Dan Abramov \u2014 A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)
- \ud83d\udcd6 [useHooks (Custom Hook Examples)](https://usehooks.com/)

---

## 4. State Management

### Topics to Master

- **Local State** \u2014 `useState`, lifting state up, prop drilling
- **Context API** \u2014 global state, when to use vs external libraries
- **Redux** \u2014 store, actions, reducers, middleware, `redux-thunk`, `redux-saga`
- **Redux Toolkit (RTK)** \u2014 `createSlice`, `createAsyncThunk`, RTK Query
- **Zustand** \u2014 lightweight, minimal boilerplate
- **Jotai / Recoil** \u2014 atomic state management
- **React Query / TanStack Query** \u2014 server state, caching, background refetching, mutations
- **SWR** \u2014 stale-while-revalidate strategy
- **When to use what** \u2014 server state vs client state, local vs global

### Key Interview Questions

- What are the trade-offs between Context API and Redux?
- Explain the flux architecture.
- What is the difference between server state and client state?
- How does RTK Query differ from React Query?
- How do you handle optimistic updates?
- When would you choose Zustand over Redux?

### Resources

- \ud83d\udcd6 [Redux Toolkit Docs](https://redux-toolkit.js.org/)
- \ud83d\udcd6 [TanStack Query Docs](https://tanstack.com/query/latest)
- \ud83d\udcd6 [Zustand GitHub](https://github.com/pmndrs/zustand)
- \ud83d\udcfa [Theo \u2014 State Management in 2024](https://www.youtube.com/watch?v=5-1LM2NySR0)

---

## 5. Performance Optimization

### Topics to Master

- **React.memo** \u2014 prevents re-renders when props haven't changed
- **`useMemo` & `useCallback`** \u2014 when they actually help (vs adding overhead)
- **Code Splitting** \u2014 `React.lazy`, dynamic imports, route-based splitting
- **Virtualization** \u2014 `react-window`, `react-virtual`, rendering large lists
- **Avoiding Unnecessary Re-renders** \u2014 component structure, state colocation
- **Profiler API** \u2014 `React.Profiler`, React DevTools Profiler
- **Web Vitals** \u2014 LCP, FID/INP, CLS, FCP, TTFB
- **Lazy Loading Images** \u2014 `loading="lazy"`, IntersectionObserver
- **Bundle Analysis** \u2014 `webpack-bundle-analyzer`, tree shaking, dead code elimination
- **HTTP Caching** \u2014 `Cache-Control`, ETag, service workers
- **Debouncing & Throttling**
- **SSR / SSG / ISR** \u2014 Next.js rendering strategies
- **Concurrent Features** \u2014 `useTransition`, `useDeferredValue`, Suspense
- **Memory Leaks** \u2014 event listeners, timers, subscriptions in useEffect cleanup

### Key Interview Questions

- How would you optimize a React app that is rendering slowly?
- Explain when NOT to use `useMemo` and `useCallback`.
- What are Core Web Vitals and how do you measure them?
- How does virtualization work and when would you implement it?
- How would you implement infinite scroll efficiently?
- What causes unnecessary re-renders and how do you debug them?

### Resources

- \ud83d\udcd6 [React Performance Docs](https://react.dev/learn/render-and-commit)
- \ud83d\udcd6 [web.dev Performance](https://web.dev/performance/)
- \ud83d\udcfa [Jack Herrington \u2014 React Performance](https://www.youtube.com/watch?v=VYRNdFLob0U)
- \ud83d\udcd6 [react-window Docs](https://react-window.vercel.app/)

---

## 6. TypeScript with React

### Topics to Master

- **Basic Types** \u2014 `string`, `number`, `boolean`, `any`, `unknown`, `never`, `void`
- **Interfaces vs Types** \u2014 differences, declaration merging, extending
- **Generics** \u2014 `Array<T>`, custom generic functions and components
- **Union & Intersection Types**
- **Utility Types** \u2014 `Partial`, `Required`, `Pick`, `Omit`, `Record`, `ReturnType`, `Parameters`
- **Typing React Props** \u2014 `React.FC`, `ReactNode`, `ReactElement`, `PropsWithChildren`
- **Typing Events** \u2014 `React.ChangeEvent<HTMLInputElement>`, `MouseEvent`
- **Typing Refs** \u2014 `RefObject<HTMLDivElement>`
- **Typing Hooks** \u2014 `useState<User | null>(null)`, custom hook return types
- **Typing Redux/Context**
- **Conditional Types** \u2014 `T extends U ? X : Y`
- **Discriminated Unions** \u2014 type narrowing with `switch`/`if`
- **Mapped Types**
- **Template Literal Types**
- **Declaration Files** \u2014 `.d.ts`

### Key Interview Questions

- What is the difference between `interface` and `type`?
- How do you type a React component that accepts children?
- Explain `unknown` vs `any`.
- How do you use generics in a custom hook?
- What are discriminated unions and why are they useful?
- How do you type an async function that returns data?

### Resources

- \ud83d\udcd6 [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- \ud83d\udcd6 [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- \ud83d\udcd6 [Total TypeScript (Free Exercises)](https://www.totaltypescript.com/tutorials)

---

## 7. CSS / Styling

### Topics to Master

- **CSS Box Model** \u2014 `content-box` vs `border-box`
- **Flexbox** \u2014 all properties, use cases
- **CSS Grid** \u2014 `grid-template`, `fr` units, `auto-fit`, `minmax`, named areas
- **Positioning** \u2014 `relative`, `absolute`, `fixed`, `sticky`, stacking context
- **CSS Variables (Custom Properties)**
- **CSS Specificity & Cascade**
- **Responsive Design** \u2014 media queries, mobile-first, container queries
- **CSS Animations & Transitions** \u2014 `@keyframes`, `will-change`, hardware acceleration
- **CSS Modules** \u2014 scoped styles in React
- **CSS-in-JS** \u2014 styled-components, Emotion: pros & cons, runtime vs zero-runtime
- **Tailwind CSS** \u2014 utility-first, purging, theming
- **BEM Naming Convention**
- **SCSS / SASS** \u2014 nesting, mixins, variables, partials
- **Stacking Context** \u2014 when `z-index` doesn't work

### Key Interview Questions

- Explain the difference between `display: none`, `visibility: hidden`, and `opacity: 0`.
- How does CSS specificity work?
- When would you use CSS Grid vs Flexbox?
- What are the trade-offs between CSS-in-JS and CSS Modules?
- How do you create a responsive layout without media queries?
- Explain `position: sticky` and its gotchas.

### Resources

- \ud83d\udcd6 [CSS Tricks Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- \ud83d\udcd6 [CSS Tricks Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
- \ud83c\udfae [Flexbox Froggy](https://flexboxfroggy.com/)
- \ud83c\udfae [CSS Grid Garden](https://cssgridgarden.com/)
- \ud83d\udcd6 [Tailwind CSS Docs](https://tailwindcss.com/docs)

---

## 8. Testing

### Topics to Master

- **Testing Pyramid** \u2014 unit, integration, e2e
- **Jest** \u2014 `describe`, `it/test`, `expect`, `beforeEach`, `afterEach`, mocking
- **React Testing Library (RTL)** \u2014 philosophy (`getByRole`, `getByText`), `userEvent`, `render`, `screen`
- **Mocking** \u2014 `jest.fn()`, `jest.spyOn()`, mocking modules, mocking API calls (`msw`)
- **Async Testing** \u2014 `waitFor`, `findBy*` queries
- **Snapshot Testing** \u2014 when to use and when to avoid
- **Coverage** \u2014 what it means, not a metric for quality
- **Cypress / Playwright** \u2014 e2e testing, component testing
- **Storybook** \u2014 visual testing, `@storybook/test`
- **Mock Service Worker (MSW)** \u2014 intercepting network requests in tests
- **Testing Custom Hooks** \u2014 `renderHook`

### Key Interview Questions

- What is the difference between `getBy`, `queryBy`, and `findBy` in RTL?
- How do you test a component that makes API calls?
- What is MSW and why is it preferred over mocking fetch directly?
- How do you write a test for a custom hook?
- What makes a good unit test?
- How would you test a form with validation?

### Resources

- \ud83d\udcd6 [React Testing Library Docs](https://testing-library.com/docs/react-testing-library/intro/)
- \ud83d\udcd6 [Jest Docs](https://jestjs.io/docs/getting-started)
- \ud83d\udcd6 [MSW Docs](https://mswjs.io/)
- \ud83d\udcfa [Kent C. Dodds \u2014 Testing React Apps](https://testingjavascript.com/)

---

## 9. Architecture & Design Patterns

### Topics to Master

- **Component Design Principles** \u2014 Single Responsibility, DRY, separation of concerns
- **Atomic Design** \u2014 atoms, molecules, organisms, templates, pages
- **Feature-based Folder Structure** \u2014 organizing by feature vs type
- **Compound Components** \u2014 `<Select>`, `<Accordion>` with context
- **Render Props** \u2014 passing functions as props for dynamic rendering
- **HOCs** \u2014 enhancing components without modifying them
- **Container / Presentational Pattern** \u2014 logic vs UI separation
- **Slots Pattern** \u2014 children-based composition
- **Observer Pattern** \u2014 event emitters, pub/sub
- **Module Pattern** \u2014 encapsulation, IIFE
- **Factory Pattern** \u2014 creating objects/components dynamically
- **Facade Pattern** \u2014 simplifying complex subsystems (API client wrappers)
- **Strategy Pattern** \u2014 algorithm families (sorting, formatting)
- **Micro-Frontend Architecture** \u2014 Module Federation, single-spa
- **Monorepo** \u2014 Turborepo, Nx, shared packages

### Key Interview Questions

- How would you structure a large React application?
- Explain the compound component pattern with an example.
- How would you implement a design system from scratch?
- What is micro-frontend architecture and when would you use it?
- How do you handle shared logic between components?
- Explain the difference between HOCs and render props.

### Resources

- \ud83d\udcd6 [patterns.dev](https://www.patterns.dev/)
- \ud83d\udcd6 [Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/)
- \ud83d\udcd6 [Bulletproof React](https://github.com/alan2207/bulletproof-react)
- \ud83d\udcd6 [Module Federation Docs](https://module-federation.io/)

---

## 10. Build Tools & Toolchain

### Topics to Master

- **Webpack** \u2014 entry, output, loaders, plugins, code splitting, `optimization.splitChunks`
- **Vite** \u2014 ESM-based dev server, Rollup for production, HMR
- **Babel** \u2014 transpilation, presets (`@babel/preset-env`, `@babel/preset-react`), plugins
- **ESLint** \u2014 configuration, custom rules, `eslint-plugin-react-hooks`
- **Prettier** \u2014 code formatting
- **Husky & lint-staged** \u2014 pre-commit hooks
- **npm / yarn / pnpm** \u2014 lockfiles, workspaces, dependency resolution
- **CI/CD** \u2014 GitHub Actions, automated testing and deployment
- **Docker** \u2014 containerizing React apps
- **Env Variables** \u2014 `.env`, `REACT_APP_*`, Vite's `import.meta.env`
- **Tree Shaking** \u2014 ES modules, side effects
- **Source Maps**

### Key Interview Questions

- What is the difference between Webpack and Vite?
- How does tree shaking work?
- How do you analyze and reduce bundle size?
- What is Hot Module Replacement (HMR)?
- How do you configure code splitting in Webpack?
- What does Babel do and why do we need it?

### Resources

- \ud83d\udcd6 [Webpack Docs](https://webpack.js.org/concepts/)
- \ud83d\udcd6 [Vite Docs](https://vitejs.dev/guide/)
- \ud83d\udcd6 [Babel Docs](https://babeljs.io/docs/)

---

## 11. Web APIs & Browser Internals

### Topics to Master

- **How Browsers Work** \u2014 parsing HTML/CSS, render tree, layout, paint, compositing
- **Critical Rendering Path** \u2014 blocking resources, render-blocking CSS/JS
- **Reflow vs Repaint** \u2014 what triggers them, how to avoid them
- **DOM API** \u2014 `querySelector`, `addEventListener`, event delegation, event bubbling/capturing
- **Storage APIs** \u2014 `localStorage`, `sessionStorage`, `IndexedDB`, `cookies`
- **Fetch API & XMLHttpRequest**
- **WebSockets** \u2014 real-time communication
- **Service Workers** \u2014 offline caching, background sync, push notifications
- **Web Workers** \u2014 offloading computation to background threads
- **IntersectionObserver** \u2014 lazy loading, infinite scroll
- **ResizeObserver** \u2014 element size changes
- **MutationObserver** \u2014 DOM changes
- **History API** \u2014 SPA routing, `pushState`, `popstate`
- **CORS** \u2014 same-origin policy, preflight requests, headers
- **HTTP/2 & HTTP/3** \u2014 multiplexing, header compression
- **Browser Caching** \u2014 `Cache-Control`, ETags, `max-age`

### Key Interview Questions

- Explain event bubbling and capturing.
- What is the critical rendering path?
- How do service workers work?
- What is CORS and how do you configure it?
- How does the browser render a webpage?
- What is the difference between `reflow` and `repaint`?

### Resources

- \ud83d\udcd6 [MDN Web Docs](https://developer.mozilla.org/en-US/)
- \ud83d\udcd6 [web.dev \u2014 How Browsers Work](https://web.dev/articles/howbrowserswork)
- \ud83d\udcd6 [Google \u2014 Critical Rendering Path](https://web.dev/articles/critical-rendering-path)

---

## 12. Accessibility (a11y)

### Topics to Master

- **WCAG Guidelines** \u2014 Level A, AA, AAA
- **Semantic HTML** \u2014 correct use of `<header>`, `<nav>`, `<main>`, `<button>`, `<a>`
- **ARIA Attributes** \u2014 `role`, `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-hidden`, `aria-live`
- **Keyboard Navigation** \u2014 focus management, `tabIndex`, focus trapping in modals
- **Screen Readers** \u2014 how they
