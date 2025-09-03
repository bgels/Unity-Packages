## **DRAG AND DROP THE CHARACTER PREFAB LOCATED IN Runtime/PlayerController TO YOUR SCENE TO USE!!**
Special thanks to @beans for the character tutorial

# **Advanced First-Person Character Controller**


A modular and customizable 3D physics-based fps character controller for Unity, featuring mechanics such as sliding, coyote time, and jump buffering.


## **Core Technologies**


This controller relies on two key assets. Make sure they are installed in your project.


* [**Kinematic Character Controller**](https://assetstore.unity.com/packages/tools/physics/kinematic-character-controller-99131) (by Philippe St-Amand): A query-based character controller that provides a stable and predictable physics foundation.  
* [**Unity's Input System Package**](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.7/manual/index.html): The modern standard for handling input, allowing for easy control remapping.


## **Setup & GameObject Hierarchy**


A specific GameObject hierarchy is required for the controller to function correctly. **This should already be present in the hierarchy upon importing this package**


\- Player (GameObject)  [With: Player Script]
  \- Character (GameObject) [With: Capsule Collider, Kinematic Character Motor Script, Player Character Script]
    \- Root (GameObject, for scaling)  
      \- Render (GameObject with Mesh, for visual appearance)
    \- CameraTarget (GameObject, set for camera positioning)
  \- Camera (Gameobject) [With: Player Camera Script]
   \- Spring (Optional Gameobject) [With: Camera Sprint Script]
    \- Lean (Optional Gameobject) [With: Camera Lean Script]
     \- MainCamera (Gameobject, for display)
 
**Player (Root Object):** Holds the Player.cs script for input capture and the PlayerInput component.  
**PlayerCharacter (Child Object):** The character's physical representation. Requires the KinematicCharacterMotor.cs and PlayerCharacter.cs scripts.  
**Root (Grandchild Object):** Child of PlayerCharacter. Its scale is modified to simulate crouching. Visual models can be parented here.  
**CameraTarget (Grandchild Object):** Child of PlayerCharacter. Its position is adjusted for the camera's anchor point. The main camera should follow this transform.


## **Script Breakdown & Dependencies**


* Player.cs  
  * Input Handler.  
  * **Dependencies:** PlayerInput component, PlayerCharacter script reference.  
  * **How it Works:** Reads raw input, packages it into a CharacterInput struct each frame, and passes it to the PlayerCharacter.  
* PlayerCharacter.cs  
  * Handles physics + movement.  
  * **Dependencies:** KinematicCharacterMotor component. Implements ICharacterController.  
  * **How it Works:** Receives CharacterInput and translates it into movement. Manages the state machine (Stand, Crouch, Slide) and all physics calculations.  
* PlayerCamera.cs (Assumed)  
  * Display.  
  * **How it Works:** Attached to the Main Camera. Follows the CameraTarget transform and handles look rotation.  


## **How It Works:**


### **State Machine**


The controller uses a state machine defined by the Stance enum. The CharacterState struct holds current frame data, while a copy of the previous frame's state (\_lastState) allows for reliable detection of state changes (e.g., landing, starting a slide) to trigger mechanics.


### **Ground vs. Air**


Movement vectors are handled differently based on grounding. On the ground, vectors are projected along the surface slope (GetDirectionTangentToSurface) for smooth traversal. In the air, vectors are projected onto a horizontal plane (ProjectOnPlane) for classic air-strafing control.


## **Key Features**


* **Sliding:**  
  * **Momentum Preservation:** Converts fall velocity into slide speed upon landing.  
  * **Intentionality:** Requires a crouch press while airborne to get the speed boost, preventing accidental activation.  
  * **Slope Physics:** Naturally accelerates down slopes while sliding.  
* **Responsive Jumping:**  
  * **Coyote Time:** Allows jumping for a brief window after walking off a ledge.  
  * **Jump Buffering:** "Remembers" a jump input from just before landing and executes it on the first possible frame.  
* **Dynamic Capsule Height:** The capsule smoothly changes height when crouching and checks for overhead obstructions before standing up.


### **Inspector Settings**


Most gameplay variables (speeds, friction, gravity, timers) are exposed in the PlayerCharacter Inspector for easy tweaking without code changes.
The state-driven design makes expansion simple.


