# ShowMoveState.cs
<show-structure depth="2" />

## Description
This script was used to display the current move state the player's finite state machine was in.
## Script
```C#
using Player;
using TMPro;
using UnityEngine;

namespace Debugging
{
    public class ShowMoveState : MonoBehaviour
    {

        private TextMeshProUGUI _moveStateText;
        private PlayerMovement _playerMovement;

        private void Start()
        {
            GameObject.FindGameObjectWithTag("Player").GetComponent<PlayerController>();
            _moveStateText = GetComponentInChildren<TextMeshProUGUI>();
        }

        private void FixedUpdate()
        {
            _moveStateText.text = _playerMovement.GetPlayerMovementState().ToString();
        }
    }
}
```
{collapsed-title="ShowMoveState.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets the player controller, and the text to be updated.

FixedUpdate()
: Updates the text according to the players current movement state.