# Tailwind CSS

## 1. Tailwind CSS?

A utility-first CSS framework packed with classes like `flex`, `pt-4`, `text-center` and `rotate-90` that can be composed to build any design, directly in your markup.

Tailwind automatically removes all unused CSS when building for production, which means your final CSS bundle is the smallest it could possibly be. In fact, most Tailwind projects ship less than `10kB` of CSS to the client.

## 2. TailwindCSS cheatsheet

**Layout & Display**

```css
.block                -> display: block;
.inline               -> display: inline;
.inline-block         -> display: inline-block;
.flex                 -> display: flex;
.inline-flex          -> display: inline-flex;
.grid                 -> display: grid;
.hidden               -> display: none;

.visible              -> visibility: visible;
.invisible            -> visibility: hidden;

.overflow-hidden      -> overflow: hidden;
.overflow-auto        -> overflow: auto;
.overflow-scroll      -> overflow: scroll;
.overflow-x-auto      -> overflow-x: auto;
.overflow-y-auto      -> overflow-y: auto;
```

**Positioning**

```css
.static               -> position: static;
.relative             -> position: relative;
.absolute             -> position: absolute;
.fixed                -> position: fixed;
.sticky               -> position: sticky;

.inset-0              -> top: 0; right: 0; bottom: 0; left: 0;
.inset-x-0            -> left: 0; right: 0;
.inset-y-0            -> top: 0; bottom: 0;
.top-0                -> top: 0;
.right-0              -> right: 0;
.bottom-0             -> bottom: 0;
.left-0               -> left: 0;

/* Common offsets (Tailwind spacing scale) */
.top-1                -> top: 0.25rem;        /* 4px */
.top-2                -> top: 0.5rem;         /* 8px */
.top-4                -> top: 1rem;           /* 16px */
.top-8                -> top: 2rem;           /* 32px */

.z-0                  -> z-index: 0;
.z-10                 -> z-index: 10;
.z-20                 -> z-index: 20;
.z-30                 -> z-index: 30;
.z-40                 -> z-index: 40;
.z-50                 -> z-index: 50;
.z-auto               -> z-index: auto;
```

**Spacing (Margin & Padding)**

Tailwind spacing scale (selected):
`0 -> 0`, `px -> 1px`, `0.5 -> 0.125rem`, `1 -> 0.25rem`, `1.5 -> 0.375rem`, `2 -> 0.5rem`, `2.5 -> 0.625rem`, `3 -> 0.75rem`, `3.5 -> 0.875rem`, `4 -> 1rem`, `5 -> 1.25rem`, `6 -> 1.5rem`, `8 -> 2rem`, `10 -> 2.5rem`, `12 -> 3rem`, `16 -> 4rem`, `20 -> 5rem`, `24 -> 6rem`, `32 -> 8rem`.

```css
/* Margin (all sides) */
.m-0                  -> margin: 0;
.m-px                 -> margin: 1px;
.m-4                  -> margin: 1rem;

/* Margin (axis) */
.mx-4                 -> margin-left: 1rem; margin-right: 1rem;
.my-2                 -> margin-top: 0.5rem; margin-bottom: 0.5rem;

/* Margin (sides) */
.mt-6                 -> margin-top: 1.5rem;
.mr-3                 -> margin-right: 0.75rem;
.mb-8                 -> margin-bottom: 2rem;
.ml-1.5               -> margin-left: 0.375rem;

/* Auto centering */
.mx-auto              -> margin-left: auto; margin-right: auto;

/* Negative margins */
.-mt-4                -> margin-top: -1rem;

/* Padding mirrors margin */
.p-4                  -> padding: 1rem;
.px-6                 -> padding-left: 1.5rem; padding-right: 1.5rem;
.py-2.5               -> padding-top: 0.625rem; padding-bottom: 0.625rem;
.pt-3                 -> padding-top: 0.75rem;
```

**Flexbox**

```css
.flex-row             -> flex-direction: row;
.flex-row-reverse     -> flex-direction: row-reverse;
.flex-col             -> flex-direction: column;
.flex-col-reverse     -> flex-direction: column-reverse;

.flex-wrap            -> flex-wrap: wrap;
.flex-nowrap          -> flex-wrap: nowrap;
.flex-wrap-reverse    -> flex-wrap: wrap-reverse;

.items-start          -> align-items: flex-start;
.items-center         -> align-items: center;
.items-end            -> align-items: flex-end;
.items-stretch        -> align-items: stretch;
.items-baseline       -> align-items: baseline;

.justify-start        -> justify-content: flex-start;
.justify-center       -> justify-content: center;
.justify-end          -> justify-content: flex-end;
.justify-between      -> justify-content: space-between;
.justify-around       -> justify-content: space-around;
.justify-evenly       -> justify-content: space-evenly;

.content-start        -> align-content: flex-start;
.content-center       -> align-content: center;
.content-end          -> align-content: flex-end;
.content-between      -> align-content: space-between;
.content-around       -> align-content: space-around;
.content-evenly       -> align-content: space-evenly;

.gap-0                -> gap: 0;
.gap-2                -> gap: 0.5rem;
.gap-4                -> gap: 1rem;
.gap-x-6              -> column-gap: 1.5rem;
.gap-y-3              -> row-gap: 0.75rem;

.self-auto            -> align-self: auto;
.self-start           -> align-self: flex-start;
.self-center          -> align-self: center;
.self-end             -> align-self: flex-end;
.self-stretch         -> align-self: stretch;

.flex-1               -> flex: 1 1 0%;
.flex-auto            -> flex: 1 1 auto;
.flex-initial         -> flex: 0 1 auto;
.flex-none            -> flex: none;

.grow                 -> flex-grow: 1;
.grow-0               -> flex-grow: 0;
.shrink               -> flex-shrink: 1;
.shrink-0             -> flex-shrink: 0;

.order-first          -> order: -9999;
.order-last           -> order: 9999;
.order-none           -> order: 0;
.order-1              -> order: 1;
```

**Grid**

```css
.grid-cols-1          -> grid-template-columns: repeat(1, minmax(0, 1fr));
.grid-cols-2          -> grid-template-columns: repeat(2, minmax(0, 1fr));
.grid-cols-3          -> grid-template-columns: repeat(3, minmax(0, 1fr));
.grid-cols-12         -> grid-template-columns: repeat(12, minmax(0, 1fr));

.col-span-1           -> grid-column: span 1 / span 1;
.col-span-3           -> grid-column: span 3 / span 3;
.col-start-2          -> grid-column-start: 2;
.col-end-4            -> grid-column-end: 4;

.grid-rows-3          -> grid-template-rows: repeat(3, minmax(0, 1fr));
.row-span-2           -> grid-row: span 2 / span 2;

.gap-4                -> gap: 1rem;
.gap-x-8              -> column-gap: 2rem;
.gap-y-2              -> row-gap: 0.5rem;

.place-items-center   -> place-items: center;
.place-content-center -> place-content: center;
```

**Sizing**

```css
/* Width */
.w-0                  -> width: 0;
.w-px                 -> width: 1px;
.w-4                  -> width: 1rem;
.w-10                 -> width: 2.5rem;
.w-1/2                -> width: 50%;
.w-1/3                -> width: 33.333333%;
.w-2/3                -> width: 66.666667%;
.w-1/4                -> width: 25%;
.w-3/4                -> width: 75%;
.w-full               -> width: 100%;
.w-screen             -> width: 100vw;
.max-w-xs             -> max-width: 20rem;      /* 320px */
.max-w-sm             -> max-width: 24rem;      /* 384px */
.max-w-md             -> max-width: 28rem;      /* 448px */
.max-w-lg             -> max-width: 32rem;      /* 512px */
.max-w-xl             -> max-width: 36rem;      /* 576px */
.max-w-2xl            -> max-width: 42rem;      /* 672px */
.max-w-4xl            -> max-width: 56rem;      /* 896px */
.max-w-7xl            -> max-width: 80rem;      /* 1280px */

/* Height */
.h-0                  -> height: 0;
.h-px                 -> height: 1px;
.h-4                  -> height: 1rem;
.h-10                 -> height: 2.5rem;
.h-full               -> height: 100%;
.h-screen             -> height: 100vh;
.min-h-screen         -> min-height: 100vh;
```

**Typography**

Font sizes (default scale):

```css
.text-xs              -> font-size: 0.75rem; line-height: 1rem;
.text-sm              -> font-size: 0.875rem; line-height: 1.25rem;
.text-base            -> font-size: 1rem; line-height: 1.5rem;
.text-lg              -> font-size: 1.125rem; line-height: 1.75rem;
.text-xl              -> font-size: 1.25rem; line-height: 1.75rem;
.text-2xl             -> font-size: 1.5rem; line-height: 2rem;
.text-3xl             -> font-size: 1.875rem; line-height: 2.25rem;
.text-4xl             -> font-size: 2.25rem; line-height: 2.5rem;
.text-5xl             -> font-size: 3rem; line-height: 1;
.text-6xl             -> font-size: 3.75rem; line-height: 1;
```

Weights, style, alignment:

```css
.font-light           -> font-weight: 300;
.font-normal          -> font-weight: 400;
.font-medium          -> font-weight: 500;
.font-semibold        -> font-weight: 600;
.font-bold            -> font-weight: 700;

.italic               -> font-style: italic;
.not-italic           -> font-style: normal;

.text-left            -> text-align: left;
.text-center          -> text-align: center;
.text-right           -> text-align: right;
.text-justify         -> text-align: justify;

/* Line-height */
.leading-none         -> line-height: 1;
.leading-tight        -> line-height: 1.25;
.leading-snug         -> line-height: 1.375;
.leading-normal       -> line-height: 1.5;
.leading-relaxed      -> line-height: 1.625;
.leading-loose        -> line-height: 2;

/* Letter-spacing */
.tracking-tighter     -> letter-spacing: -0.05em;
.tracking-tight       -> letter-spacing: -0.025em;
.tracking-normal      -> letter-spacing: 0;
.tracking-wide        -> letter-spacing: 0.025em;
.tracking-wider       -> letter-spacing: 0.05em;
.tracking-widest      -> letter-spacing: 0.1em;
```

Colors (examples from default palette):

```css
.text-black           -> color: #000000;
.text-white           -> color: #ffffff;
.text-gray-500        -> color: #6b7280;
.text-slate-700       -> color: #334155;
.text-blue-500        -> color: #3b82f6;
.text-emerald-600     -> color: #059669;

.bg-white             -> background-color: #ffffff;
.bg-gray-100          -> background-color: #f3f4f6;
.bg-slate-900         -> background-color: #0f172a;
.bg-blue-600          -> background-color: #2563eb;
.bg-rose-500          -> background-color: #f43f5e;

.decoration-sky-500   -> text-decoration-color: #0ea5e9;
.underline            -> text-decoration-line: underline;
.no-underline         -> text-decoration-line: none;
```

**Borders & Radius**

```css
.border               -> border-width: 1px;
.border-0             -> border-width: 0;
.border-2             -> border-width: 2px;
.border-4             -> border-width: 4px;
.border-t             -> border-top-width: 1px;
.border-x-2           -> border-left-width: 2px; border-right-width: 2px;

.border-solid         -> border-style: solid;
.border-dashed        -> border-style: dashed;
.border-dotted        -> border-style: dotted;

.border-gray-200      -> border-color: #e5e7eb;
.border-slate-700     -> border-color: #334155;
.border-blue-500      -> border-color: #3b82f6;

/* Radius */
.rounded-none         -> border-radius: 0;
.rounded-sm           -> border-radius: 0.125rem;
.rounded              -> border-radius: 0.25rem;
.rounded-md           -> border-radius: 0.375rem;
.rounded-lg           -> border-radius: 0.5rem;
.rounded-xl           -> border-radius: 0.75rem;
.rounded-2xl          -> border-radius: 1rem;
.rounded-3xl          -> border-radius: 1.5rem;
.rounded-full         -> border-radius: 9999px;
.rounded-t-lg         -> border-top-left-radius: 0.5rem; border-top-right-radius: 0.5rem;
```

**Shadows, Rings, Filters**

```css
.shadow-sm            -> box-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05);
.shadow               -> box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
.shadow-md            -> box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
.shadow-lg            -> box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
.shadow-xl            -> box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);
.shadow-2xl           -> box-shadow: 0 25px 50px -12px rgb(0 0 0 / 0.25);
.shadow-inner         -> box-shadow: inset 0 2px 4px 0 rgb(0 0 0 / 0.05);
.shadow-none          -> box-shadow: 0 0 #0000;

/* Rings */
.ring                 -> box-shadow: 0 0 0 3px rgb(59 130 246 / 0.5);
.ring-2               -> box-shadow: 0 0 0 2px currentColor;
.ring-4               -> box-shadow: 0 0 0 4px currentColor;
.ring-offset-2        -> box-shadow: 0 0 0 2px #fff, 0 0 0 calc(2px + var(--tw-ring-offset-width, 0px)) currentColor;

/* Filters */
.blur-sm              -> filter: blur(4px);
.blur                 -> filter: blur(8px);
.blur-lg              -> filter: blur(16px);
.brightness-110       -> filter: brightness(1.1);
.contrast-125         -> filter: contrast(1.25);
.grayscale            -> filter: grayscale(100%);
.hue-rotate-60        -> filter: hue-rotate(60deg);
.saturate-150         -> filter: saturate(1.5);
.sepia                -> filter: sepia(100%);
```

**Transitions & Transforms**

```css
.transition           -> transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter, backdrop-filter; transition-duration: 150ms; transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
.transition-colors    -> transition-property: color, background-color, border-color, text-decoration-color, fill, stroke;
.transition-opacity   -> transition-property: opacity;
.duration-75          -> transition-duration: 75ms;
.duration-150         -> transition-duration: 150ms;
.duration-300         -> transition-duration: 300ms;
.ease-linear          -> transition-timing-function: linear;
.ease-in              -> transition-timing-function: cubic-bezier(0.4, 0, 1, 1);
.ease-out             -> transition-timing-function: cubic-bezier(0, 0, 0.2, 1);
.ease-in-out          -> transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);

.transform            -> transform: translate(var(--tw-translate-x,0), var(--tw-translate-y,0)) rotate(var(--tw-rotate,0)) skewX(var(--tw-skew-x,0)) skewY(var(--tw-skew-y,0)) scaleX(var(--tw-scale-x,1)) scaleY(var(--tw-scale-y,1));
.scale-50             -> --tw-scale-x: .5; --tw-scale-y: .5;
.scale-95             -> --tw-scale-x: .95; --tw-scale-y: .95;
.scale-100            -> --tw-scale-x: 1; --tw-scale-y: 1;
.rotate-45            -> --tw-rotate: 45deg;
.-rotate-12           -> --tw-rotate: -12deg;
.translate-x-4        -> --tw-translate-x: 1rem;
.-translate-y-1/2     -> --tw-translate-y: -50%;
```

**Opacity, Cursor, Pointer Events, Select**

```css
.opacity-0            -> opacity: 0;
.opacity-50           -> opacity: 0.5;
.opacity-100          -> opacity: 1;

.cursor-pointer       -> cursor: pointer;
.cursor-not-allowed   -> cursor: not-allowed;

.pointer-events-none  -> pointer-events: none;
.pointer-events-auto  -> pointer-events: auto;

.select-none          -> user-select: none;
.select-text          -> user-select: text;
```

**Object Fit & Backgrounds**

```css
.object-contain       -> object-fit: contain;
.object-cover         -> object-fit: cover;
.object-center        -> object-position: center;
.object-left          -> object-position: left;

.bg-fixed             -> background-attachment: fixed;
.bg-local             -> background-attachment: local;
.bg-scroll            -> background-attachment: scroll;

.bg-center            -> background-position: center;
.bg-top               -> background-position: top;
.bg-bottom            -> background-position: bottom;

.bg-no-repeat         -> background-repeat: no-repeat;
.bg-repeat-x          -> background-repeat: repeat-x;

.bg-contain           -> background-size: contain;
.bg-cover             -> background-size: cover;

.bg-gradient-to-r     -> background-image: linear-gradient(to right, var(--tw-gradient-stops));
.from-blue-500        -> --tw-gradient-from: #3b82f6;
.via-purple-500       -> --tw-gradient-via: #a855f7;
.to-pink-500          -> --tw-gradient-to: #ec4899;
```

**Lists, Tables, Misc**

```css
.list-disc            -> list-style-type: disc;
.list-decimal         -> list-style-type: decimal;
.list-none            -> list-style-type: none;

.table                -> display: table;
.table-fixed          -> table-layout: fixed;
.border-collapse      -> border-collapse: collapse;
.border-separate      -> border-collapse: separate;

.align-top            -> vertical-align: top;
.align-middle         -> vertical-align: middle;
.align-bottom         -> vertical-align: bottom;

.shadow-none          -> box-shadow: 0 0 #0000;
.outline-none         -> outline: 2px solid transparent; outline-offset: 2px;
```

**Responsive & State Variants**

Responsive prefixes generate media queries using default breakpoints:

```css
/* Example: md:text-lg */
@media (min-width: 768px) {
  .md\:text-lg {
    font-size: 1.125rem;
    line-height: 1.75rem;
  }
}
```

State variants:

```css
/* Example: hover:bg-blue-600 */
.button:hover {
  background-color: #2563eb;
}

/* Example: focus:ring */
.input:focus {
  box-shadow: 0 0 0 3px rgb(59 130 246 / 0.5);
}
```

**Quick Recipes**

```css
/* Center a div */
.flex.items-center.justify-center -> display: flex; align-items: center; justify-content: center;

/* Card */
.rounded-lg.shadow-md.p-6.bg-white -> border-radius: 0.5rem; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1); padding: 1.5rem; background-color: #fff;

/* Responsive grid */
.grid.grid-cols-1.md\:grid-cols-2.lg\:grid-cols-3.gap-6 ->
  display: grid;
  grid-template-columns: repeat(1, minmax(0, 1fr));
  gap: 1.5rem;
  @media (min-width: 768px) { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  @media (min-width: 1024px) { grid-template-columns: repeat(3, minmax(0, 1fr)); }
```

[Reference](https://www.webdevultra.com/articles/tailwindcss-cheatsheet-css-equivalents)

## 3. Design System Rules

**CRITICAL**: No inline styles. Always use TailwindCSS with design tokens.

### 1. Colors

Use design tokens defined in `src/styles/colors.css` (e.g. `text-primary`, `bg-primary-500`, `border-neutral-100`) instead of raw hex values or default Tailwind palette colors. Tokens map to CSS variables, so a theme/brand color change updates every usage in one place.

```tsx
// ❌ WRONG — magic value, breaks on rebrand, no dark-mode support
<div style={{ color: '#C77DFF' }}>Hello</div>
<div className="text-purple-400">Hello</div>

// ✅ CORRECT — references the design token
<div className="text-primary">Hello</div>
<div className="bg-primary-500 border border-neutral-100">Card</div>
```

### 2. Typography

Use the type scale defined in `src/styles/fontSize.css` (e.g. `text-display-lg`, `text-heading-md`, `text-body-sm`) instead of raw Tailwind size utilities like `text-2xl`. The scale encodes both font size and line-height for each role, keeping headings/body text consistent across the app.

```tsx
// ❌ WRONG — arbitrary size, no semantic meaning
<h1 className="text-3xl font-bold">Page Title</h1>

// ✅ CORRECT — semantic, consistent across the app
<h1 className="text-heading-md font-bold">Page Title</h1>
<p className="text-body-sm">Supporting text</p>
```

### 3. Conditional classes

Use `twMerge` (from `tailwind-merge`), optionally paired with `clsx`, instead of string concatenation or template literals. Plain concatenation can produce duplicate/conflicting utilities (e.g. two `p-*` classes) where the last one wins by source order, not by intent — `twMerge` resolves conflicts deterministically by utility group.

```tsx
// ❌ WRONG — string concatenation, conflicting classes silently overridden
const className = `p-4 ${isActive ? 'p-6 bg-primary' : ''}`;

// ✅ CORRECT
import { twMerge } from 'tailwind-merge';

const className = twMerge('p-4', isActive && 'p-6 bg-primary');
```

### 4. Spacing

Use `gap-*` on a flex/grid parent to space sibling elements; reserve `padding-*` for space *inside* a component's own boundary (between its border and its content). Mixing margins on children for layout spacing makes spacing depend on sibling order and breaks when items are reordered or wrapped.

```tsx
// ❌ WRONG — margin-based spacing, fragile when children are reordered
<div className="flex">
  <Card className="mr-4" />
  <Card className="mr-4" />
  <Card />
</div>

// ✅ CORRECT — gap for sibling spacing, padding for internal spacing
<div className="flex gap-4">
  <Card className="p-4" />
  <Card className="p-4" />
  <Card className="p-4" />
</div>
```

### 5. Component variants

Use `class-variance-authority` (`cva`) or a similar variant map instead of chained ternaries/`if` blocks for components with multiple visual states (size, variant, state). This keeps every valid combination declarative and type-checked, rather than scattered across conditional expressions.

```tsx
// ❌ WRONG — nested ternaries, hard to extend or type-check
const className = `btn ${
  variant === 'primary' ? 'bg-primary text-white' : 'bg-neutral-10 text-primary'
} ${size === 'lg' ? 'px-6 py-3' : 'px-4 py-2'}`;

// ✅ CORRECT
import { cva } from 'class-variance-authority';

const button = cva('rounded-md font-medium', {
  variants: {
    variant: {
      primary: 'bg-primary text-white',
      secondary: 'bg-neutral-10 text-primary',
    },
    size: {
      sm: 'px-4 py-2',
      lg: 'px-6 py-3',
    },
  },
});
```

### 6. Avoid arbitrary values

Prefer scale utilities (`gap-4`, `text-heading-md`, `rounded-lg`) over arbitrary-value syntax (`gap-[17px]`, `text-[15px]`, `rounded-[3px]`). Arbitrary values bypass the design system's scale, making the UI inconsistent and harder to theme.

```tsx
// ❌ WRONG — one-off value not on the spacing/type scale
<div className="gap-[17px] text-[15px]">

// ✅ CORRECT — pulled from the existing scale/tokens
<div className="gap-4 text-body-sm">
```

### 7. Don't use `@apply` to recreate components

Don't reach for `@apply` to bundle utility classes into a custom CSS class for a component — that reintroduces the maintenance cost utility-first CSS is meant to avoid (a second place to look for styles, naming to invent, no co-location with markup). Build a reusable React component instead, and keep the utilities in JSX.

```css
/* ❌ WRONG — re-creates a "Card" component in CSS */
.card {
  @apply rounded-lg shadow-md p-6 bg-white;
}
```

```tsx
// ✅ CORRECT — reusable component, utilities stay in markup
function Card({ children }: { children: React.ReactNode }) {
  return <div className="rounded-lg shadow-md p-6 bg-white">{children}</div>;
}
```
