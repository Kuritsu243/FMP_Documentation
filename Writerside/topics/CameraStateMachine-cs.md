# CameraStateMachine.cs
<show-structure depth="2" />

## Description
Base class to be used for the camera state machine.
## Script
```C#
using Cameras.FSM.States;
using UnityEngine;

namespace Cameras.FSM
{
    public abstract class CameraStateMachine
    {
        private CameraState _initialState;
        
        public CameraState CurrentState { get; private set; }
        
        public CameraState PreviousState { get; set; }

        public void Initialize(CameraState startingState)
        {
            CurrentState = startingState;
            startingState.Enter();
        }

        public void ChangeState(CameraState newState)
        {
            PreviousState = CurrentState;
            CurrentState?.Exit();
            CurrentState = newState;
            CurrentState?.Enter();
        }

    }

    public class CameraMachine : CameraStateMachine
    {
        [HideInInspector] public FirstPersonState FirstPersonState;
        [HideInInspector] public ThirdPersonState ThirdPersonState;
    }
}
```
{collapsible="true" collapsed-title="CameraStateMachine.cs"}

## Public Methods
{type="wide" sorted="asc"}
Initialize()
: Sets the current state to the starting state, and enters it.

ChangeState()
: Stores a reference to the current state, exits it, updates the current state to be the new state, and enters it.