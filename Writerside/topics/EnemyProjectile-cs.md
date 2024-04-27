# EnemyProjectile.cs
<show-structure depth="2" />

## Description

## Script
```C#
using Player;
using UnityEngine;

namespace Weapons.Enemy
{
    public class EnemyProjectile : Projectile
    {
        public override void OnTriggerEnter(Collider other)
        {
            switch (other.transform.tag)
            {
                case "PlayerMesh":
                case "Player":
                    Debug.LogWarning("Player hit!!!");
                    if (other.transform.root.TryGetComponent<PlayerHealth>(out var playerHealthScript))
                        playerHealthScript.Damage(ProjectileDamage);
                    Despawn();
                    break;
                case "enemyProjectile":
                case "Enemy":
                    Physics.IgnoreCollision(other, ProjectileCollider);
                    break;
            }
        }
    }
}

```
{collapsible="true" collapsed-title="EnemyProjectile.cs"}