# WriteDocs — React Implementation Scheme & Architecture Overview
This document outlines the  blueprint, state management flow, component breakdown, and API integration strategy for the single-file **WriteDocs** React studio application.

---

## 1. System Architecture Diagram

```
+-------------------------------------------------------------------------------+
|                                App Component                                  |
|  - Theme Context (Dark/Light)                                                 |
|  - Active Workspace & History State Management                                 |
|  - Gemini 2.5 AI Service Integration                                          |
+-------------------------------------------------------------------------------+
       |                                       |                                |
       v                                       v                                v
+-----------------------+   +------------------------------------+   +---------------------------+
|      Top Navbar       |   |      Left Sidebar (Workspace)      |   |    Main Split Workspace   |
| - Theme Toggle        |   | - Document Search & Filter Tag     |   | +-----------------------+ |
| - Gemini API Key Input|   | - Workspace History Cards          |   | | Code Editor & Controls| |
| - Trigger Generator   |   | - New Workspace Creation           |   | | - Preset Loader       | |
+-----------------------+   +------------------------------------+   | | - Style/Audience Matrix| |
                                                                     | | - Output Toggles      | |
                                                                     | | - Code Area & Upload  | |
                                                                     | +-----------------------+ |
                                                                     | +-----------------------+ |
                                                                     | | Live Preview Panel    | |
                                                                     | | - Tab Switcher        | |
                                                                     | | - Markdown Renderer   | |
                                                                     | | - Mermaid Diagram     | |
                                                                     | | - Line-by-Line Breakdown|
                                                                     | | - Quality Score Audit | |
                                                                     | | - Raw Code View       | |
                                                                     | +-----------------------+ |
                                                                     +---------------------------+
```

---

## 2. Component Structure Breakdown

The application is structured into modular, functional sections inside a single component scope:

### Core Top-Level Components & Layouts
1. **Header Navigation Bar (`<header>`)**
   - Brand logo & engine badge (`Gemini 2.5 AI`).
   - Secure input field for optional Gemini API Key (`apiKey` state).
   - Theme toggle switcher (Light/Dark mode).
   - Primary `Generate Docs` trigger button with active loading state.

2. **Left Workspace Drawer / History Sidebar (`<aside>`)**
   - Collapsible panel toggled via header icon.
   - Live search input filter (`searchTerm`).
   - Category tag filter pills (`#API`, `#Backend`, `#Frontend`, etc.).
   - Interactive document history list with deletion actions and dynamic active-state highlight.

3. **Split Workspace Editor & Live Viewer (`<main>`)**
   - **Left Pane (Code Input & Configuration):**
     - Dynamic title input field.
     - Preset selector grid (`SAMPLE_PRESETS`).
     - Audience & style customization dropdown matrix.
     - Toggle pills for extra generation modules (Flowcharts, Edge cases, Type schemas).
     - File upload trigger (`.js`, `.ts`, `.py`, `.rs`, `.go`, etc.).
     - Code input textarea with mono-font formatting.
   - **Right Pane (Multi-Tab Output Preview):**
     - Interactive tab bar (`docs`, `diagram`, `explain`, `quality`, `raw`).
     - Tab content renderer:
       - **Docs Tab:** Formatted text output with executive summary and usage examples.
       - **Diagram Tab:** Rendered Mermaid.js flow block diagram.
       - **Explain Tab:** Line-by-line code explanation cards.
       - **Quality Audit Tab:** Document score breakdown (Clarity, Completeness, Examples) and improvement suggestions.
       - **Raw Tab:** Raw markdown display with copy and export functionality.

---

## 3. State Management Schema

| State Key | Type | Default Value | Description |
| :--- | :--- | :--- | :--- |
| `theme` | `string` | `'dark'` | Color scheme mode (`'dark'` \| `'light'`) |
| `sidebarOpen` | `boolean` | `true` | Left workspace navigation visibility |
| `activeTab` | `string` | `'docs'` | Selected output tab view (`'docs'`, `'diagram'`, `'explain'`, `'quality'`, `'raw'`) |
| `code` | `string` | *Express Sample* | Raw code snippet string input by user |
| `language` | `string` | `'javascript'` | Target programming language identifier |
| `docTitle` | `string` | *Title string* | Name of the current active documentation |
| `presetStyle` | `string` | `'API Reference'` | Documentation format style preset |
| `targetAudience`| `string` | `'Senior Engineer'` | Reader expertise target level |
| `detailLevel` | `string` | `'Detailed'` | Output granularity (`'Concise'`, `'Detailed'`, `'Step-by-Step'`) |
| `toggles` | `object` | `{ diagrams: true, ... }` | Feature flags for output generation components |
| `apiKey` | `string` | `''` | User's Google Gemini API Key |
| `isGenerating` | `boolean` | `false` | Asynchronous generation status state |
| `generatedDoc` | `string` | `''` | Markdown response text |
| `mermaidCode` | `string` | `''` | Mermaid.js diagram source string |
| `explainedLines`| `Array` | `[]` | Array of objects `{ line, explanation }` |
| `qualityReport` | `Object` | `null` | Audit score metrics and recommendations |
| `history` | `Array` | `[...]` | Persistence list of generated documentation objects |
| `activeDocId` | `string` | `'doc-1'` | ID of currently loaded document item |

---

## 4. API Processing & Execution Flow

```
[ User Action: Click "Generate Docs" ]
                 |
                 v
   Is API Key provided in state?
       /                   \
      YES                   NO
     /                       \
[ Call Gemini API ]      [ Execute Mock Engine ]
  Model: gemini-2.5-flash   Simulates AI response with
  Payload: Code + Settings   preset output objects
     \                       /
      v                     v
  Parse Output JSON Structure:
  {
    markdown: string,
    mermaid: string,
    lineByLine: Array,
    qualityScore: Object
  }
                 |
                 v
  Update React States & Prepend to History Array
                 |
                 v
  Rerender Active View & Auto-Switch Output Panels
```

---

## 5. Export & Storage Features

- **Copy to Clipboard:** One-click utility copying active raw Markdown output using `navigator.clipboard.writeText`.
- **Markdown File Export:** Generates a dynamic `.md` blob and triggers a browser download styled as `${docTitle}-docs.md`.
- **Local Workspace Persistence:** Documents remain stored in state history during session lifetime for quick switching.
