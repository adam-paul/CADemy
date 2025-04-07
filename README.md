**Project:** CADemy - Incept Layer 2 - Gamified Educational UI (MVP)

**0. Install and Run**

For now, this is a simple frontend MVP using three.js. Just download source, do a `bun install`, 
then `bun run dev` and fool around with it in your browser.

**1. Project Goal & Background:**

- **Overall Objective:** Develop a unified, gamified user interface (UI) layer that serves structured educational content generated by the "Incept" project. The goal is a personalized, character-driven game world where learning takes place[cite: 1]. This layer aims to make learning engaging and fun, not just "easy"[cite: 9].
- **Core Concept:** Create an interactive game world where progress through educational content is visualized and incentivized through game mechanics. The initial focus is on building a Minimum Viable Product (MVP) for rapid iteration and feedback[cite: 10].
- **Inspiration:** Draws inspiration from platforms like Roblox for engagement [cite: 3] and concepts from Duolingo/Anki for progress mechanics[cite: 3], but aims to integrate deeper learning science and avoid common pitfalls of "edutainment"[cite: 8].
- **Key Principle:** Gamification must serve genuine engagement, challenge, progression, inspiration, novelty, and self-expression, not just addictive loops[cite: 4, 5, 6, 7]. AI content generation from Incept should be presented opaquely to the user[cite: 7].

**2. MVP Scope & Features:**

- **Core Feature:** A 2D, RPG-like overworld map interface accessible via web/mobile browsers.
- **Multiple Maps:** Implement 4 distinct overworld maps, one for each subject: Math, Science, History, and Language.
  - Users should be able to switch between these maps (e.g., via tabs in the UI).
  - Each map should have unique thematic styling (backgrounds, path visuals, node appearance) appropriate to the subject.
- **Map Structure:** Each map consists of nodes connected by defined paths.
  - **Nodes:** Represent specific lessons, mini-games, or educational activities (content sourced from Incept later). They are _not_ broad subject categories.
  - **Paths:** Define valid movement connections between nodes.
- **Player Character:**
  - Represented by a basic geometric shape in the MVP.
  - Code structure must be modular to allow easy replacement with a sprite/avatar later.
- **Navigation:**
  - Player moves from node to adjacent node along defined paths.
  - **Desktop:** Use Arrow Keys (Up, Down, Left, Right) to move to the connected node most aligned with that direction.
  - **Mobile:** Tap on a connected, adjacent node to move the player character there automatically.
- **Node Interaction (MVP):** When the player character arrives at a node, display the name of the associated lesson/game (e.g., via a simple text popup). No actual lesson content loading in MVP.
- **Data Source (MVP):** All map data (node positions, connections, names, theme info) will be hardcoded in a structured JSON format within the frontend application. No backend interaction for the MVP.

**3. Tech Stack & Implementation:**

- **Runtime/Package Manager:** Bun
- **Frontend Framework:** SvelteKit with TypeScript (using the "minimal" template).
- **Graphics Rendering:** Three.js for rendering the 2D maps, nodes, paths, and player character within a Svelte component.
  - Standard Svelte lifecycle functions (`onMount`, `onDestroy`) will manage Three.js setup and cleanup.
  - Use an Orthographic camera for the 2D view.
- **State Management (MVP):** Simple component props and reactive Svelte variables (`let`, `$:`) initially. Consider Svelte Stores if complexity increases.
- **Data Structure:** Define TypeScript interfaces for `LessonNodeData` and `SubjectMapData`. Store initial map data in a `.ts` or `.json` file within `src/lib`.
- **Type Organization:** Shared TypeScript interfaces/types should be defined in dedicated files within `src/lib` (e.g., `src/lib/types.ts` or `src/lib/types/mapTypes.ts`) for maintainability and reusability.
- **Styling:** Standard CSS/SCSS within Svelte components, potentially using scoped styles. Thematic map backgrounds/icons are required, though initial placeholders are acceptable.
- **Code Principles:** Emphasize minimal, maintainable, reusable, and extensible code. Minimize tech debt. Use clear component separation (e.g., `MapCanvas`, potentially `Player`, `Node` representations managed within the canvas).

**4. Future Considerations (Beyond MVP):**

- **Backend:** Plan for a Node.js backend with a PostgreSQL database.
- **Persistence:** Backend will handle saving user progress, character state, etc.
- **Incept Integration:** Backend will manage API calls to Incept to fetch lesson/game content corresponding to map nodes.
- **Player Avatar:** Replace the basic shape with customizable sprites/avatars[cite: 7].
- **Enhanced Interactions:** Implement actual loading of educational content when interacting with nodes.
- **Social Features:** Potentially add leaderboards, multiplayer aspects later[cite: 6, 7].

---
