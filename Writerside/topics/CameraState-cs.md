# CameraState.cs
<show-structure depth="2" />

## Description
This is the base class to be used by the different states of the camera's state machine.
## Script

```C#
using Unity.Cinemachine;

namespace Cameras.FSM
{
    public abstract class CameraState
    {
        protected CameraController CameraController;
        protected CameraStateMachine StateMachine;

        public bool IsChangingPerspective { get; set; }

        protected CinemachineCamera StateCamera { get; set; }


        protected CameraState(CameraStateMachine stateMachine, CameraController cameraController,
            CinemachineCamera stateCamera)
        {
            StateMachine = stateMachine;
            CameraController = cameraController;
            StateCamera = stateCamera;
            CameraController.playerInput.actions["Perspective"].performed += _ => PerspectiveChange();
        }

        public void Enter()
        {
            MainCamera.SetActiveCamera(StateCamera);
        }

        public void HandleInput()
        {
        }

        public virtual void LogicUpdate()
        {
        }

        public void PhysicsUpdate()
        {
        }

        private void PerspectiveChange()
        {
            StateMachine.CurrentState.IsChangingPerspective = true;
        }

        public void Exit()
        {
            IsChangingPerspective = false;
        }
    }
}
```
{collapsed-title="CameraState.cs" collapsible="true"}

## Protected Methods
{type="wide" sorted="asc"}
CameraState()
: Stores a reference to the required components, while also setting up a bind to change camera perspectives

## Public Methods
{type="wide" sorted="asc"}
Enter()
: Sets the active camera to be the state camera 

HandleInput()
: Does nothing, overridden by the states.

LogicUpdate()
: Does nothing, overridden by the states.

PhysicsUpdate()
: Does nothing, overridden by the states.

PerspectiveChange()
: Indicates that the camera is changing perspective.

Exit()
: Indicates that the camera is no longer changing perspective.

## Variables
{type="wide" sorted="asc"}
CameraController
: The script that controls the camera.

StateMachine
: The script that controls the camera state machine.

IsChangingPerspective
: Is the player changing camera perspective.

StateCamera
: The camera to be used for each specific state.