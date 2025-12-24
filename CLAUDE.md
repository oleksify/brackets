# CLAUDE.md - Brackets Code Editor

This document provides essential information for AI assistants working with the Brackets codebase.

## Project Overview

Brackets is a modern open-source code editor for HTML, CSS, and JavaScript, built *in* HTML, CSS, and JavaScript. It runs as a desktop application using a native shell ([brackets-shell](https://github.com/adobe/brackets-shell/)) that provides local file system access.

**Note:** Adobe ended support for Brackets on September 1, 2021. This is a legacy/community-maintained project.

## Quick Reference

```bash
# Install dependencies and set up dev environment
npm install

# Run linting
npm run eslint
# or
grunt eslint

# Run tests
npm test
# or
grunt test

# Build for production
grunt build
```

## Repository Structure

```
brackets/
├── src/                    # Main source code
│   ├── brackets.js         # Main application entry point (defines window.brackets)
│   ├── main.js             # Bootstrap module (RequireJS configuration)
│   ├── command/            # Command system and keyboard shortcuts
│   ├── document/           # Document model and management
│   ├── editor/             # CodeMirror wrapper and editor functionality
│   ├── extensions/         # Extension system
│   │   ├── default/        # Built-in extensions (code hints, themes, etc.)
│   │   ├── dev/            # Development extensions
│   │   └── samples/        # Sample extensions for learning
│   ├── file/               # File utilities
│   ├── filesystem/         # File system abstraction layer
│   ├── htmlContent/        # HTML templates
│   ├── language/           # Language modes and language management
│   ├── languageTools/      # LSP (Language Server Protocol) support
│   ├── LiveDevelopment/    # Live Preview feature
│   ├── nls/                # Internationalization (35+ languages)
│   ├── preferences/        # User preferences system
│   ├── project/            # Project management and file tree
│   ├── search/             # Search and Quick Open features
│   ├── styles/             # LESS/CSS styles
│   ├── thirdparty/         # Third-party libraries (CodeMirror, jQuery, etc.)
│   ├── utils/              # Utility modules
│   ├── view/               # View management
│   └── widgets/            # UI widgets (dialogs, modals, bootstrap)
├── test/                   # Test suite
│   ├── spec/               # Jasmine unit tests
│   ├── perf/               # Performance tests
│   └── smokes/             # Smoke tests
├── samples/                # Sample projects (Getting Started, etc.)
├── tasks/                  # Custom Grunt tasks
└── tools/                  # Build and development tools
```

## Architecture

### Module System

Brackets uses **RequireJS** (AMD) for module loading:

```javascript
define(function (require, exports, module) {
    "use strict";

    var SomeModule = require("path/to/Module");

    // Module code...

    exports.someFunction = someFunction;
});
```

### Key Components

| Component | Location | Description |
|-----------|----------|-------------|
| **AppInit** | `src/utils/AppInit.js` | Application initialization and lifecycle |
| **CommandManager** | `src/command/CommandManager.js` | Command registration and execution |
| **DocumentManager** | `src/document/DocumentManager.js` | Document lifecycle management |
| **EditorManager** | `src/editor/EditorManager.js` | Editor instance management |
| **Editor** | `src/editor/Editor.js` | CodeMirror wrapper |
| **ProjectManager** | `src/project/ProjectManager.js` | Project and file tree management |
| **FileSystem** | `src/filesystem/FileSystem.js` | File system abstraction |
| **PreferencesManager** | `src/preferences/PreferencesManager.js` | User preferences |
| **ExtensionLoader** | `src/utils/ExtensionLoader.js` | Extension loading system |

### Event System

Brackets uses a custom `EventDispatcher` pattern:

```javascript
var EventDispatcher = require("utils/EventDispatcher");

// Add event dispatching to an object
EventDispatcher.makeEventDispatcher(exports);

// Dispatch events
exports.trigger("eventName", data);

// Listen for events
someModule.on("eventName", handler);
```

## Development Workflow

### Prerequisites

- Node.js 6.x (as specified in `.travis.yml`)
- npm
- Grunt CLI (`npm install -g grunt-cli`)

### Setup

```bash
git clone https://github.com/adobe/brackets.git
cd brackets
npm install
```

The `npm install` automatically runs `grunt install` which:
1. Writes dev config
2. Compiles LESS to CSS
3. Downloads default extensions
4. Installs source dependencies
5. Packs web dependencies

### Running Brackets

Brackets requires the native shell to run. You cannot simply open `index.html` in a browser.

### Testing

```bash
# Run all tests (eslint + jasmine + nls-check)
grunt test

# Run only linting
grunt eslint

# Run specific test categories
grunt eslint:src    # Source files only
grunt eslint:test   # Test files only
```

Tests use **Jasmine 1.3** framework. Test files are in `test/spec/` and follow the naming pattern `*-test.js`.

### Building

```bash
# Production build
grunt build

# Pre-release build
grunt build-prerelease
```

Build output goes to `dist/` directory.

## Code Style and Conventions

### ESLint Configuration

The project uses ESLint with these key rules (see `.eslintrc.js`):

- **Indentation:** 4 spaces
- **Max line length:** 120 characters
- **Strict mode:** Required (`"use strict";`)
- **Semicolons:** Required
- **Curly braces:** Required for all control statements
- **Equality:** Smart equality (`===` preferred, but `==` allowed for null checks)
- **No trailing spaces**
- **camelCase** naming convention

### File Header

All source files should include the MIT license header:

```javascript
/*
 * Copyright (c) 2012 - present Adobe Systems Incorporated. All rights reserved.
 *
 * Permission is hereby granted, free of charge, to any person obtaining a
 * copy of this software and associated documentation files (the "Software"),
 * ...
 */
```

### Module Pattern

```javascript
define(function (require, exports, module) {
    "use strict";

    // Dependencies at top
    var Dependency1 = require("path/Dependency1"),
        Dependency2 = require("path/Dependency2");

    // Private variables/functions
    var _privateVar;

    function _privateFunction() { }

    // Public API
    function publicFunction() { }

    // Exports at bottom
    exports.publicFunction = publicFunction;
});
```

### Naming Conventions

- **Private members:** Prefix with underscore (`_privateMethod`)
- **Constants:** UPPER_SNAKE_CASE
- **Classes/Constructors:** PascalCase
- **Functions/Variables:** camelCase
- **jQuery objects:** Prefix with `$` (`$element`)

## Extensions

### Default Extensions

Located in `src/extensions/default/`, these provide core functionality:
- Code hints (CSS, HTML, JavaScript)
- JSLint integration
- Quick Edit/Quick View
- Live Preview (Static Server)
- Themes (Dark/Light)
- Code Folding
- Navigation and History
- And more...

### Writing Extensions

Extensions have this basic structure:

```javascript
define(function (require, exports, module) {
    "use strict";

    var CommandManager = require("command/CommandManager"),
        Menus          = require("command/Menus");

    function handleCommand() {
        // Extension logic
    }

    // Register command
    CommandManager.register("My Command", "myextension.command", handleCommand);

    // Add to menu
    var menu = Menus.getMenu(Menus.AppMenuBar.EDIT_MENU);
    menu.addMenuItem("myextension.command");
});
```

See `src/extensions/samples/` for examples.

## Internationalization (i18n)

- Translations are in `src/nls/` subdirectories (e.g., `src/nls/de/`, `src/nls/fr/`)
- Uses RequireJS i18n plugin
- Root strings in `src/nls/root/strings.js`
- 35+ community-maintained translations
- See `src/nls/README.md` for translation guidelines

## Key Third-Party Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| CodeMirror | (bundled) | Core text editor |
| jQuery | 2.1.3 | DOM manipulation |
| RequireJS | (bundled) | Module loading |
| LESS | (bundled) | CSS preprocessing |
| Lodash | 4.17.15 | Utility functions |
| Mustache | (bundled) | Templating |

## Important Notes for AI Assistants

1. **Legacy Project:** This project is no longer actively maintained by Adobe. Be cautious about suggesting major architectural changes.

2. **Native Shell Dependency:** Brackets cannot run in a regular browser - it requires the brackets-shell native wrapper.

3. **AMD Modules:** All JavaScript uses RequireJS AMD format, not ES modules.

4. **ECMAScript Version:** The codebase uses ES6 features (arrow functions, classes, const/let) but within AMD modules.

5. **Testing:** Tests must be run through the Brackets application or via Grunt. Direct browser testing of spec files won't work.

6. **File System:** The file system implementation (`filesystem/impls/appshell/`) is specific to the native shell.

7. **Live Development:** The Live Preview feature requires browser communication through the native shell.

8. **Extension Context:** Extensions run in a sandboxed RequireJS context with access to core Brackets APIs.

## Common Tasks

### Adding a New Command

```javascript
var Commands       = require("command/Commands"),
    CommandManager = require("command/CommandManager"),
    Menus          = require("command/Menus");

// Define command ID in Commands.js or use custom namespace
var MY_COMMAND = "myextension.myCommand";

// Register the command
CommandManager.register("My Command Name", MY_COMMAND, function() {
    // Handler code
});

// Add to a menu
var menu = Menus.getMenu(Menus.AppMenuBar.FILE_MENU);
menu.addMenuItem(MY_COMMAND);
```

### Working with Documents

```javascript
var DocumentManager = require("document/DocumentManager");

// Get current document
var currentDoc = DocumentManager.getCurrentDocument();

// Open a document
DocumentManager.getDocumentForPath("/path/to/file.js")
    .done(function(doc) {
        // Work with document
    });
```

### Working with the Editor

```javascript
var EditorManager = require("editor/EditorManager");

// Get the focused editor
var editor = EditorManager.getFocusedEditor();

// Get cursor position
var pos = editor.getCursorPos();

// Get selected text
var selection = editor.getSelectedText();
```

## Resources

- [How to Hack on Brackets](https://github.com/adobe/brackets/wiki/How-to-Hack-on-Brackets)
- [How to Write Extensions](https://github.com/adobe/brackets/wiki/How-to-write-extensions)
- [Brackets API Documentation](https://github.com/adobe/brackets/wiki/Brackets-Development-How-Tos)
- [CodeMirror Documentation](https://codemirror.net/doc/manual.html)
