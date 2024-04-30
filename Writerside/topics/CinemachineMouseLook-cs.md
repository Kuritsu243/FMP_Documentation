# CinemachineMouseLook.cs
<show-structure depth="2" />

## Description
My modified version of the cinemachine input provider, to scale with the mouse sensitivity in-game.
## Script
```C#
using System;
using Unity.Cinemachine;
using UnityEngine;
using UnityEngine.InputSystem;
using Object = UnityEngine.Object;

namespace Cameras
{
    [ExecuteAlways]
    [SaveDuringPlay]
    [AddComponentMenu("Cinemachine/Helpers/Cinemachine Mouse Look")]
    public class CinemachineMouseLook : InputAxisControllerBase<CinemachineMouseLook.MouseReader>
    {
        // value that is read by the mouse reader class
        private static float _mouseSensitivity;
        // value visible in inspector
        [SerializeField] private float mouseSens;
        // input override
        [SerializeField] private PlayerInput playerInput;
        
        private void Awake()
        {
            // non-static to static var
            UpdateSensAndSmoothing(mouseSens);
            // attempt to get player input override
            if (!playerInput)
                TryGetComponent(out playerInput);
            if (!playerInput)
                Debug.LogError("Cannot find input component");
            else
            {
                playerInput.notificationBehavior = PlayerNotifications.InvokeCSharpEvents;
                playerInput.onActionTriggered += value =>
                {
                    foreach (var controller in Controllers)
                    {
                        controller.Input.ProcessInput(value.action);
                    }
                };
            }
        }

        public void UpdateSensAndSmoothing(float newSens)
        {
            mouseSens = newSens;
            _mouseSensitivity = newSens;
            foreach (var controller in Controllers)
            {
                controller.Driver.AccelTime = (float) (0.1 * (1 / newSens));
                controller.Driver.DecelTime = (float) (0.1 * (1 / newSens));
            }
        }
        
        private void FixedUpdate()
        {
            if (Application.isPlaying)
                UpdateControllers();
        }

        [Serializable]
        public sealed class MouseReader : IInputAxisReader
        {
            public InputActionReference inputActionReference;
            private Vector2 _value;

            public void ProcessInput(InputAction action)
            {
                if (!inputActionReference || inputActionReference.action.id != action.id) return;
                if (action.expectedControlType == "Vector2")
                    _value = action.ReadValue<Vector2>();
                else
                    _value.x = _value.y = action.ReadValue<float>();
            }
            
            public float GetValue(Object context, IInputAxisOwner.AxisDescriptor.Hints hint)
            {
                return hint == IInputAxisOwner.AxisDescriptor.Hints.Y
                    ? _value.y * -_mouseSensitivity
                    : _value.x * _mouseSensitivity;
            }
        }
  
    }
}
```
{collapsed-title="CinemachineMouseLook.cs" collapsible="true"}
## Private Methods
{type="wide" sorted="asc"}
Awake()
: Updates the sensitivity depending on the mouse sensitivity.
Gets the player input component, if it can't then will manually setup the input provider.

## Public Methods
{type="wide" sorted="asc"}
UpdateSensAndSmoothing()
: Stores a reference to the new sensitivity.
Updates the acceleration and deceleration time to the new sensitivity.

FixedUpdate()
: While playing the game is in play mode, constantly update the input value.

ProcessInput()
: Reads the value of the mouse input.

GetValue()
: Gets the value of the mouse input with the mouse sensitivity applied.

## Variables
{type="wide" sorted="asc"}
_mouseSensitivity
: the value of the mouse sensitivity to be read by the mouse reader class.

mouseSens
: The mouse sensitivity that can be set within the unity editor.

playerInput
: The player input component that this script requires.

