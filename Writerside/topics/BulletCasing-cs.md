# BulletCasing.cs

## Description

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