# Bingo Mixer - Agent Instructions

## Mandatory Development Checklist

- [ ] `npm run lint`
- [ ] `npm run build`
- [ ] `npm test`

Run all three after changes. Fix all errors before finishing.

Social icebreaker bingo game built with React 19, TypeScript, Vite 8, and Tailwind CSS v4.
Players get a randomized 5x5 board and find real matches to complete 5-in-a-row.

## Commands

```bash
npm run dev    # dev server -> http://localhost:5173
npm run build  # tsc -b && vite build
npm run lint   # ESLint
npm test       # vitest run (41 tests, jsdom env)
```

## Architecture

| Path | Role |
|---|---|
| `src/types/index.ts` | Shared domain types (BingoSquareData, BingoLine, GameState) |
| `src/utils/bingoLogic.ts` | Pure functions: board gen, toggle, bingo check - unit-testable, no React |
| `src/hooks/useBingoGame.ts` | All game state (localStorage persistence, STORAGE_KEY/STORAGE_VERSION) |
| `src/data/questions.ts` | Array of 24 prompt strings + FREE_SPACE constant |
| `src/components/` | React components - consume hook, never hold game logic |
| `src/App.tsx` | Root: routes between start / playing / bingo states |

Data flow: `questions.ts` -> `bingoLogic.ts` -> `useBingoGame.ts` -> components.

## Key Conventions

- **Game logic:** `src/utils/bingoLogic.ts` (pure, immutable, no side effects).
- **State:** `useBingoGame.ts` (localStorage with `STORAGE_VERSION`; bump when shape changes).
- Board is a flat `BingoSquareData[]` of length 25; index 12 is always the FREE_SPACE (pre-marked).
- Square IDs are stable positional indices (0-24). Use `id` for all lookups, not array position.
- `BingoLine.type` is `'row' | 'column' | 'diagonal' | 'corners'`.
- Tailwind CSS v4 uses CSS-only config via `@theme` in `src/index.css` (no `tailwind.config.js`). See [.github/instructions/tailwind-4.instructions.md](.github/instructions/tailwind-4.instructions.md).
- Use color tokens `accent`, `accent-light`, `marked`, `marked-border`, `bingo` (no hardcoded hex).
- Component styling: inline `className` strings with Tailwind utility classes; no separate CSS files per component.

## Testing

- Tests live beside source (`src/utils/bingoLogic.test.ts`).
- Setup file: `src/test/setup.ts` (extends @testing-library/jest-dom matchers, calls cleanup() after each test)
- Vitest globals are enabled (`describe`, `it`, `expect` need no imports).
- Test pure logic in `bingoLogic.ts`; use `@testing-library/react` for component tests

## Adding Questions

Edit `src/data/questions.ts`. The pool must have at least 24 entries (board draws exactly 24 non-free squares).

## Existing Agents & Prompts

Custom agents are in [`.github/agents/`](.github/agents/):
- **Quiz Master** - generates themed bingo questions and updates `questions.ts`
- **TDD Supervisor** - orchestrates Red -> Green -> Refactor cycle via sub-agents
- **Pixel Jam**, **UI Review** - frontend design and review agents

Frontend design conventions: [.github/instructions/frontend-design.instructions.md](.github/instructions/frontend-design.instructions.md)
