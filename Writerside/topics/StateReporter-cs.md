# StateReporter.cs
<show-structure depth="2" />

## Description
This script was used to visualise both the player state and the camera state.
## Script
```C#
using UnityEngine;
using Cameras;
using Player;
using TMPro;

namespace Debugging
{
    public class StateReporter : MonoBehaviour
    {
        [SerializeField] private CameraController cameraController;
        [SerializeField] private PlayerController playerController;

        private TextMeshProUGUI _text;

        private void Start()
        {
            _text = GetComponent<TextMeshProUGUI>();
        }

        private void FixedUpdate()
        {
            _text.text = $"Player State: {playerController.PlayerFsm.CurrentState}\n" +
                         $"Camera State: {cameraController.CameraFsm.CurrentState}";
        }
    }
}
```
{collapsible="true" collapsed-title="StateReporter.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Get the text component to be updated.

FixedUpdate()
: Updates the text, displaying the player state and the camera state.

## Variables
{type="wide" sorted="asc"}
cameraController
: The camera controller component attached to the cameras.

playerController
: The player controller component attached to the player.