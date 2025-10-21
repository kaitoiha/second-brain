# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Zettelkasten information management system** - a knowledge management application inspired by Notion and Obsidian. It implements the Zettelkasten method (slip-box) for personal and team knowledge management with Markdown support.

The system facilitates the transformation of fleeting thoughts into structured permanent notes, visualizes connections between notes, and supports lightweight Markdown-based note management.

## Key Concepts

The application manages three distinct note types that follow the Zettelkasten methodology:

1. **Fleeting Notes (走り書きメモ)**: Temporary ideas and quick thoughts that can be marked as "done" to automatically generate permanent notes
2. **Literature Notes (文献メモ)**: References, citations, and summaries from external sources (books, articles, web) with optional URLs
3. **Permanent Notes (永久保存メモ)**: Structured knowledge notes that are atomically organized (one concept per note) and can link to each other and literature notes

When a fleeting or literature note is marked as "done", a permanent note is automatically created and the original is archived (not deleted).

## Tech Stack

- **Next.js 15.5.6** with App Router
- **React 19.1.0**
- **TypeScript 5**
- **Tailwind CSS 4** (using new PostCSS architecture)
- **Planned**: Prisma + Supabase (PostgreSQL), Auth.js v5, react-markdown, D3.js for graph view

## Development Commands

```bash
# Start development server
npm run dev

# Build for production
npm run build

# Start production server
npm run start

# Run linter
npm run lint
```

## Project Architecture

### Database Design (To Be Implemented)

The data model follows this hierarchy:

```
User (1:N)
├─ FleetingNote (isDone flag → auto-generates PermanentNote)
├─ LiteratureNote (isDone flag → auto-generates PermanentNote)
└─ PermanentNote
   ├─ N:M relationship with Category (flat structure, no hierarchy)
   ├─ N:M relationship with LiteratureNote (for citations)
   └─ N:M self-referencing (bidirectional links between permanent notes)
```

Key tables to implement:
- Authentication: `users`, `accounts`, `sessions`, `verification_tokens`
- Notes: `fleeting_notes`, `literature_notes`, `permanent_notes`, `permanent_note_links`
- Organization: `categories`, junction tables for relationships

### Important Note Fields

- **PermanentNote**: `sourceType` and `sourceId` track origin (fleeting/literature/manual)
- **PermanentNote**: `showInGraph` boolean controls visibility in graph view
- **FleetingNote/LiteratureNote**: `isDone` flag triggers automatic permanent note creation

### Markdown Link Syntax

The app will use `[[Note Title]]` syntax (Obsidian/Roam-style) for internal note linking. Links need to be parsed and automatically converted to database relationships for backlink tracking.

### Graph View Implementation

Uses D3.js to visualize permanent notes and their connections. Nodes represent notes, edges represent bidirectional links. Clicking a node opens the note in a modal. The graph supports filtering by category and note type.

## UI/UX Architecture

### Layout Structure
- **Sidebar** (LINK section): Shows categories, literature notes with `isDone=true`, permanent notes list
- **Main Area**: Three-column layout (or tabs on mobile) showing fleeting/literature/permanent notes
- **Modal System**: Universal modal for viewing/editing all note types with preview and edit modes
- **Graph View**: Full-screen D3.js visualization with filtering controls

### Modal Behavior
- Opens from: note cards, graph nodes, category pages
- Two modes: preview (read-only) and edit (with real-time markdown preview)
- Always shows: title, categories, markdown content, timestamps, backlinks section at bottom

## Implementation Phases

### Phase 1 (Current MVP Target)
- Authentication system (email/password + Google OAuth)
- CRUD operations for all three note types
- "Done" checkbox → permanent note auto-generation
- Category system with many-to-many relationships
- Modal system with preview/edit modes
- Basic UI layout (sidebar, main area)
- Static search UI (no backend yet)

### Phase 2
- `[[Link]]` syntax parsing and backlink detection
- D3.js graph view with basic interactions
- Full-text search implementation (Supabase FTS or Fuse.js)

### Phase 3
- Graph view filters (by category, note type)
- Team features (workspaces, invitations)
- Export functionality, keyboard shortcuts, dark mode

## Path Alias

Use `@/*` for imports from the `src` directory as configured in `tsconfig.json`:

```typescript
import Component from '@/components/Component'
import { helper } from '@/lib/utils'
```

## Code Style Notes

- This is a Japanese-language application - UI text and documentation will be in Japanese
- The codebase follows strict TypeScript with Next.js 15 App Router conventions
- Uses ESLint with Next.js core-web-vitals and TypeScript presets
- Tailwind CSS v4 with new PostCSS architecture (note the `@tailwindcss/postcss` dependency)
