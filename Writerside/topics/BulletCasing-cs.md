# BulletCasing.cs
<show-structure depth="2" />

## Description

The script handling the bullet casing to be spawned once the weapon is fired.

## Script
```C#
using UnityEngine;

namespace Weapons
{
    [RequireComponent(typeof(Rigidbody))]
    public class BulletCasing : MonoBehaviour
    {
        private Rigidbody _rigidbody;

        private void Start()
        {
            Invoke(nameof(Despawn), 2f);
            if (TryGetComponent(out _rigidbody))
                _rigidbody.velocity = transform.TransformDirection(Vector3.right * 5f);
        }

        private void Despawn()
        {
            Destroy(gameObject);
        }
    }
}
```
{collapsible="true" collapsed-title="BulletCasing.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Invokes the despawn function, and tries to get the rigidbody component. Sets the velocity of the pistol spawn casing to the right hand side of the weapon with a multiplier.

Despawn()
: Destroys the game object.

## Variables
{type="wide" sorted="asc"}
_rigidbody
: The rigidbody component attached to the bullet casing.