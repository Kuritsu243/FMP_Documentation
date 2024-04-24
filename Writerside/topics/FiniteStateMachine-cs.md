# FiniteStateMachine.cs

## Description
This script defines the Player's Finite State Machine system, and handles the initialisation & changing of the active state.

## Script
```C#
using System;
using UnityEngine;

namespace Player.FSM
{
    public abstract class FiniteStateMachine
    {
        private FsmState initialState;
        
        public FsmState CurrentState { get; set; }
        public FsmState PreviousState { get; set; }
        
        

        public void Initialize(FsmState startingState)
        {
            CurrentState = startingState;
            CurrentState.Enter();
            
        }
        

        public void ChangeState(FsmState newState)
        {
            PreviousState = CurrentState;
            CurrentState?.Exit();
            CurrentState = newState;
            CurrentState?.Enter();
        }
        

    }
}
```
{collapsible="true" collapsed-title="FiniteStateMachine.cs"}

## Public Methods
{type="wide" sorted="asc"}
Initialize()
: Sets the current state of the Finite State Machine to the state passed through as an input, and then enters that state.

ChangeState()
: Changes the current state of the Finite State Machine. Stores a reference to the previous state, exits it, updates the current state and then enters the new state.

## Variables
{type="wide" sorted="asc"}
CurrentState
: The current state of the Finite State Machine.

PreviousState
: The previous state of the Finite State Machine.