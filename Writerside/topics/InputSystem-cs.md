# InputSystem.cs
<show-structure depth="2" />

## Description
This script is mostly unused due to the movement to a finite state machine.
All this script does is lock the cursor on begin play.
## Script

```C#
using UnityEngine;

namespace input
{
    public class inputSystem : MonoBehaviour
    {
        public inputSystem Instance { get; private set; }

        public float HorizontalInput => _movementInput.x;
        public float VerticalInput => _movementInput.y;

        public float MouseX => _mouseInput.x;
        public float MouseY => _mouseInput.y;


        private Vector2 _movementInput;
        private Vector2 _mouseInput;

        private void Awake()
        {
            if (Instance != null && Instance != this)
                Destroy(gameObject);
            else
            {
                Instance = this;
            }
        }


        private void OnEnable()
        {
            Cursor.lockState = CursorLockMode.Locked;
        }
        
    }
}
```
{collapsible="true" collapsed-title="InputSystem.cs"}