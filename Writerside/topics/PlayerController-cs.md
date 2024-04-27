# PlayerController.cs
<show-structure depth="2" />

## Description
This script is used for controlling the player and handling all the player-relevant logic.

## Script
```C#
using System;
using System.Collections;
using Cameras;
using Unity.Cinemachine;
using input;
using Player.FSM.States;
using Tutorial;
using UI;
using UnityEngine;
using UnityEngine.InputSystem;
using Weapons;

namespace Player
{
    public static class TransformExtensions
    {
        public static GameObject FindGameObjectInChildWithTag(this Transform parent, string tag)
        {
            GameObject foundChild = null;
            for (var i = 0; i < parent.childCount; i++)
            {
                var child = parent.GetChild(i);
                if (child.CompareTag(tag))
                    foundChild = child.gameObject;
            }

            if (foundChild)
            {
                return foundChild;
            }

            throw new Exception("No child object with tag!");
        }
    }

    public class PlayerController : MonoBehaviour
    {
        #region Required Components

        [HideInInspector] public GameObject eventSystem;
        [HideInInspector] public inputSystem inputSystem;
        [HideInInspector] public PlayerInput playerInput;
        [HideInInspector] public MainCamera mainCamera;
        [HideInInspector] public CharacterController characterController;
        [HideInInspector] public GameObject playerMesh;
        [HideInInspector] public PlayerShooting playerShooting;
        [HideInInspector] public PlayerHealth playerHealth;
        [HideInInspector] public CanvasScript canvasScript;
        [HideInInspector] public AudioSource audioSource;

        #endregion

        #region Player States

        private PlayerStateMachine _playerStateMachine;
        [HideInInspector] public Idle IdleState;
        [HideInInspector] public Walking WalkingState;
        [HideInInspector] public Jumping JumpingState;
        [HideInInspector] public Airborne AirborneState;
        [HideInInspector] public WallJumping WallJumpingState;
        [HideInInspector] public WallRunning WallRunState;
        [HideInInspector] public Sliding SlidingState;

        #endregion

        #region Configurable Settings

        [Header("Player Movement")] 
        [SerializeField] private float playerSpeed;
        [SerializeField] private float sprintingSpeed;
        [SerializeField] private Quaternion maxWallRotation;

        [Header("Player Look")] [Range(0, 200)] 
        [SerializeField] private float mouseSensitivity;
        [SerializeField] private float xClamp;
        [SerializeField] private float rotationSpeed;


        [Header("Player Jump")] 
        [SerializeField] private float playerJumpHeight;
        [SerializeField] private float playerGravity;
        [SerializeField] private float playerJumpCooldown;

        [Header("Layer Mask Settings")] 
        [SerializeField] private LayerMask groundMask;
        [SerializeField] private LayerMask whatIsWall;
        [SerializeField] private LayerMask raycastLayers;

        [Header("Wall Run Settings")] 
        [SerializeField] private float wallRunSpeed;
        [SerializeField] private float wallRunForce;
        [SerializeField] private float wallRunMaxDuration;
        [SerializeField] private float wallRunExitTime;
        [SerializeField] private float wallRunCooldown;

        [Header("Wall Run Detection Settings")] 
        [SerializeField] private float maxWallDistance;

        [Header("Wall Jump Settings")] 
        [SerializeField] private float wallJumpUpForce;
        [SerializeField] private float wallJumpSideForce;
        [SerializeField] private float wallMemoryTime;
        [SerializeField] private float wallJumpCooldown;
        
        [Header("Sliding Settings")] 
        [SerializeField] private float maxSlideTime;
        [SerializeField] private float slideForce;
        [SerializeField] private float slideYScale;
        [SerializeField] private float slideCooldown;

        [Header("Interact Settings")] 
        [SerializeField] private float maxInteractDistance;

        [Header("Weapons")] 
        [SerializeField] private Pistol pistol; 
        [SerializeField] private Shotgun shotgun;

        [Header("Tutorial Settings")] 
        [SerializeField] private bool isTutorial;
        [SerializeField] private GameObject lineRender;

        #endregion

        #region Public References to private vars

        public Transform PlayerTransform => characterController.transform;
        public float JumpHeight => playerJumpHeight;
        public float PlayerSpeed => playerSpeed;
        public float SlideCooldown => slideCooldown;
        public float PlayerGravity => playerGravity;
        public float WallRunForce => wallRunForce;
        public LayerMask WhatIsWall => whatIsWall;
        public float MaxSlideTime => maxSlideTime;
        public float SlideForce => slideForce;
        public float SlideYScale => slideYScale;
        public float JumpCooldown => playerJumpCooldown;
        public float WallRunCooldown => wallRunCooldown;
        public float WallJumpCooldown => wallJumpCooldown;
        public float WallJumpUpForce => wallJumpUpForce;
        public float WallJumpSideForce => wallJumpSideForce;
        public float MaxWallDistance => maxWallDistance;
        public bool IsTutorial => isTutorial;
        public PlayerStateMachine PlayerFsm => _playerStateMachine;

        #endregion

        #region Public Vars

        public bool isGrounded;
        public bool canSlide;
        public bool canJump;
        public bool checkForWallsWhenAirborne;
        public bool canWallJump;
        public bool jumpingFromLeftWall;
        public bool jumpingFromRightWall;
        public bool leftWall;
        public bool rightWall;

        public RaycastHit JumpingLeftWallHit;
        public RaycastHit JumpingRightWallHit;
        public RaycastHit LeftWallHit;
        public RaycastHit RightWallHit;

        public CinemachineBrain activeCinemachineBrain;
        public TutorialController tutorialController;

        #endregion


        public void Awake()
        {
            audioSource = GetComponent<AudioSource>();
            characterController = GetComponentInChildren<CharacterController>();
            eventSystem = GameObject.FindGameObjectWithTag("EventSystem");
            mainCamera = eventSystem.GetComponent<MainCamera>();
            inputSystem = eventSystem.GetComponent<inputSystem>();
            playerMesh = transform.FindGameObjectInChildWithTag("PlayerMesh");
            playerInput = GetComponent<PlayerInput>();
            playerHealth = GetComponent<PlayerHealth>();
            canvasScript = GameObject.FindGameObjectWithTag("Canvas").GetComponent<CanvasScript>();
            _playerStateMachine = new PlayerStateMachine();
            activeCinemachineBrain = GetComponentInChildren<CinemachineBrain>();
            playerShooting = GetComponent<PlayerShooting>();
            if (isTutorial) tutorialController = eventSystem.GetComponent<TutorialController>();
            IdleState = new Idle("Idle", this, _playerStateMachine);
            WalkingState = new Walking("Walking", this, _playerStateMachine);
            JumpingState = new Jumping("Jumping", this, _playerStateMachine);
            WallRunState = new WallRunning(this, _playerStateMachine);
            AirborneState = new Airborne("Airborne", this, _playerStateMachine);
            SlidingState = new Sliding("Sliding", this, _playerStateMachine);
            WallJumpingState = new WallJumping(this, _playerStateMachine);
            playerInput.actions["Shoot"].performed += _ => playerShooting.Fire();
            playerInput.actions["Interact"].performed += _ => Interact();
            playerInput.actions["Reload"].performed += _ => playerShooting.Reload();
            canSlide = true;
            canJump = true;
            canWallJump = true;
            _playerStateMachine.Initialize(IdleState);
            Cursor.lockState = CursorLockMode.Locked;
            SetMouseSensitivity();
        }

        private void Update()
        {
            _playerStateMachine.CurrentState.HandleInput();
            _playerStateMachine.CurrentState.LogicUpdate();
        }


        private void SetMouseSensitivity()
        {
            mainCamera.SetSensitivity(mouseSensitivity);
        }

        private void FixedUpdate()
        {
            isGrounded = characterController.isGrounded;
            _playerStateMachine.CurrentState.PhysicsUpdate();
        }

        public static IEnumerator ActionCooldown(Action cooldownComplete, float timeToTake)
        {
            yield return new WaitForSeconds(timeToTake);
            cooldownComplete?.Invoke();
        }

        private void Interact()
        {
            activeCinemachineBrain.gameObject.TryGetComponent<Camera>(out var activeCam);
            var rayOrigin = new Ray(activeCam.transform.position, activeCam.transform.forward);
            if (!Physics.Raycast(rayOrigin, out var hit, maxInteractDistance)) return;
            switch (hit.transform.tag)
            {
                case "Pistol":
                    var collidedPistol = hit.transform.gameObject;
                    playerShooting.EquipWeapon(pistol);
                    pistol.gameObject.SetActive(true);
                    if (collidedPistol.TryGetComponent<Outline>(out _))
                        Destroy(collidedPistol.GetComponent<Outline>());
                    Destroy(collidedPistol);
                    if (isTutorial) tutorialController.PistolCollected();
                    break;
                case "Shotgun":
                    var collidedShotgun = hit.transform.gameObject;
                    playerShooting.EquipWeapon(shotgun);
                    shotgun.gameObject.SetActive(true);
                    Destroy(collidedShotgun);
                    break;
                case "Computer":
                    tutorialController.ComputerInteracted();
                    break;
            }
        }
    }
}
```
{collapsible="true" collapsed-title="PlayerController.cs"}

## Public Methods
{type="wide" sorted="asc"}
ActionCooldown()
: Waits for a specified amount of time, then invokes the cooldown action passed into the function.

## Private Methods

{type="wide" sorted="asc"}
Awake()
: Gets all the required components for the script to function. Also creates the states for the Player State Machine, and creates the bindings linked in with the Input System.

Update()
: Calls the current state within the Player State Machine to handle Player Input and Logic Updates.

SetMouseSensitivity()
: Calls the Main Camera within the scene and sets the mouse sensitivity.

FixedUpdate()
: Checks if the player is grounded, and calls the current state within the Player State Machine to handle Physics Updates.

Interact()
: Handles the player interacting with objects within the scene. Raycasts, then checks the tag of the hit object, and does the relevant logic.

FindGameObjectInChildWithTag()
: Static function that finds a child object of a specified Game Object with a matching tag.

## Variables


### Components
{type="wide" sorted="asc"}
eventSystem
: The event system Game Object that handles logic within the scene.

inputSystem
: The component that handles Player Input.

playerInput
: The input map for the Player Controls.

mainCamera
: The Main Camera script that controls the Games camera components.

characterController
: The character controller component attached to the player Game Object.

playerMesh
: The Player's Mesh / visual model.

playerShooting
: The script that controls the players weapon system.

playerHealth
: The script that manages the players health system.

canvasScript
: The script with is used to handle UI elements within the scene.

audioSource
: The Audio Source component attached to the player.

### Player States
{type="wide" sorted="asc"}
_playerStateMachine
: The script that controls the current player state.

IdleState
: The Idle state script for the player.

WalkingState
: The Walking state script for the player.

SprintingState
: The Sprinting state script for the player.

JumpingState
: The Jumping state script for the player.

AirborneState
: The Airborne state script for the player.

WallJumpingState
: The Wall Jumping state script for the player.

WallRunState
: The Wall Running state script for the player.

SlidingState
: The Sliding state script for the player.

### Player Settings

#### Player Movement
{type="wide" sorted="asc"}
playerSpeed
: The value of the player's movement speed.

sprintingSpeed
: The value of the player's sprinting speed.

#### Player Look
{type="wide" sorted="asc"}
mouseSensitivity
: The mouse sensitivity of the cameras in-game.

xClamp
: The limits of the X axis for the camera.


#### Player Jump
{type="wide" sorted="asc"}
playerJumpHeight
: How high the player can jump.

playerGravity
: The strength of gravity for the player.

playerJumpCooldown
: The time determining the length when the player can jump again.

#### Layer Masks
{type="wide" sorted="asc"}
whatIsWall
: A LayerMask to determine what walls can be wall ran on.

raycastLayers
: The Layers in-game that can receive raycasts.


#### Wall Running
{type="wide" sorted="asc"}
wallRunSpeed
: The speed that the player can run walls at.

wallRunForce
: The force applied onto the side of the player during wall running.

wallRunMaxDuration
: The length of how long the player can wall run for.

wallRunCooldown
: The time determining when the player can wall run again.

maxWallDistance
: The max distance between the player and the walls that can be detected during ray casting.

#### Wall Jumping
{type="wide" sorted="asc"}
wallJumpUpForce
: The amount of force applied to the player's Y Axis when wall jumping.

wallJumpSideForce
: The amount of force applied to the player's X Axis when wall jumping.

wallJumpCooldown
: The time determining when the player can wall jump again.

#### Sliding
{type="wide" sorted="asc"}
maxSlideTime
:  The max time the player is able to slide for.

slideForce
: The force applied onto the player when sliding.

slideYScale
: The scale applied to the Y Axis of the player when sliding.

slideCooldown
: The time determining when the player can slide again.

#### Interaction
{type="wide" sorted="asc"}
maxInteractDistance
: The max distance for raycasts.


#### Weapons
{type="wide" sorted="asc"}
pistol
: The pistol object childed to the player.

shotgun
: The shotgun object childed to the player.

#### Tutorial
{type="wide" sorted="asc"}
isTutorial
: Determines if the player is in the tutorial stage or not.

### Encapsulated Public Variables

#### Player
{type="wide" sorted="asc"}
PlayerTransform
: Reference to the Character Controllers transform component.

PlayerGravity
: Reference to the player's gravity.

PlayerSpeed
: Reference to the player's speed.

#### Wall Run Settings
{type="wide" sorted="asc"}
WallRunForce
: Reference to the player's wall run force.

WallRunSpeed
: Reference to the player's wall run speed.

WhatIsWall
: Reference to the layer mask determining what can be wall ran on.

#### Slide Settings
{type="wide" sorted="asc"}
SlideCooldown
: Reference to the player's slide cooldown.

MaxSlideTime
: Reference to the player's max slide time.

SlideForce
: Reference to the player's slide force.

SlideYScale
: Reference to the player's Y Scale when sliding.

#### Jump Settings
{type="wide" sorted="asc"}
JumpHeight
: Reference to the player's jump height.

JumpCooldown
: Reference to the player's jump cooldown length.

WallRunCooldown
: Reference to the player's wall run cooldown length.

WallJumpCooldown
: Reference to the player's wall jump cooldown length.

WallJumpUpForce
: Reference to the player's wall jump Y Axis force.

WallJumpSideForce
: Reference to the player's wall jump X Axis force.

#### Interaction Settings
{type="wide" sorted="asc"}
MaxWallDistance
: Reference to the max wall distance from the player.

#### Tutorial Settings
{type="wide" sorted="asc"}
IsTutorial
: Reference to the check determining if the player is in a tutorial.


#### Player State Machine 
{type="wide" sorted="asc"}
PlayerFsm
: Reference to the player's state machine.

### Public Variables
{type="wide" sorted="asc"}
isGrounded
: Reference to if the player is grounded.

canSlide
: Reference to if the player can slide.

canJump
: Reference to if the player can jump.

checkForWallsWhenAirborne
: Reference to if the script should detect walls when the player is airborne.

canWallRun
: Reference to if the player can wall run.

canWallJump
: Reference to if the player can wall jump.

jumpingFromLeftWall
: Reference to if the player is jumping from a wall on the left.

jumpingFromRightWall
: Reference to if the player is jumping from a wall on the right.

leftWall
: Reference to if a wall is detected on the left of the player.

rightWall
: Reference to if a wall is detected on the right of the player.

JumpingLeftWallHit
: The RaycastHit value of the wall detected to the left of the player while wall jumping.

JumpingRightWallHit
: The RaycastHit value of the wall detected to the right of the player while wall jumping.

LeftWallHit
: The RaycastHit value of the wall detected to the left of the player.

RightWallHit
: The RaycastHit value of the wall detected to the right of the player.

activeCinemachineBrain
: The active Cinemachine Brain component in the scene.

tutorialController
: The tutorial controller within the tutorial scene.


