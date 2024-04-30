# ThirdPersonState.cs
<show-structure depth="2" />

## Description
The script to be used when the camera is in the third-person state.
## Script
```C#
using Unity.Cinemachine;

namespace Cameras.FSM.States
{
    public class ThirdPersonState : CameraState
    {
        
        public ThirdPersonState(string stateName, CameraStateMachine stateMachine, CameraController cameraController, CinemachineCamera stateCamera) : base(stateMachine, cameraController, stateCamera)
        {
            StateMachine = stateMachine;
            CameraController = cameraController;
            StateCamera = stateCamera;
        }
        public override void LogicUpdate()
        {
            base.LogicUpdate();
            
            if (StateMachine.CurrentState.IsChangingPerspective)
                StateMachine.ChangeState(CameraController.FirstPersonState);
        }
        
    }
}
```
{collapsible="true" collapsed-title="ThirdPersonState.cs"}

## Public Methods
{type="wide" sorted="asc"}
ThirdPersonState()
: Stores references to the components required by this script.

LogicUpdate()
: Checks if the camera is changing perspective.
If so then change the camera state machine to the first-person state.