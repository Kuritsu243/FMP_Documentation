# PlayerAnimation.cs

## Description
This script controls the animations for the player character model.


## Script
```C#
using UnityEngine;

namespace Player
{
    public class PlayerAnimation : MonoBehaviour
    {
        private PlayerController _playerController;
        private Animator _playerAnimator;
        private float _velocity;
        private Vector3 _previousPos;
        private GameObject _playerMesh;
        private static readonly int Velocity = Animator.StringToHash("Velocity");
        private static readonly int HasWeapon = Animator.StringToHash("hasWeapon");

        private void Start()
        {
            _playerController = GetComponent<PlayerController>();
            _playerMesh = _playerController.playerMesh;
            _playerAnimator = _playerMesh.GetComponent<Animator>();
        }


        private void LateUpdate()
        {
            _velocity = (_playerMesh.transform.position - _previousPos).magnitude / Time.deltaTime;
            _previousPos = _playerMesh.transform.position;
            _playerAnimator.SetBool(HasWeapon, _playerController.playerShooting.CurrentWeapon);
            _playerAnimator.SetFloat(Velocity, _velocity);
        }
    }
}
```
{collapsible="true" collapsed-title="PlayerAnimation.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets the relevant components required for the script to function

LateUpdate()
: Calculates the players velocity and updates the Animator components variables accordingly

## Variables
{type="wide" sorted="asc"} 
_playerController
: A variable reference to the Player Controller script.

_playerAnimator
: A variable reference to the Player Animator component.

_velocity
: A variable reference to the speed of the Player.

_previousPos
: A variable storing the previous position of the Player at the previous frame.

_playerMesh
: A variable reference to the Player Mesh GameObject.

Velocity
: a hashed reference to the Velocity variable within the Player Animator component.

HasWeapon
: a hashed reference to the hasWeapon variable within the Player Animator component.