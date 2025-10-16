# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository Overview
This is an Obsidian plugin that automatically uploads images to image hosting services using PicGo, PicList, and PicGo-Core. The plugin supports automatic upload on paste, batch upload of local images, and image deletion management.

## Development Commands

### Core Development
- `pnpm dev` - Start development mode with file watching and auto-rebuild
- `pnpm build` - Build the plugin for production (includes typecheck)
- `pnpm typecheck` - Run TypeScript type checking without emitting files
- `pnpm test` - Run unit tests with Vitest

### Package Management
- Uses pnpm (version 9.12.3+) as package manager
- Node.js version specified in `.node-version` file

## Architecture Overview

### Core Plugin Structure
- **main.ts** - Primary plugin class extending Obsidian's Plugin class
- **setting.ts** - Settings tab implementation and configuration management  
- **helper.ts** - Utility methods for file operations and content manipulation
- **uploader/** - Modular uploader implementations for different services
- **lang/** - Internationalization support

### Key Architectural Patterns
- **Strategy Pattern**: Multiple uploader implementations (PicGo, PicGo-Core) behind common interface
- **Plugin System**: Extends Obsidian's Plugin base class with standardized lifecycle methods
- **Event-Driven**: Heavy use of Obsidian's event system for paste, drag-drop, and menu interactions
- **Settings Management**: Centralized configuration through Obsidian's data storage API

### Uploader System
The plugin uses a modular uploader system:
- `UploaderManager` - Factory and orchestration layer
- `PicGoUploader` - HTTP-based communication with PicGo app
- `PicGoCoreUploader` - Direct CLI integration with PicGo-Core
- Support for both local and remote server modes

### File Processing Pipeline
1. **Input Detection** - Clipboard, drag-drop, or command-triggered
2. **File Filtering** - Image type validation and network domain filtering  
3. **Upload Processing** - Route through appropriate uploader
4. **Content Replacement** - Update markdown with uploaded URLs
5. **Cleanup** - Optional local file deletion

## Development Conventions

### TypeScript Configuration
- Target: ES2023 with ESNext modules
- Strict type checking enabled (`noImplicitAny: true`)
- Source maps inlined for debugging
- Path aliases for mocking Obsidian API in tests

### Code Style  
- Prettier configuration with 2-space indentation
- No trailing commas, semicolons enabled
- Double quotes preferred over single quotes

### Testing Setup
- Vitest for unit testing with TypeScript support
- Mocked Obsidian API in `src/mocks/obsidian.ts`
- Separate TypeScript config for tests (`tsconfig.vitest.json`)

### Build System
- Rollup with TypeScript, Node resolution, and CommonJS plugins
- Outputs single file bundle compatible with Obsidian's plugin system
- External dependencies: `obsidian`, `electron`
- Inline dynamic imports for plugin compatibility

## Plugin-Specific Development Notes

### Obsidian API Integration
- Extends `Plugin` class with lifecycle methods (`onload`, `onunload`)
- Register event handlers for `editor-paste`, `editor-drop`, `file-menu`, `editor-menu`
- Uses Obsidian's settings API for persistent configuration
- Integrates with Obsidian's command palette and context menus

### Platform Considerations
- Desktop and mobile app compatibility (some features desktop-only)
- Electron integration for file system access in desktop app
- Remote server mode required for mobile platform

### Localization
- Multi-language support through `lang/` directory
- Translation helper function `t()` for UI strings
- Language files organized by locale

### Plugin Manifest
- Standard Obsidian plugin structure with `manifest.json`
- Version synchronization between `package.json` and `manifest.json`
- Minimum Obsidian version compatibility specified

## Testing Strategy
- Unit tests focus on utility functions and core logic
- Obsidian API mocked to enable isolated testing
- Test files follow `.test.ts` naming convention
- Coverage of image processing, file filtering, and URL handling logic
