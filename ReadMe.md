# Unreal Engine Skydiving Gameplay Prototype

A short, self-contained gameplay sequence created in Unreal Engine 5 that demonstrates a complete level loop, from a cinematic trigger to a player-driven objective and a final completion state.

## Synopsis

This project showcases a linear gameplay sequence where the player triggers an in-game cutscene, makes a choice that affects their starting position, and then engages in a skydiving chase to catch a falling objective. It serves as a practical example of integrating various Unreal Engine systems like Level Sequencer, UMG widgets, Character Movement, and Blueprint communication.

## Features

This prototype includes the following implemented features:

*   **Cinematic Triggers:** The level begins by having the player walk into a `TriggerVolume`, which starts a Level Sequence cutscene.
*   **Player Choice UI:** After the cutscene, a UMG widget appears, pausing the game and allowing the player to select one of two different spawn points.
*   **Dynamic Skydiving State:** Based on the player's choice, the character teleports to the selected location and enters a custom "flying" movement mode with gravity enabled to simulate skydiving.
*   **Real-Time Altitude HUD:** A UMG widget appears on screen during the skydive, displaying the character's Z-axis location (altitude) in real-time.
*   **In-Air Character Control:** The `IA_Move` input action is re-mapped during the skydiving state to allow full 3D movement based on the camera's direction, enabling the player to steer their descent.
*   **Physics-Based Objective:** A collectible `BP_Bag` actor with physics enabled falls from the sky, creating a dynamic chase objective for the player.
*   **Level Completion:** Overlapping with the falling bag triggers a final cutscene and disables player input, successfully completing the level.
*   **Sequencer UMG Integration:** The final cutscene dynamically displays and animates a "Level Complete!" text widget using a UMG track within the Level Sequence.
*   **Dynamic Music:** Looping background music is triggered via the Level Blueprint after the initial cutscene concludes.

## How to Play

1.  Start the level by pressing "Play" in the editor.
2.  Walk your character into the large trigger volume to begin the first cutscene.
3.  When the UI appears, click either **"Spawn Location A"** or **"Spawn Location B"**.
4.  You will be teleported into the sky and begin falling. Use the **mouse to look** and **WASD keys to steer** your descent. Point the camera down and press 'W' to dive faster.
5.  Intercept and collide with the falling bag to trigger the final cutscene and complete the objective.

## Technical Implementation Details

*   **Character State Management:** The core of the gameplay logic is driven by a `bIsSkydiving` boolean variable in the `BP_ThirdPersonCharacter`. This variable is used in `Branch` and `Sequence` nodes within the `Event Tick` and `IA_Move` events to control whether the character uses ground-based or skydiving-based logic. It is also read by the Animation Blueprint to switch to the skydiving animation state.
*   **Cinematics & Events:** The cutscenes are driven by **Level Sequence** assets. The transition between the cutscene and gameplay is handled in the Level Blueprint using the `Create Level Sequence Player` node and binding a custom event to its **`OnFinished`** delegate.
*   **UI and Game Flow:** The spawn choice menu (`WBP_SpawnChoice`) demonstrates proper UI flow management by switching the game's input mode between "UI Only" and "Game Only" and controlling the visibility of the mouse cursor.
*   **Physics and Gameplay:** The falling bag uses a standard Static Mesh component with `Simulate Physics` enabled and `Linear Damping` adjusted to control its fall speed relative to the player. The player character's skydiving is achieved by setting the `CharacterMovementComponent` to `Flying` mode while re-enabling `Gravity Scale`.

## Getting Started

1.  Clone this repository to your local machine.
2.  Ensure you have **Unreal Engine 5.x** installed.
3.  Right-click the `.uproject` file and select "Generate Visual Studio project files".
4.  Open the `.sln` file to build the project in Visual Studio, or open the `.uproject` file directly in the Unreal Editor.
5.  Open the main level map and press **Play**.