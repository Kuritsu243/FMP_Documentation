# EnemyPistol.cs
<show-structure depth="2" />

## Description

This is the script used for the enemy pistol within my game. It is similar to the player pistol script, however, functions
oppositely - being able to affect the player instead of the enemy objects.

## Script
```C#
using Player;
using UnityEngine;

namespace Weapons.Enemy
{
    public class EnemyPistol : EnemyBaseWeapon
    {
        public override void Fire()
        {
            if (weaponAction != WeaponState.Idle) return;
            if (Physics.Raycast(spawnPosition.position, spawnPosition.forward * 10, out RaycastHit hit, weaponRange) &&
                shootingType == ShootingType.Hitscan)
            {
                switch (hit.transform.root.tag)
                {
                    case "Player":
                        var collidedPlayer = hit.transform.root.gameObject;
                        collidedPlayer.GetComponent<PlayerHealth>().Damage(weaponDamage);
                        break;
                }
            }
            base.Fire();
        }
        
        
    }
}
```
{collapsed-title="EnemyPistol.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
Fire()
: Called when an enemy shoots. Fires a raycast and then checks if the raycast hits the player. If it does, it called the `Damage()` function
attached to the player health component.