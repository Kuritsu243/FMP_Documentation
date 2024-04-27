# Sliding.cs
<show-structure depth="2" />

## Description

This script controls the Sliding state of the player.

## Script
```C#
using System.Collections;
using UnityEngine;

namespace Player.FSM.States
{
    public class Sliding : FsmState
    {
        private float _maxSlideTime;
        private float _slideForce;
        private float _slideYScale;
        private Transform _playerTransform;
        private float _playerSpeed;
        private Vector2 _movementInput;
        private Vector3 _playerVelocity;
        private Vector3 _slideVelocity;
        private bool _isSliding;
        private bool _isJumping;
        private Coroutine _slideTimer;
        private float _startYScale;

        public Sliding(string stateName, PlayerController playerController, FiniteStateMachine stateMachine) : base(stateMachine, playerController)
        {
            Character = playerController;
            StateMachine = stateMachine;
        }

        public override void Enter()
        {
            base.Enter();

            _isSliding = true;
            _isJumping = false;
            _maxSlideTime = Character.MaxSlideTime;
            _slideForce = Character.SlideForce;
            _slideYScale = Character.SlideYScale;
            _playerTransform = Character.PlayerTransform;
            _playerSpeed = Character.PlayerSpeed;
            _slideTimer = Character.StartCoroutine(SlideTimer());
            _startYScale = _playerTransform.localScale.y;

            var localScale = _playerTransform.localScale;
            localScale = new Vector3(localScale.x, _slideYScale, localScale.y);
            _playerTransform.localScale = localScale;
        }

        public override void HandleInput()
        {
            base.HandleInput();

            _movementInput = MoveAction.ReadValue<Vector2>();
            _playerVelocity = (_playerTransform.right * _movementInput.x +
                               _playerTransform.forward * _movementInput.y) * _playerSpeed;


            _isJumping = JumpAction.IsPressed();
        }

        public override void LogicUpdate()
        {
            base.LogicUpdate();


            switch (_isSliding)
            {
                case false when _movementInput is { x: 0, y: 0 }:
                    StateMachine.ChangeState(Character.IdleState);
                    break;
                case false when _movementInput is not { x: 0, y: 0 }:
                    StateMachine.ChangeState(Character.WalkingState);
                    break;
            }

            if (_isJumping)
                StateMachine.ChangeState(Character.JumpingState);
        }

        public override void PhysicsUpdate()
        {
            base.PhysicsUpdate();
            _slideVelocity = _playerVelocity.normalized * _slideForce;

            if (!_isSliding) return;
            Character.characterController.Move(_slideVelocity * Time.deltaTime);
        }

        public override void Exit()
        {
            base.Exit();
            _isSliding = false;
            Character.StopCoroutine(_slideTimer);
            Character.canSlide = false;
            Character.StartCoroutine(PlayerController.ActionCooldown(() => Character.canSlide = true,
                Character.SlideCooldown));
            var localScale = _playerTransform.localScale;
            localScale = new Vector3(localScale.x, _startYScale, localScale.z);
            _playerTransform.localScale = localScale;
        }

        private IEnumerator SlideTimer()
        {
            yield return new WaitForSeconds(_maxSlideTime);
            _isSliding = false;
        }
    }
}
```
{collapsed-title="Sliding.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
Sliding()
: Stores a reference to the player controller script and the state machine controller.

Enter()
: Sets the variable values to match the values on the Player Controller script, while also setting the scaling the Y axis of the player by the slide Y scale.

HandleInput()
: Reads for Player Movement input and updates the player's velocity accordingly. Also checks if the player is jumping.

LogicUpdate()
: Checks if the player is still sliding and the movement input, changes the current state accordingly.

PhysicsUpdate()
: Calculates the slide velocity of the player, them moves the player based on the value of the slide velocity.

Exit()
: Zeroes values, sets the sliding cooldown, and then restores the players scale on the Y axis to the value it was set to beforehand.

SlideTimer()
: Waits for the max duration the player is able to slide for. Then updates a variable to indicate that the player is no longer sliding.