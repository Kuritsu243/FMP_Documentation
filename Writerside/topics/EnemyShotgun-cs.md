# EnemyShotgun.cs

## Description

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