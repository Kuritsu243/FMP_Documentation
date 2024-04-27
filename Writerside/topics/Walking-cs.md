# Walking.cs
<show-structure depth="2" />

## Description

This script controls the Walking state of the player.

```C#
using UnityEngine;

namespace Player.FSM.States
{
    public class Walking : FsmState
    {
        private float _gravityValue;
        private float _playerSpeed;
        private bool _isJumping;
        private bool _isSliding;
        private bool _isGrounded;
        private bool _isMoving;
        private Vector2 _movementInput;
        private Vector3 _playerVelocity;
        private Vector3 _verticalVelocity;
        private Transform PlayerTransform => Character.PlayerTransform;


        public Walking(string stateName, PlayerController playerController, FiniteStateMachine stateMachine) : base(stateMachine, playerController)
        {
            Character = playerController;
            StateMachine = stateMachine;
        }

        public override void Enter()
        {
            base.Enter();


            _isMoving = true;
            _isJumping = false;
            _isSliding = false;
            _isGrounded = true;
            _playerSpeed = Character.PlayerSpeed;
            _gravityValue = Character.PlayerGravity;
        }

        public override void HandleInput()
        {
            base.HandleInput();

            _isJumping = JumpAction.IsPressed();
            _isSliding = SlideAction.IsPressed();

            if (_movementInput is { x: 0, y: 0 })
                _isMoving = false;
            _movementInput = MoveAction.ReadValue<Vector2>();
            _playerVelocity = (PlayerTransform.right * _movementInput.x +
                               PlayerTransform.forward * _movementInput.y) * _playerSpeed;
        }

        public override void LogicUpdate()
        {
            base.LogicUpdate();


            if (_isJumping && Character.canJump)
                StateMachine.ChangeState(Character.JumpingState);
            if (!_isMoving)
                StateMachine.ChangeState(Character.IdleState);
            if (_isSliding && Character.canSlide)
                StateMachine.ChangeState(Character.SlidingState);
        }

        public override void PhysicsUpdate()
        {
            base.PhysicsUpdate();
            _verticalVelocity.y += _gravityValue * Time.deltaTime;
            _isGrounded = Character.isGrounded;

            if (_isGrounded && _verticalVelocity.y < 0)
                _verticalVelocity.y = 0f;

            Character.characterController.Move(_playerVelocity * Time.deltaTime + _verticalVelocity * Time.deltaTime);
        }
    }
}
```
{collapsed-title="Walking.cs" collapsible="true"}

## Public Methods
Walking()
: Stores a reference to the player controller script and the state machine controller.

Enter()
: Sets variable values to match the values on the Player Controller script, while also resetting variable values that may be different before entering this state.

HandleInput()
: Reads for Player Movement input and updates the player's velocity. Also checks if the player is jumping or sliding.

LogicUpdate()
: If Jumping and can jump then change to the jumping state, if not moving then change to idle state, if sliding and can slide then go to the sliding state.

PhysicsUpdate()
: Applies gravity to the players vertical velocity and updates the value to check if the player is grounded. If grounded then zero the vertical velocity. Moves the player.

## Variables
{type="wide" sorted="asc"}
gravityValue
: Reference to the player gravity.

playerSpeed
: Reference to the player's speed.

isJumping
: Is the player jumping.

isSliding
: Is the player sliding.

isGrounded
: Is the player grounded.

isMoving
: Is the player moving.

movementInput
: The X and Y values of the players movement input.

playerVelocity
: The X, Y and Z values of the players velocity.

verticalVelocity
: The X, Y and Z values of the players vertical velocity.

PlayerTransform
: A reference to the transform of the player's Character Controller component.