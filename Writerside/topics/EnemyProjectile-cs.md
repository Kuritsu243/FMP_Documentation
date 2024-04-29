# EnemyProjectile.cs
<show-structure depth="2" />

## Description

Script attached to all projectiles fired by the enemy, if the weapon is set up to be projectile instead of hit scan.

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

## Public Methods
{type="wide" sorted="asc"}
OnTriggerEnter()
: Checks if the player has collided with projectile. Attempts to get the player health component and called the Damage function, 
with the projectiles damage value as an input. If it hits an enemy, tells the game to ignore the collision.