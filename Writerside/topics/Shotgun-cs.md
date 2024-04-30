# Shotgun.cs
<show-structure depth="2" />

## Description

## Script
```C#
using System.Collections.Generic;
using AI;
using UnityEngine;

namespace Weapons
{
    public class Shotgun : BaseWeapon
    {
        public override void Fire()
        {
            if (weaponAction != WeaponState.Idle) return;
            var direction = GetWeaponSpread(spawnPosition);
            if (Physics.Raycast(spawnPosition.position, direction, out RaycastHit hit, weaponRange) &&
                shootingType == ShootingType.Hitscan)
            {
                switch (hit.transform.root.tag)
                {
                    case "Enemy":
                        var collidedEnemy = hit.transform.root.gameObject;
                        collidedEnemy.GetComponent<EnemyHealth>().Damage(weaponDamage);
                        break;
                }
            }
            else if (weaponProjectile && shootingType == ShootingType.Projectile)
            {
                var pellets = new List<Quaternion>(shotgunPelletCount);
                for (var i = 0; i < shotgunPelletCount; i++) pellets.Add(Quaternion.Euler(Vector3.zero));
                for (var h = 0; h < shotgunPelletCount; h++)
                {
                    pellets[h] = Random.rotation;
                    var pellet = Instantiate(weaponProjectile, spawnPosition.position, spawnPosition.rotation);
                    pellet.transform.rotation =
                        Quaternion.RotateTowards(pellet.transform.rotation, pellets[h], weaponSpread.x);
                    var pelletScript = pellet.GetComponent<Projectile>();
                    pelletScript.Initialize(weaponDamage, ProjectileSpeed, ProjectileDespawnTime, spawnPosition.transform.forward + GetWeaponSpread(spawnPosition.transform));
                }
            }

            Instantiate(shotgunBulletCasing, bulletCasingSpawnPos.position, transform.rotation);
            base.Fire();
        }
        
    }
}
```
{collapsed-title="Shotgun.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}

Fire()
: Checks if the weapon is currently idle.
Calculates weapon spread.
If shotgun is set to raycast then fires a raycast and checks if it has hit the enemy.
If it has, then will call the Damage function on the EnemyHealth component.
If projectile mode, then creates a new list of pellets,
randomises the rotation, and for each one will apply randomised rotation.
Then gets the projectile script attached to them,
and calls the Initialize function, with the relevant values needed for the projectile to work.
Finally, instantiates the bullet casing.