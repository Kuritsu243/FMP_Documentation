# WallRunning.cs
<show-structure depth="2" />

## Description

This script controls the wall running state of the player.

## Script
```C#
using Unity.Cinemachine;
using UnityEngine;

namespace Player.FSM.States
{
    public class WallRunning : FsmState
    {
        private bool _isGrounded;
        private bool _isJumping;
        private Vector2 _mouseInput;
        private Vector2 _movementInput;
        private Vector3 _verticalVelocity;
        private float _mouseX;
        private float _mouseY;
        private float _xRotation;
        private Vector3 _targetRotation;
        private CinemachineCamera _thirdPersonCam;
        private CinemachineCamera _firstPersonCam;
        private LayerMask _whatIsWall;
        private float _wallRunForce;
        private float _wallRunMaxDuration;
        private float _wallRunExitTime;
        private float _wallRunSpeed;
        private RaycastHit _leftWallHit;
        private RaycastHit _rightWallHit;
        private bool _leftWall;
        private bool _rightWall;
        private bool _isExitingWall;
        private bool _exitWallTimerExceeded;
        private bool _exitWallTimerActive;
        private float _maxWallDistance;

        private Transform PlayerTransform => Character.PlayerTransform;


        public WallRunning(PlayerController playerController, FiniteStateMachine stateMachine) : base(stateMachine,
            playerController)
        {
            Character = playerController;
            StateMachine = stateMachine;
        }

        public override void Enter()
        {
            base.Enter();

            _isGrounded = false;
            _wallRunForce = Character.WallRunForce;
            _maxWallDistance = Character.MaxWallDistance;
            _whatIsWall = Character.WhatIsWall;

            if (Character.leftWall)
            {
                _leftWallHit = Character.LeftWallHit;
                _leftWall = true;
            }
            else if (Character.rightWall)
            {
                _rightWallHit = Character.RightWallHit;
                _rightWall = true;
            }
        }

        public override void HandleInput()
        {
            base.HandleInput();

            _isJumping = JumpAction.IsPressed();
            _movementInput = MoveAction.ReadValue<Vector2>();
        }

        public override void LogicUpdate()
        {
            base.LogicUpdate();

            if (!_leftWall && !_rightWall && !_isGrounded)
                StateMachine.ChangeState(Character.AirborneState);
            if (_isJumping && Character.canWallJump)
                StateMachine.ChangeState(Character.WallJumpingState);
        }

        public override void PhysicsUpdate()
        {
            base.PhysicsUpdate();
            _isGrounded = Character.isGrounded;
            var right = PlayerTransform.right;
            var position = PlayerTransform.position;
            _rightWall = Physics.Raycast(position, right, out _rightWallHit, _maxWallDistance, _whatIsWall);
            _leftWall = Physics.Raycast(position, -right, out _leftWallHit, _maxWallDistance, _whatIsWall);

            if (_rightWall)
                _leftWall = false;
            else if (_leftWall)
                _rightWall = false;


            if (_rightWall && _leftWall)
                Debug.LogError("Both walls have been detected. This is logically not meant to happen.");

            if (!_leftWall && !_rightWall) return;

            var wallNormal = _rightWall ? _rightWallHit.normal : _leftWallHit.normal;
            var wallForward = Vector3.Cross(wallNormal, PlayerTransform.up);
            if ((PlayerTransform.forward - wallForward).magnitude > (PlayerTransform.forward - -wallForward).magnitude)
                wallForward = -wallForward;
            Character.characterController.Move(wallForward * (_wallRunForce * Time.deltaTime));

            switch (_leftWall)
            {
                case true or true when _movementInput is not { x: 0, y: 0 } && !_isGrounded:
                {
                    if (_leftWall)
                    {
                        if (Character.IsTutorial && !TutorialController.WallRunChecks["FirstWall"])
                            TutorialController.WallRunChecks["FirstWall"] = true;
                        Character.leftWall = true;
                        Character.LeftWallHit = _leftWallHit;
                    }
                    else if (_rightWall)
                    {
                        if (Character.IsTutorial && TutorialController.WallRunChecks["FirstWall"] &&
                            !TutorialController.WallRunChecks["SecondWall"])
                            TutorialController.WallRunChecks["SecondWall"] = true;
                        Character.rightWall = true;
                        Character.RightWallHit = _rightWallHit;
                    }
                    
                    break;
                }
                case false when _rightWall && _movementInput is not { x: 0, y: 0 }:
                    if (Character.IsTutorial && TutorialController.WallRunChecks["FirstWall"] &&
                        !TutorialController.WallRunChecks["SecondWall"])
                        TutorialController.WallRunChecks["SecondWall"] = true;
                    Character.characterController.Move(-wallNormal * (100 * Time.deltaTime));
                    break;
            }
        }

        public override void Exit()
        {
            base.Exit();
            if (!_isJumping) return;
            if (_leftWall && !_rightWall)
            {
                Character.jumpingFromLeftWall = true;
                Character.jumpingFromRightWall = false;
                Character.rightWall = false;
                Character.leftWall = false;
                Character.JumpingLeftWallHit = _leftWallHit;
            }
            else if (_rightWall && !_leftWall)
            {
                Character.jumpingFromRightWall = true;
                Character.jumpingFromLeftWall = false;
                Character.leftWall = false;
                Character.rightWall = false;
                Character.JumpingRightWallHit = _rightWallHit;
            }
        }
    }
}
```
{collapsed-title="WallRunning.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
WallRunning()
: Stores a reference to the player controller script and the state machine controller.

Enter()
: Sets variable values to match the values on the Player Controller script, while also zeroing variables that may have been true before entering this state. Also checks which side of the player
that the wall they are running on is.

HandleInput()
: Reads for Player Movement input and updates the player's velocity accordingly.

LogicUpdate()
: If no walls on either side of the player are detected then change to the airborne state. If the player is jumping and can wall jump then change to the wall jumping state.

PhysicsUpdate()
: Updates if the player is grounded, and calculates the correct forces to apply on the play depending on the walls detected and their magnitudes. Then moves the play based upon
the force and forward values of the walls. If the player is in the tutorial level then it updates the wall run checks to indicate that the player has passed certain walls.

Exit()
: Indicates that the player is no longer wall running, if the player is jumping whilst wall running then it sets the values appropriate to what side of the player the wall
they are jumping from is.