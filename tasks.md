# Implementation Plan: The Obsidian Ledger

## Overview

This implementation plan breaks down The Obsidian Ledger into four main milestones: (1) Project Setup with React/Phaser frontend and Node.js backend, (2) The Architect Agent parser that converts local codebases into JSON maps, (3) The game world renderer using Phaser, and (4) The Sage Agent integration for AI-powered tutoring and puzzles.

## Tasks

### Milestone 1: Project Setup

- [ ] 1. Initialize project structure and dependencies
  - Create monorepo structure with `frontend/` and `backend/` directories
  - Initialize React + TypeScript project in `frontend/` with Vite
  - Initialize Node.js + TypeScript project in `backend/`
  - Install Phaser 3 for game rendering
  - Install Express for backend API
  - Set up ESLint and Prettier for code formatting
  - Create `.gitignore` and basic README
  - _Requirements: Foundation for all other requirements_

- [ ] 2. Set up data models and TypeScript interfaces
  - Create `shared/types.ts` with WorldMap, Zone, LearningModule, Puzzle, PlayerState interfaces
  - Ensure interfaces match the JSON schemas from design document
  - Export types for use in both frontend and backend
  - _Requirements: 2.1, 5.1, 6.1, 9.1_

- [ ] 3. Create basic backend API structure
  - Set up Express server with TypeScript
  - Create `/api/parse` endpoint (placeholder)
  - Create `/api/sage` endpoint (placeholder)
  - Add CORS configuration for local development
  - _Requirements: 1.3, 4.1_

- [ ] 4. Create basic frontend structure
  - Set up React app with routing
  - Create main game container component
  - Create placeholder screens: Home, Loading, Game
  - Add basic CSS with Obsidian color palette
  - _Requirements: 3.1, 3.2_

- [ ] 5. Checkpoint - Ensure project builds and runs
  - Verify frontend starts on `localhost:5173`
  - Verify backend starts on `localhost:3000`
  - Verify types are shared correctly between frontend and backend
  - Ask the user if questions arise

### Milestone 2: The Architect Agent (Parser)

- [ ] 6. Implement codebase file system parser
  - [ ] 6.1 Create `CodebaseParser` class in `backend/src/parser/`
    - Implement `parseCodebase(path: string)` method
    - Recursively read directory structure
    - Filter files by supported extensions (.ts, .js, .py, .java, .cpp, .go)
    - Return `ParsedCodebase` structure with file paths and contents
    - _Requirements: 1.3, 1.5_
  
  - [ ]* 6.2 Write property test for codebase parsing
    - **Property 1: Codebase Parsing Completeness**
    - **Validates: Requirements 1.3**
  
  - [ ]* 6.3 Write property test for multi-language support
    - **Property 3: Multi-Language Support**
    - **Validates: Requirements 1.5**
  
  - [ ]* 6.4 Write unit tests for parser edge cases
    - Test empty directories
    - Test nested directory structures
    - Test files with no extensions
    - _Requirements: 1.3_

- [ ] 7. Implement error handling for invalid paths
  - [ ] 7.1 Add path validation in `CodebaseParser`
    - Check if path exists using `fs.existsSync()`
    - Check if path is accessible
    - Return structured errors with type and message
    - _Requirements: 1.4_
  
  - [ ]* 7.2 Write property test for invalid path handling
    - **Property 2: Invalid Path Error Handling**
    - **Validates: Requirements 1.4**

- [ ] 8. Implement learning content extraction
  - [ ] 8.1 Create `ContentExtractor` class
    - Implement `extractLearningModules(codebase: ParsedCodebase)` method
    - Parse code files to identify functions, classes, and comments
    - Use simple regex patterns for extraction (can be enhanced later)
    - Group related code into modules by file/directory structure
    - Generate concept lists from function/class names
    - _Requirements: 8.2_
  
  - [ ] 8.2 Create `CodeExample` objects from extracted code
    - Store original code text
    - Extract inline comments as explanations
    - Detect language from file extension
    - _Requirements: 8.2_
  
  - [ ]* 8.3 Write property test for code example extraction
    - **Property 16: Code Example Extraction**
    - **Validates: Requirements 8.2**

- [ ] 9. Implement world map generation
  - [ ] 9.1 Create `WorldMapGenerator` class
    - Implement `generateWorldMap(modules: LearningModule[])` method
    - Create one zone per learning module
    - Assign zone positions in a grid layout
    - Generate tile maps (simple grass/path tiles for now)
    - Set first module as start zone
    - Create connections between adjacent zones
    - _Requirements: 2.1, 2.4, 2.5_
  
  - [ ] 9.2 Implement zone connectivity logic
    - Connect zones based on directory hierarchy (parent-child relationships)
    - Ensure all zones are reachable from start
    - Use breadth-first search to verify connectivity
    - _Requirements: 2.5_
  
  - [ ]* 9.3 Write property test for world map structure
    - **Property 4: World Map Structure Validity**
    - **Validates: Requirements 2.1**
  
  - [ ]* 9.4 Write property test for start and goal zones
    - **Property 5: Start and Goal Zone Existence**
    - **Validates: Requirements 2.4**
  
  - [ ]* 9.5 Write property test for zone reachability
    - **Property 6: Zone Reachability**
    - **Validates: Requirements 2.5**

- [ ] 10. Implement JSON export functionality
  - [ ] 10.1 Create `JSONExporter` class
    - Implement `exportWorldMap(worldMap: WorldMap, outputPath: string)` method
    - Implement `exportLearningModules(modules: LearningModule[], outputPath: string)` method
    - Write formatted JSON with proper indentation
    - Validate JSON against schemas before writing
    - _Requirements: 2.1_
  
  - [ ]* 10.2 Write unit tests for JSON export
    - Test that exported JSON is valid
    - Test that exported JSON matches schema
    - _Requirements: 2.1_

- [ ] 11. Wire up `/api/parse` endpoint
  - [ ] 11.1 Implement POST `/api/parse` handler
    - Accept `{ path: string }` in request body
    - Call `CodebaseParser.parseCodebase()`
    - Call `ContentExtractor.extractLearningModules()`
    - Call `WorldMapGenerator.generateWorldMap()`
    - Call `JSONExporter` to write files to `backend/data/`
    - Return success response with file paths
    - _Requirements: 1.3, 2.1_
  
  - [ ]* 11.2 Write integration test for parse endpoint
    - Test with sample codebase
    - Verify JSON files are created
    - Verify JSON structure is valid
    - _Requirements: 1.3, 2.1_

- [ ] 12. Checkpoint - Test parser with real codebase
  - Test parsing a small sample codebase (e.g., a simple Node.js project)
  - Verify world-map.json and learning-modules.json are generated
  - Manually inspect JSON structure
  - Ask the user if questions arise

### Milestone 3: The World (Game Rendering)

- [ ] 13. Set up Phaser game engine
  - [ ] 13.1 Create Phaser game configuration
    - Create `frontend/src/game/config.ts` with Phaser config
    - Set canvas size (800x600 for now)
    - Configure physics (Arcade physics for simple collisions)
    - Set background color to Obsidian theme (#0d0d0d)
    - _Requirements: 3.1, 3.2_
  
  - [ ] 13.2 Create main game scene
    - Create `frontend/src/game/scenes/MainScene.ts`
    - Extend Phaser.Scene
    - Implement `preload()`, `create()`, and `update()` methods
    - _Requirements: 3.1_

- [ ] 14. Implement world map loading
  - [ ] 14.1 Create `WorldMapLoader` class
    - Implement `loadWorldMap(jsonPath: string)` method
    - Fetch world-map.json from backend
    - Parse JSON into WorldMap TypeScript object
    - Validate structure
    - _Requirements: 2.1_
  
  - [ ] 14.2 Create `LearningModuleLoader` class
    - Implement `loadLearningModules(jsonPath: string)` method
    - Fetch learning-modules.json from backend
    - Parse JSON into LearningModule array
    - Store in game state
    - _Requirements: 5.1_

- [ ] 15. Implement tile map rendering
  - [ ] 15.1 Create simple pixel art tileset
    - Create 16x16 pixel tiles: grass, path, wall, water
    - Use Obsidian color palette
    - Export as sprite sheet PNG
    - _Requirements: 3.1, 3.2, 3.3_
  
  - [ ] 15.2 Implement tile rendering in MainScene
    - Load tileset in `preload()`
    - Render zone's tileMap array in `create()`
    - Use Phaser's Tilemap system
    - _Requirements: 3.1_

- [ ] 16. Implement player character
  - [ ] 16.1 Create player sprite
    - Create 16x16 pixel character sprite with 4-direction animations
    - Use Obsidian theme colors (purple accent)
    - Export as sprite sheet
    - _Requirements: 3.3, 3.4_
  
  - [ ] 16.2 Implement player movement
    - Create `Player` class extending Phaser.Sprite
    - Handle keyboard input (WASD or arrow keys)
    - Update player position based on input
    - Add collision detection with walls
    - Implement smooth movement animation
    - _Requirements: 7.1_
  
  - [ ]* 16.3 Write property test for movement input
    - **Property 13: Movement Input Response**
    - **Validates: Requirements 7.1**

- [ ] 17. Implement camera system
  - [ ] 17.1 Configure camera to follow player
    - Set camera bounds to current zone size
    - Make camera follow player sprite
    - Add smooth camera movement
    - _Requirements: 3.5_

- [ ] 18. Implement zone transitions
  - [ ] 18.1 Create zone boundary detection
    - Detect when player crosses zone edge
    - Check if target zone is unlocked (prerequisites met)
    - Trigger zone transition if allowed
    - _Requirements: 7.2, 7.3_
  
  - [ ] 18.2 Implement zone loading
    - Unload current zone
    - Load new zone's tile map
    - Update player position to zone entrance
    - Load new zone's NPCs
    - _Requirements: 7.2_
  
  - [ ]* 18.3 Write property test for zone transition
    - **Property 14: Zone Transition Module Loading**
    - **Validates: Requirements 7.2**
  
  - [ ]* 18.4 Write property test for prerequisite enforcement
    - **Property 15: Prerequisite Enforcement**
    - **Validates: Requirements 7.3, 7.4**

- [ ] 19. Implement NPC rendering
  - [ ] 19.1 Create Sage NPC sprite
    - Create 16x16 pixel NPC sprite (wizard/sage appearance)
    - Use Obsidian theme colors
    - _Requirements: 3.4, 4.1_
  
  - [ ] 19.2 Create `NPC` class
    - Extend Phaser.Sprite
    - Position NPCs based on zone data
    - Add interaction indicator when player is nearby
    - _Requirements: 4.1_

- [ ] 20. Implement minimap
  - [ ] 20.1 Create minimap UI component
    - Create small overlay in corner of screen
    - Render simplified zone layout
    - Highlight current zone
    - Show explored vs unexplored zones
    - _Requirements: 7.5_

- [ ] 21. Checkpoint - Test game world rendering
  - Load a generated world map
  - Verify tiles render correctly
  - Verify player can move around
  - Verify zone transitions work
  - Ask the user if questions arise

### Milestone 4: The Sage (AI Integration)

- [ ] 22. Set up Sage Agent backend
  - [ ] 22.1 Create `SageAgent` class in `backend/src/sage/`
    - Set up OpenAI API client (or alternative LLM)
    - Store API key in environment variables
    - Implement `answerQuery(query: string, context: LearningModule)` method
    - _Requirements: 4.2, 4.3_
  
  - [ ] 22.2 Implement context building
    - Format learning module data for LLM context
    - Include relevant code examples
    - Include concept list
    - _Requirements: 4.2_

- [ ] 23. Implement puzzle generation
  - [ ] 23.1 Create `PuzzleGenerator` class
    - Implement `generatePuzzle(module: LearningModule)` method
    - Use LLM to create coding challenges based on module concepts
    - Generate test cases for puzzles
    - Generate starter code
    - _Requirements: 5.1_
  
  - [ ]* 23.2 Write property test for puzzle concept alignment
    - **Property 7: Puzzle Concept Alignment**
    - **Validates: Requirements 5.1**

- [ ] 24. Implement code execution and validation
  - [ ] 24.1 Create `CodeExecutor` class
    - Implement `executePuzzle(code: string, testCases: TestCase[])` method
    - Use VM2 or isolated-vm for safe code execution
    - Run code against each test case
    - Capture outputs and errors
    - Return validation results
    - _Requirements: 5.3, 5.4_
  
  - [ ]* 24.2 Write property test for solution validation
    - **Property 8: Solution Validation Correctness**
    - **Validates: Requirements 5.3**
  
  - [ ]* 24.3 Write property test for incorrect solution feedback
    - **Property 9: Incorrect Solution Feedback**
    - **Validates: Requirements 5.4**

- [ ] 25. Wire up `/api/sage` endpoint
  - [ ] 25.1 Implement POST `/api/sage/query` handler
    - Accept `{ query: string, moduleId: string }` in request body
    - Load learning module by ID
    - Call `SageAgent.answerQuery()`
    - Return response text
    - _Requirements: 4.2, 4.3_
  
  - [ ] 25.2 Implement POST `/api/sage/hint` handler
    - Accept `{ puzzleId: string, attemptCount: number }` in request body
    - Generate progressive hints based on attempt count
    - Return hint text
    - _Requirements: 4.4_
  
  - [ ] 25.3 Implement POST `/api/sage/validate` handler
    - Accept `{ puzzleId: string, code: string }` in request body
    - Load puzzle test cases
    - Call `CodeExecutor.executePuzzle()`
    - Return validation results
    - _Requirements: 5.3, 5.4_

- [ ] 26. Implement dialogue UI system
  - [ ] 26.1 Create `DialogueBox` React component
    - Create modal overlay with Obsidian theme
    - Display NPC portrait and name
    - Display message text with typewriter effect
    - Add input field for player queries
    - Add "Ask Question" and "Close" buttons
    - _Requirements: 4.1_
  
  - [ ] 26.2 Integrate dialogue with Phaser game
    - Trigger dialogue when player interacts with NPC
    - Pass current module context to dialogue
    - Send queries to `/api/sage/query` endpoint
    - Display responses in dialogue box
    - _Requirements: 4.1, 4.2_

- [ ] 27. Implement code editor UI
  - [ ] 27.1 Create `CodeEditor` React component
    - Use Monaco Editor or CodeMirror
    - Display puzzle description
    - Load starter code
    - Add "Submit" and "Get Hint" buttons
    - Style with Obsidian theme
    - _Requirements: 5.2_
  
  - [ ] 27.2 Integrate code editor with game
    - Trigger code editor when player interacts with puzzle object
    - Send code submissions to `/api/sage/validate`
    - Display validation results
    - Update player state on successful completion
    - _Requirements: 5.2, 5.3, 5.5_
  
  - [ ]* 27.3 Write property test for puzzle completion state
    - **Property 10: Puzzle Completion State Update**
    - **Validates: Requirements 5.5**

- [ ] 28. Implement player state management
  - [ ] 28.1 Create `PlayerStateManager` class in frontend
    - Track current position, zone, completed modules, solved puzzles
    - Implement `saveState()` method to localStorage
    - Implement `loadState()` method from localStorage
    - _Requirements: 6.1, 6.2, 6.3, 9.1, 9.2_
  
  - [ ] 28.2 Implement save/load functionality
    - Save state on zone transitions and puzzle completions
    - Load state on game initialization
    - Handle multiple save slots (one per codebase)
    - _Requirements: 9.1, 9.2, 9.3, 9.4_
  
  - [ ]* 28.3 Write property test for progress persistence
    - **Property 11: Progress Persistence Round-Trip**
    - **Validates: Requirements 6.3, 9.1, 9.2, 9.3**
  
  - [ ]* 28.4 Write property test for save slot isolation
    - **Property 18: Multiple Save Slot Isolation**
    - **Validates: Requirements 9.4**
  
  - [ ]* 28.5 Write property test for corrupted save handling
    - **Property 19: Corrupted Save Handling**
    - **Validates: Requirements 9.5**

- [ ] 29. Implement progress display UI
  - [ ] 29.1 Create `ProgressPanel` React component
    - Display list of learning modules
    - Show completion status for each module
    - Display list of puzzles with solved status
    - Add visual indicators (checkmarks, progress bars)
    - _Requirements: 6.4_
  
  - [ ] 29.2 Implement completed content review
    - Allow clicking on completed modules to review content
    - Navigate to completed module zones
    - _Requirements: 6.5_
  
  - [ ]* 29.3 Write property test for completed content accessibility
    - **Property 12: Completed Content Accessibility**
    - **Validates: Requirements 6.5**

- [ ] 30. Checkpoint - Test full learning flow
  - Parse a sample codebase
  - Load the generated world
  - Navigate to a zone
  - Interact with Sage NPC
  - Attempt a coding puzzle
  - Verify progress is saved
  - Ask the user if questions arise

### Final Integration and Polish

- [ ] 31. Implement home screen and codebase selection
  - [ ] 31.1 Create `HomeScreen` React component
    - Add input field for codebase path
    - Add "Parse Codebase" button
    - Display loading indicator during parsing
    - Show error messages if parsing fails
    - List available save slots
    - _Requirements: 1.3, 1.4_
  
  - [ ] 31.2 Implement loading screen
    - Show progress during world generation
    - Display progress indicator for large codebases
    - _Requirements: 10.5_

- [ ] 32. Add error handling and user feedback
  - [ ] 32.1 Implement global error boundary
    - Catch and display runtime errors
    - Provide user-friendly error messages
    - Offer recovery options (restart, return to home)
    - _Requirements: 1.4, 8.5, 9.5_
  
  - [ ]* 32.2 Write property test for malformed input resilience
    - **Property 17: Malformed Input Resilience**
    - **Validates: Requirements 8.5**

- [ ] 33. Performance optimization
  - [ ] 33.1 Optimize rendering performance
    - Implement sprite batching
    - Use object pooling for frequently created objects
    - Optimize tile rendering for large zones
    - _Requirements: 10.1_
  
  - [ ] 33.2 Optimize API response times
    - Add caching for learning modules
    - Implement request debouncing for Sage queries
    - _Requirements: 10.2, 10.3_

- [ ]* 34. Write end-to-end integration tests
  - Test complete flow: parse → load → navigate → solve puzzle → save
  - Test error recovery scenarios
  - Test multiple save slots
  - _Requirements: All_

- [ ] 35. Final checkpoint - Complete system test
  - Parse a real-world codebase (e.g., a small open-source project)
  - Play through the generated game
  - Verify all features work together
  - Test on different browsers
  - Ask the user if questions arise

## Notes

- Tasks marked with `*` are optional property-based tests and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation at key milestones
- Property tests validate universal correctness properties (minimum 100 iterations each)
- Unit tests validate specific examples and edge cases
- The implementation uses TypeScript for both frontend (React + Phaser) and backend (Node.js + Express)
- The Sage Agent can use any LLM API (OpenAI, Anthropic, or local models)
