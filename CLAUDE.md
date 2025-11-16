# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static HTML/CSS/JavaScript web application for managing school exercises. The main application is a single-page French school exercise manager (gestionnaire-documents.html) that allows students to create exercise folders, add questions with images, and provide answers with drawing annotations.

## Development Commands

**Running the application:**
- Simply open `gestionnaire-documents.html` or `index.html` in a web browser
- No build process, compilation, or server required
- For development, you can use any local HTTP server if needed:
  - Python: `python -m http.server 8000`
  - Node.js: `npx http-server`

**Testing:**
- Manual testing only - open files in browser
- Test in multiple browsers (Chrome, Firefox, Safari) for compatibility
- Test localStorage persistence by closing and reopening the application

## Code Architecture

### Main Application (gestionnaire-documents.html)

This is a **self-contained single-page application** with all HTML, CSS, and JavaScript in one file.

**Data Structure:**
```javascript
donnees = {
    classeurs: [  // Exercise folders/binders
        { id, nom, dateCreation }
    ],
    exercices: [  // Exercises within folders
        {
            id,
            classeurId,
            titre,
            questions: [
                {
                    id,
                    texte,
                    image,      // Base64 encoded image
                    reponse,    // Text answer
                    dessin      // Base64 encoded canvas drawing overlay
                }
            ],
            dateCreation,
            dateModification
        }
    ]
}
```

**Key Features:**

1. **localStorage Persistence**: All data stored in localStorage under key 'gestionnaireExercices'
   - Auto-saves responses with 500ms debounce
   - Load with `chargerDonnees()`, save with `sauvegarderDonnees()`

2. **Canvas Drawing System**: Questions with images get interactive canvas overlays
   - Drawing tools: color picker, brush sizes, eraser
   - Touch and mouse support for drawing
   - Drawings saved as Base64 PNG in `question.dessin`
   - Canvas positioned absolutely over image for annotation

3. **Modal System**: Three modals control the workflow
   - `editModal`: Create/edit exercises and manage questions
   - `questionModal`: Add/edit individual questions
   - `viewModal`: View and answer exercises

4. **State Management**: Global variables track current context
   - `classeurActif`: Currently selected folder
   - `exerciceEnEdition`: Exercise being edited (ID or null)
   - `exerciceEnConsultation`: Exercise being viewed (ID or null)
   - `questionsTemporaires`: Questions array during editing (not yet saved)

**Important Implementation Details:**

- Image handling: Images converted to Base64 via FileReader for localStorage storage
- Canvas coordinates: Must account for `canvas.getBoundingClientRect()` to convert mouse/touch positions to canvas coordinates
- Modal closing: Click outside modal (backdrop) closes it via event listener checking `e.target === modal`
- Demo data: If no localStorage data exists, creates 3 default classeurs (Mathématiques, Français, Sciences)

### Secondary Files

**index.html**: Simple static page with a form about animals (example: "La tortue")
- References non-existent document1-5.html files (placeholder links)
- Uses external styles.css and Luciole font

**styles.css**: Styling for index.html
- Imports Luciole-Regular font from fonts/ directory
- Sets base font-size to 62.5% for rem calculations (1.6rem = 16px)

**fonts/**: Custom Luciole font family (accessibility-focused typeface)
- Contains Bold, BoldItalic, Italic, Regular variants
- Imported via Luciole-Regular.css

## Common Patterns

**Adding new features to gestionnaire-documents.html:**
1. Add UI elements in the appropriate modal or section
2. Add global state variables if needed (before init())
3. Implement handler functions
4. Update `afficherQuestionsEdit()` or `afficherExercices()` to display new data
5. Ensure `sauvegarderDonnees()` is called to persist changes

**Working with canvas drawings:**
- Canvas must match image dimensions exactly for proper overlay
- Store drawing parameters in `canvas._drawingParams` for access in event handlers
- Use `canvas.toDataURL('image/png')` to save drawings
- Load saved drawings by creating new Image() and drawing to context

## Browser Compatibility

- Requires modern browser with HTML5 Canvas support
- localStorage must be enabled
- FileReader API required for image uploads
- Touch events for mobile drawing support
