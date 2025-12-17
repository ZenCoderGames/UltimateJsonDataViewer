# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Ultimate JSON Navigator is a single-page web application for visualizing and exploring multiple JSON files with intelligent reference detection and navigation. The entire application is contained in `index.html` as a self-contained HTML/CSS/JavaScript file with no external dependencies.

## Architecture

### Single-File Structure
- **index.html**: Complete application including HTML structure, embedded CSS styles, and JavaScript logic
- No build process, package manager, or external dependencies
- Open directly in a browser to run the application

### Core JavaScript Class: JSONDataViewer

The `JSONDataViewer` class (index.html:432-1036) is the main application controller with these key responsibilities:

1. **Data Processing Pipeline**:
   - `handleFileSelect()`: Loads JSON files from user-selected folder
   - `processData()`: Orchestrates flattening and reference detection
   - `flattenJSON()`: Traverses JSON structures to create individual entries from array items
   - `detectReferences()`: Identifies cross-references between items based on ID matching

2. **Reference System**:
   - Builds a map of all item IDs across all loaded files
   - Scans all string values to detect references to known IDs (case-insensitive)
   - Tracks reference count and source locations
   - Enables click-to-navigate functionality for referenced IDs

3. **Search Capabilities**:
   - **Global Search** (index.html:582): Searches across ALL files, overrides file filter
   - **Local Search** (index.html:597): Filters currently visible results
   - **File Filter**: Restricts view to a single JSON file
   - Auto-navigation to ID definitions when searching

4. **Data Model**:
   - `jsonData`: Raw JSON data keyed by filename
   - `flattenedData`: Array of entry objects, each representing one item from a JSON array
   - `references`: Map of ID strings to arrays of source locations
   - Entry structure: `{id, filename, title, data, parentPath}`

### Entry Creation Logic (index.html:511-544)

The flattening algorithm creates entries only for objects within arrays:
- Traverses the JSON tree to find arrays
- Creates one entry per object in each array
- Uses `id`, `name`, or auto-generated identifier for entry ID
- Preserves full object data including nested arrays/objects as fields
- Does NOT create separate entries for nested arrays within entry objects

### UI Rendering

- **Expandable Values** (index.html:765-878): Arrays and objects show as collapsed summaries, click to expand
- **Clickable References** (index.html:853-869): Values matching known IDs become clickable links with reference counts
- **Search Highlighting** (index.html:955-978): Active search terms highlighted in green
- **Smart Navigation** (index.html:980-1011): When clicking a reference, performs global search if target not visible

## Development Workflow

Since this is a static HTML file with no build system:

1. **Running**: Open `index.html` directly in a web browser
2. **Debugging**: Set `DEBUG = true` at index.html:417 to enable debug logging
3. **Testing**: Use browser DevTools, manual testing with sample JSON files
4. **Version Control**: This is a Git repository, use standard Git commands

## Key Implementation Details

### Reference Detection Algorithm
- IDs are normalized to lowercase for case-insensitive matching
- Only string values are checked against the ID set
- References are tracked bidirectionally (both source and target)
- Reference count includes all occurrences across all files

### Search Behavior
- Global search clears file filter to search everything
- Local search filters current view without changing data source
- Searches are recursive through nested structures
- Auto-scrolls to ID definition when exact match found

### Value Rendering
Values are rendered with context-aware formatting:
- Primitives: Plain text or clickable if matching an ID
- Arrays/Objects: Collapsed by default with expand/collapse toggle
- References: Blue underlined text with reference count badge
- Search matches: Green highlighting on matching terms
