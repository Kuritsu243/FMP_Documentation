# Walking.cs

## Description

This script controls the Walking state of the player.

```C#
using System;
using Cameras;
using Unity.Cinemachine;
using UnityEngine;

namespace Player.FSM.States
{
    public class Walking : FsmState
    {
        private float gravityValue;
        private float playerSpeed;
        private bool isJumping;
        private bool isSliding;
        private bool isGrounded;
        private bool isMoving;
        private Vector2 movementInput;
        private Vector3 playerVelocity;
        private Vector3 verticalVelocity;
        private Transform PlayerTransform => Character.PlayerTransform;


        
        
        public Walking(string stateName, PlayerController playerController, FiniteStateMachine stateMachine) : base(stateName, stateMachine, playerController)
        {
            StateName = stateName;
            Character = playerController;
            StateMachine = stateMachine;
        }

        public override void Enter()
        {
            base.Enter();


            isMoving = true;
            isJumping = false;
            isSliding = false;
            isGrounded = true;
            playerSpeed = Character.PlayerSpeed;
            gravityValue = Character.PlayerGravity;
        }

        public override void HandleInput()
        {
            base.HandleInput();

            isJumping = JumpAction.IsPressed();
            isSliding = SlideAction.IsPressed();
            
            if (movementInput is {x: 0, y: 0})
                isMoving = false;
            movementInput = MoveAction.ReadValue<Vector2>();
            playerVelocity = (PlayerTransform.right * movementInput.x +
                              PlayerTransform.forward * movementInput.y) * playerSpeed;
            
        }

        public override void LogicUpdate()
        {
            base.LogicUpdate();
            
            
            if (isJumping && Character.canJump)
                StateMachine.ChangeState(Character.JumpingState);
            if (!isMoving)
                StateMachine.ChangeState(Character.IdleState);
            if (isSliding && Character.canSlide)
                StateMachine.ChangeState(Character.SlidingState);

        }

        public override void PhysicsUpdate()
        {
            base.PhysicsUpdate();
            
            
            verticalVelocity.y += gravityValue * Time.deltaTime;
            isGrounded = Character.isGrounded;

            if (isGrounded && verticalVelocity.y < 0)
                verticalVelocity.y = 0f;

            Character.characterController.Move(playerVelocity * Time.deltaTime + verticalVelocity * Time.deltaTime);
        }

        public override void Exit()
        {
            base.Exit();
           
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