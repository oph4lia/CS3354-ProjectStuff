# Homework 2
> Mikkael Dumancas

## Part D: Non-trivial Steps Identification

From HW1, I will be identifying the non-trivial steps in the expanded usecase 1: "Player pauses/resumes game". I will paste the expanded usecase here for easier reference:

Preconditions: The game is actively running. The player is in control of their ship.
| Actor: Player | System: Greenfoot Invaders Game |
| ------------- | ------------- |
|1. TUCBW the player presses the pause key (ESC or P) during active gameplay | 2. Game freezes all game entities (player ship, enemies, projectiles) |
| | 3. System displays the pause menu with options: "Resume", "Help", "Settings", and "Quit to Main Menu". |
| 4a. Players selects the "Resume" option | 5a. System hides the pause menu |
| | 6a. System unfreezes all game entities and resumes gameplay |
| 4b. Player selects "Help" option | 5b. System displays the help screen while keeping the game paused | 
| 6b. Player closes the help screen | 7b. System returns to the pause game |
| 4c. Player selects "Quit to Main Menu" option | 5c. System ends the current game session |
| | 6c.System returns tot he main menu screen |
| | 7. TUCEW the player returns to active gameplay or exits to the main menu |
Postconditions: The game state has been updated. Player progress is maintained if gameplay is resumed.

These are the non-trivial steps I identified:

| Non-trivial step | Reason |
| --- | --- |
| Step 2 | Requires the system to identify/access all active game entities, the ability to modify each entity's state to prevent further movement/action, the ability to restore the current state of all entities when resumed, and it needs to track which entities were in motion to maintain their trajectory information.|
| Step 3 | Requires the creation of the pause menu UI objects, association between menu options and their corresponding actions, modification of the game state to recognize it's in "paused" mode, and ensuring input handling is now directed to the pause menu rather than gameplay controls. |
| Step 6a | Requires background processes, including accessing all previously frozen game entities, restoring the previous state and movement patterns of all entities, re-associating player input with ship movement control, reactivating AI behaviors for enemy objects, reestablishing collision detection between objects |

## Part E: Usecase Realization

Step 2 Sequence Diagram:
![Step2 drawio](https://github.com/user-attachments/assets/8505c7e0-c5a0-428d-8eff-9e220f4d1b6a)

GRASP Concepts and extra notes:
- :GameWorld manages the game state and holds knowledge about entities. (GRASP: Information Expert)
- :GameController delegates user actions and coordinates game logic. (GRASP: Controller)
- :EntityManager maintains and provides access to game entities. It decouple storage from logic in them. (GRASP: Low Coupling)
- pause() is implemented via the GameObject interface. Ship, Enemy, and Projectile override it. (GRASP: Polymorphism)
- GameObject interface includes: Ship, Enemy, and Projectile. All inherit from a common GameObject abstraction.
- The pause mechanism halts in-game activity without losing state or entity context.

Step 3 Sequence Diagram:

![ActualStep3 drawio](https://github.com/user-attachments/assets/2c80bd07-8611-47dc-bbfd-8439607942b8)

GRASP Concepts and extra notes:
- :UIManager is responsible for creating UI elements. (GRASP: Creator)
- :GameController delegates UI requests and bridges game logic with UI (GRASP: Controller)
- :GreeenFoodGUI displays the menu withouth having direct game logic involvement. (GRASP: Low Coupling)
- :MenuOption represents a common UI element. (Polymorphism & shared interface among menu elements)

Step 6a Sequence Diagram:
![Step3 drawio](https://github.com/user-attachments/assets/216b9067-95a4-4845-b617-70a9d298843e)

GRASP Concepts and extra notes:
- :GameWorld manages the game state and holds knowledge about entities. (GRASP: Information Expert)
- The game state is encapsulated to isolate changes (GRASP: Protected Variations)
- resume() is implemented via common GameObject interface (GRASP: Polymorphism)
- All entity types implement a shared GameObject interface with a resume() method to enable unified handling.

## Part F: Class Diagram

![ClassDiagram drawio](https://github.com/user-attachments/assets/f79f75ba-cec4-4b85-845d-53c6665e45de)

Application of GRASP Patterns:

- Information Expert: GameWorld maintains collections of all game entities since it has the information needed to manage them.
- Creator: GameWorld creates Enemy and Projectile instances since it closely uses these objects.
- Controller: GameController handles system operations triggered by user events, coordinating between UI and domain layers.
- Low Coupling: The design minimizes dependencies between classes. For example, Player doesn't directly interact with UI components.
- High Cohesion: Each class has a clear, focused set of responsibilities related to a single aspect of the system.
- Protected Variations: The design isolates parts likely to change (like UI elements) from those that are more stable.
- Polymorphism: All game entities (Player, Enemy, Projectile) can be treated polymorphically through a common interface or abstract class (GameObject) with methods like pause() and resume().

## Part G: Testing

**Scenario 1: Successful Game Pause**
***Input:***
- Current Game State:
  - Player ship at position (150, 550)
  - 3 Enemy ships at positions [(100, 100), (200, 100), (300, 100)]
  - 2 Active projectiles at positions [(150, 300), (250, 400)]
  - Game Status: ACTIVE
- Player's Action: Press ESC key

***Expected Output:***
- Updated Game State:
  - Player ship remains at position (150, 550)
  - 3 enemy ships remain at positions [(100, 100), (200, 100), (300, 100)]
  - 2 projectiles remain at positions [(150, 300), (250, 400)]
  - Game Status: PAUSED
  - Pause menu displayed with options: "Resume", "help", "settings", "Quit to Main Menu"

**Scenario 2: Resume Game from Pause Menu**
***Input:***
- Current Game State:
  - Player ship at position (150, 550)
  - 3 Enemy ships at positions [(100, 100), (200, 100), (300, 100)]
  - 2 Active projectiles at positions [(150, 300), (250, 400)]
  - Game Status: PAUSED
  - Pause menu is visible

- Player's Action: Select "Resume" option

***Expected Output:***
- Updated Game State:
  - Player ship at position (150, 550), responsive to controls
  - 3 Enemy ships at positions [(100, 100), (200, 100), (300, 100)], resuming movement patterns
  - 2 Projectiles at positions [(150, 300), (250, 400)], continuing along trajectory
  - Game Status: ACTIVE
  - Pause menu no longer visible
  - Game physics and collision detection active

**Scenario 3: Access Help from Pause Menu**
***Input:***
- Current Game State:
  - Game Status: PAUSED
  - Pause menu is visible
- Player's Action: Select "Help" option

***Expected Output:***
- Updated Game State:
  - Game Status: PAUSED
  - Pause menu replaced by help screen showing game controls and instructions
  - Option to return to pause menu visible
  - All game entities remain frozen

**Scenario 4: Quit to Main Menu**
***Input:***
- Current Game State:
  - Game Status: PAUSED
  - Pause menu is visible
- Player's Action: Select "Quit to Main Menu" option

***Expected Output:***
- Updated Game State:
  - Game session terminated
  - All game entities removed from memory
  - Main menu screen displayed
  - Game Status: MAIN_MENU
