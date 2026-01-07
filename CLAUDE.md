# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a React-based case study showcase application built with Vite, TypeScript, and shadcn/ui. The app presents business case studies (TSMC, Uni-President, Foxconn) and learning reflections in a single-page application with client-side routing handled by state management instead of react-router.

## Development Commands

```bash
# Start development server (runs on port 8080)
npm run dev

# Build for production
npm run build

# Build in development mode (with component tagger)
npm run build:dev

# Lint code
npm run lint

# Preview production build
npm run preview
```

## Architecture

### Routing Pattern

This app uses an unconventional routing pattern:
- **react-router-dom** is installed but only used for the catch-all 404 page
- **Main navigation** is handled through state in `src/pages/Index.tsx` using `currentPage` state
- When a card is clicked, `setCurrentPage(pageId)` triggers conditional rendering of `<SubPage>` component
- The back button calls `setCurrentPage(null)` to return to the home page
- This creates smooth transitions without URL changes

### Data-Driven Content

Content is centralized in `src/data/siteContent.ts`:
- `StudentInfo`: Student metadata (class, ID, name)
- `PageContent`: Defines each case study page with id, title, subtitle, themeColor, icon, isReflection flag
- Content components are mapped by pageId in `src/components/SubPage.tsx`

### Component Organization

```
src/components/
├── HeroSection.tsx          # Landing page hero with student info
├── NavigationCards.tsx      # Case study card grid
├── SubPage.tsx              # Container for individual case study pages
├── SubPageHeader.tsx        # Back button header for case studies
├── Footer.tsx               # Site footer
├── content/                 # Case study content components
│   ├── TSMCContent.tsx
│   ├── UniPresidentContent.tsx
│   ├── FoxconnContent.tsx
│   └── ReflectionContent.tsx
└── ui/                      # shadcn/ui components (49 components)
```

### Page Flow

1. `Index.tsx` renders `HeroSection` + `NavigationCards` + `Footer`
2. User clicks card → `onNavigate(pageId)` called
3. `currentPage` state updates, triggering conditional render
4. `SubPage` component displays with:
   - `SubPageHeader` (back button)
   - Dynamic content from `src/components/content/`
   - `Footer`
5. Back button → `onBack()` → `setCurrentPage(null)` → returns to home

### Color Theming

Each case study has a theme color defined in `SubPage.tsx` colorMap:
- TSMC: blue (`tsmc`, `tsmc-light`)
- Uni-President: orange (`uni`, `uni-light`)
- Foxconn: slate (`foxconn`, `foxconn-light`)
- Reflection: emerald (`reflection`, `reflection-light`)

These colors should be defined in `tailwind.config.ts`.

### Icon System

Icons use `lucide-react` with a mapping in `SubPage.tsx`:
- Cpu, Store, Factory, BookOpen
- New icons must be imported and added to `iconMap`

## Tech Stack Details

- **Build Tool**: Vite with React SWC plugin
- **UI Components**: shadcn/ui with Radix UI primitives
- **Styling**: Tailwind CSS with CSS variables for theming
- **State Management**: React Query for async state
- **Forms**: react-hook-form with zod validation (installed but not actively used)
- **Charts**: recharts (installed but not actively used)
- **Package Manager**: Both npm and bun lockfiles present

## Import Aliases

```typescript
@/* → ./src/*
```

All imports use the `@/` alias. When creating new files, follow this pattern.

## TypeScript Configuration

- `noImplicitAny: false` - implicit any is allowed
- `noUnusedParameters: false` - unused params won't error
- `noUnusedLocals: false` - unused variables won't error
- `strictNullChecks: false` - null checks are relaxed

This is a lenient TS config suitable for rapid development.

## Adding New Case Studies

1. Add entry to `siteContent.pages` array in `src/data/siteContent.ts`
2. Create content component in `src/components/content/YourContent.tsx`
3. Import and add to `contentMap` in `src/components/SubPage.tsx`
4. Add icon to `iconMap` if using new icon type
5. Add color theme to `colorMap` and define colors in `tailwind.config.ts`

## shadcn/ui Component Management

Components are managed via `components.json` configuration. To add new shadcn components:

```bash
npx shadcn@latest add [component-name]
```

All UI components are in `src/components/ui/` with the `@/components/ui` alias.
