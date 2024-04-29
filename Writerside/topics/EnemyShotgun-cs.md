# EnemyShotgun.cs
<show-structure depth="2" />

## Description

The script attached to the enemy shotguns.

## Script
```C#
using System.Collections.Generic;
using UnityEngine;

namespace Weapons.Enemy
{
    public class EnemyShotgun : EnemyBaseWeapon
    {
        public override void Fire()
        {
            if (weaponAction != WeaponState.Idle) return;
            if (weaponProjectile && shootingType == ShootingType.Projectile)
            {
                var pellets = new List<Quaternion>(shotgunPelletCount);
                for (var i = 0; i < shotgunPelletCount; i++) pellets.Add(Quaternion.Euler(Vector3.zero));
                for (var h = 0; h < shotgunPelletCount; h++)
                {
                    pellets[h] = Random.rotation;
                    var pellet = enemyProjectilePool.GetPooledProjectile();
                    var pelletScript = pellet.GetComponent<EnemyProjectile>();
                    if (pellet != null)
                    {
                        pellet.transform.position = spawnPosition.position;
                        pellet.transform.rotation = spawnPosition.rotation;
                        pellet.SetActive(true);
                    }
                    pelletScript.Initialize(weaponDamage, ProjectileSpeed, ProjectileDespawnTime,
                        spawnPosition.transform.forward + GetWeaponSpread(spawnPosition.transform));
                }
            }
            base.Fire();
        }
    }
}

```
{collapsible="true" collapsed-title="EnemyShotgun.cs"}

## Public Methods
{type="wide" sorted="asc"}
Fire()
: Checks if the weapon state is not idle. Then verifies that the firing time is projectile, and not hitscan. Creates a new list
of Rotations for the pellets, and adds them to a list. Proceeds to randomise the rotation of each pellet, get an available pooled projectile,
and then initializes them.