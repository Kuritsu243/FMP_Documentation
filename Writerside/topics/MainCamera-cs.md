# MainCamera.cs
<show-structure depth="2" />

## Description
This script is used to control the main camera that will be used within the game.
## Script
```C#
using System;
using Unity.Cinemachine;
using UnityEngine;
using UnityEngine.Serialization;

namespace Cameras
{
    public class MainCamera : MonoBehaviour
    {
        [SerializeField] private CinemachineCamera firstPersonCam;
        [SerializeField] private CinemachineCamera thirdPersonCam;
        [FormerlySerializedAs("_cameraController")] [SerializeField] private CameraController cameraController;
        
        [Header("Testing")]
        [SerializeField] private bool isTesting;
        [Range(0, 360)] [SerializeField] private float xRotation;
        [Range(0, 360)] [SerializeField] private float yRotation;
        [Range(0, 360)] [SerializeField] private float zRotation;
        [Range(0, 360)] [SerializeField] private float dutch;
        [Range(0, 250)] [SerializeField] private float fov;
        [SerializeField] private bool lerpFOV;
        
        public static CameraChanger.CameraModes ActiveCameraMode => CameraChanger.GetActiveCamera();

        private static CinemachineCamera _previousCam;
        private static CinemachineCamera _activeCam;

        private CinemachineMouseLook _cinemachineMouseLook;
        public MainCamera Instance { get; private set; }

        
        private void Awake()
        {
            if (Instance != null && Instance != this)
                Destroy(gameObject);
            else
            {
                Instance = this;
            }
        }

        public static void SetActiveCamera(CinemachineCamera newCamera)
        {
            if (!_activeCam && !_previousCam) Debug.LogWarning("No active cam or previous cam assigned");
            else
            {
                _previousCam = _activeCam;
                _previousCam.Priority.Value = 0;
            }
            _activeCam = newCamera;
            _activeCam.Priority.Value = 10;
 

        }

        public static CameraChanger.CameraModes GetActiveMode()
        {
            return CameraChanger.GetActiveCamera();
        }
        
        public void SetSensitivity(float sensitivity)
        {
            if (!_cinemachineMouseLook)
                _cinemachineMouseLook = firstPersonCam.GetComponent<CinemachineMouseLook>();
            if (!_cinemachineMouseLook)
                throw new Exception("Cannot find the mouse look component!");
            _cinemachineMouseLook.UpdateSensAndSmoothing(sensitivity);

        }

        public static void DoFov(float endValue, float timeToTake)
        {
            switch (ActiveCameraMode)
            {   
                case CameraChanger.CameraModes.FirstPerson:
                    CameraChanger.FirstPersonCam.LerpFirstFOV(endValue, timeToTake);
                    break;
                case CameraChanger.CameraModes.ThirdPerson:
                    CameraChanger.ThirdPersonCam.LerpThirdFOV(endValue, timeToTake);
                    break;
                default:
                    throw new ArgumentOutOfRangeException();
            }
        }

        public static void DoTilt(float endValue, float timeToTake)
        {
            switch (ActiveCameraMode)
            {
                case CameraChanger.CameraModes.FirstPerson:
                    CameraChanger.FirstPersonCam.LerpFirstDutch(endValue, timeToTake);
                    break;
                case CameraChanger.CameraModes.ThirdPerson:
                    CameraChanger.ThirdPersonCam.LerpThirdDutch(endValue, timeToTake);
                    break;
                default:
                    throw new ArgumentOutOfRangeException();
            }
        }        
    }
}

```
{collapsible="true" collapsed-title="MainCamera.cs"}

## Private Methods
{type="wide" sorted="asc"}
Awake()
: Checks if an instance of this script already exists in the game.
If it does, then it destroys the script currently checking.
If not, sets this script as the instance.

SetActiveCamera()
: Sets the active camera to the starting camera at the start of the game.
Sets the priority to 10, while setting the previous cameras priority to 0.
Will trigger a cinemachine transition between the two.

GetActiveMode()
: Returns the current active camera.

SetSensitivity()
: Calls the cinemachine mouse look script, and updates the mouse sensitivity.

DoFov()
: Tweens the camera's FOV.

DoTilt
: Tweens the camera's Dutch.

## Variables
{type="wide" sorted="asc"}
firstPersonCam
: The camera used for first-person perspective.

thirdPersonCam
: The camera used for third-person perspective.

cameraController
: The camera controller component.

isTesting
: Is the developer currently in testing mode.

xRotation
: the x rotation of the camera.

yRotation
: the y rotation of the camera.

zRotation
: the z rotation of the camera.

dutch
: The dutch value of the camera.

fov
: the FOV value of the camera.

lerpFov
: Should the game lerp the camera's FOV.

ActiveCameraMode
: Direct variable reference to the active camera.

_previousCam
: The previous active camera.

_activeCam
: The currently active camera.

_cinemachineMouseLook
: The cinemachine mouse look component.

Instance
: The instance of the main camera script.
