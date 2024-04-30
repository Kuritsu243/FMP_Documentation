# FirstPersonState.cs
<show-structure depth="2" />

## Description
The script to be used when the camera is in their first-person state.
## Script
```C#
using Unity.Cinemachine;

namespace Cameras.FSM.States
{
    public class FirstPersonState : CameraState
    {
        private bool _isChangingPerspective;
        public FirstPersonState(string stateName, CameraStateMachine stateMachine, CameraController cameraController, CinemachineCamera stateCamera) : base(stateMachine, cameraController, stateCamera)
        {
            StateMachine = stateMachine;
            CameraController = cameraController;
            StateCamera = stateCamera;
        }
        public override void LogicUpdate()
        {
            base.LogicUpdate();
            if (StateMachine.CurrentState.IsChangingPerspective)
                StateMachine.ChangeState(CameraController.ThirdPersonState);
        }
    }
}
```
{collapsed-title="FirstPersonState.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}

FirstPersonState()
: Stores references to the required components by the script.

LogicUpdate()
: Checks if the camera is changing perspective.
If so then change the camera state machine to the third-person state.

## Variables
{type="wide" sorted="asc"}
_isChangingPerspective
: Is the camera currently changing perspective
