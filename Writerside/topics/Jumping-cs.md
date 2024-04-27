# Jumping.cs
<show-structure depth="2" />

## Description
This script controls the Jumping state of the player.

## Script
```C#
using Unity.Cinemachine;
using UnityEngine;

namespace Player.FSM.States
{
    public class Jumping : FsmState
    {
        private float _gravityValue;
        private float _playerSpeed;
        private float _playerJumpHeight;
        private bool _isGrounded;
        private Vector2 _mouseInput;
        private Vector2 _movementInput;
        private Vector3 _playerVelocity;
        private Vector3 _verticalVelocity;
        private float _mouseX;
        private float _mouseY;
        private float _xRotation;
        private float _maxWallDistance;
        private LayerMask _whatIsWall;
        private Vector3 _targetRotation;
        private CinemachineCamera _thirdPersonCam;
        private CinemachineCamera _firstPersonCam;
        private RaycastHit _leftWallHit;
        private RaycastHit _rightWallHit;
        private bool _leftWall;
        private bool _rightWall;
        private bool _canWallRun;
        private Transform PlayerTransform => Character.PlayerTransform;


        public Jumping(string stateName, PlayerController playerController, FiniteStateMachine stateMachine) : base(stateMachine, playerController)
        {
            Character = playerController;
            StateMachine = stateMachine;
        }

        public override void Enter()
        {
            base.Enter();
            _canWallRun = false;
            _isGrounded = false;
            _playerSpeed = Character.PlayerSpeed;
            _gravityValue = Character.PlayerGravity;
            _playerJumpHeight = Character.JumpHeight;
            _verticalVelocity = Vector3.zero;
            _maxWallDistance = Character.MaxWallDistance;
            _whatIsWall = Character.WhatIsWall;
            Jump();
        }

        private void Jump()
        {
            _verticalVelocity.y = Mathf.Sqrt(-2f * _playerJumpHeight * _gravityValue);
        }

        public override void HandleInput()
        {
            base.HandleInput();


            SlideAction.IsPressed();
            _movementInput = MoveAction.ReadValue<Vector2>();
            _playerVelocity = (PlayerTransform.right * _movementInput.x +
                               PlayerTransform.forward * _movementInput.y) * _playerSpeed;
        }

        public override void LogicUpdate()
        {
            base.LogicUpdate();

            switch (_isGrounded)
            {
                case false when (_leftWall || _rightWall) && _canWallRun:
                    StateMachine.ChangeState(Character.WallRunState);
                    break;
                case true when _movementInput is not { x: 0, y: 0 } && _verticalVelocity.y < 0:
                    StateMachine.ChangeState(Character.IdleState);
                    break;
                case true when _movementInput is { x: 0, y: 0 } && _verticalVelocity.y < 0:
                    StateMachine.ChangeState(Character.WalkingState);
                    break;
                case false when _verticalVelocity.y <= 0:
                    StateMachine.ChangeState(Character.AirborneState);
                    break;
            }
        }

        public override void PhysicsUpdate()
        {
            base.PhysicsUpdate();
            _isGrounded = Character.isGrounded;
            Character.characterController.Move(_playerVelocity * Time.deltaTime + _verticalVelocity * Time.deltaTime);
            if (!_isGrounded) _verticalVelocity.y += _gravityValue * Time.deltaTime;


            var right = PlayerTransform.right;
            var position = PlayerTransform.position;
            _rightWall = Physics.Raycast(position, right, out _rightWallHit, _maxWallDistance, _whatIsWall);
            _leftWall = Physics.Raycast(position, -right, out _leftWallHit, _maxWallDistance, _whatIsWall);

            if ((!_leftWall && !_rightWall) || _movementInput is { x: 0, y: 0 } || _isGrounded) return;
            if (_leftWall && !_rightWall)
            {
                Character.leftWall = true;
                Character.rightWall = false;
                Character.LeftWallHit = _leftWallHit;
            }
            else if (_rightWall && !_leftWall)
            {
                Character.rightWall = true;
                Character.leftWall = false;
                Character.RightWallHit = _rightWallHit;
            }

            _canWallRun = true;
        }

        public override void Exit()
        {
            base.Exit();
            _isGrounded = true;

            if (_leftWall && !_rightWall)
            {
                Character.leftWall = true;
                Character.rightWall = false;
                Character.LeftWallHit = _leftWallHit;
            }
            else if (_rightWall && !_leftWall)
            {
                Character.rightWall = true;
                Character.leftWall = false;
                Character.RightWallHit = _rightWallHit;
            }
        }
    }
}
```
{collapsible="true" collapsed-title="Jumping.cs"}

## Public Methods
{type="wide" sorted="asc"}
Jumping()
: Stores a reference to the player controller script and the state machine controller.

Enter()
: Sets variable values to match the values on the Player Controller script, while also resetting variable values that may have been different before entering this state,
then calls the Jump function.

HandleInput()
: Reads for Player Movement input and then updates the player's velocity accordingly.

LogicUpdate()
: Checks if the player is grounded, and then changes the state depending on this alongside the players movement input and vertical velocity,
while also making checks if the player can wall run and if either walls to the left or the right of the player has been detected.

PhysicsUpdate()
: Updates if the player is grounded, moves the player, calculates the players vertical velocity, ray casts to the left and right
of the player, if walls are detected, the player is moving & not grounded, then store references to the walls detected alongside the
RaycastHit variable and indicate that the player is able to wall run.

Exit()
: Sets the player to grounded, no longer jumping, and checks if walls are marked as true and update the values on the Player Controller script.

## Private Methods
{type="wide" sorted="asc"}
Jump()
: Sets the Y axis of the vertical velocity vector to the calculated value dependant on the players jump height and gravity.

## Variables

{type="wide" sorted="asc"}
gravityValue
: Reference to the player gravity.

playerSpeed
: Reference to the player's speed.

playerJumpHeight
: Reference to the player's jump height.

isJumping
: Is the player jumping.

isSliding
: Is the player sliding.

isGrounded
: Is the player grounded.

isMoving
: Is the player moving.

mouseInput
: The X and Y values of the player's mouse delta.

movementInput
: The X and Y values of the players movement input.

playerVelocity
: The X, Y and Z values of the players velocity.

verticalVelocity
: The X, Y and Z values of the players vertical velocity

_maxWallDistance
: The max length that can be reached between a player and a wall.

_whatIsWall
: The layer mask determining what objects can be wall ran on.

_leftWallHit
: Reference to the wall detected to the left of the player.

_rightWallHit
: Reference to the wall detected to the right of the player.

_leftWall
: Is left wall detected.

_rightWall
: Is right wall detected.

_canWallRun
: Can the player wall run.

PlayerTransform
: A reference to the transform of the player's Character Controller component.
