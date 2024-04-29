# EnemyBaseWeapon.cs
<show-structure depth="2" />

## Description
This script is the base class for the weapons used by the enemies in my game. 

## Script
```C#
namespace Weapons.Enemy
{
    
    public class EnemyBaseWeapon : BaseWeapon
    {
        public EnemyProjectilePool enemyProjectilePool;

        private void Awake()
        {
            enemyProjectilePool = transform.root.GetComponentInChildren<EnemyProjectilePool>();
        }
    }
}
```
{collapsible="true" collapsed-title="EnemyBaseWeapon.cs"}

## Private Methods

{type="wide" sorted="asc"}
Awake()
: Stores a reference to the enemy projectile pool, as I have set up objects pooling for my enemies projectiles.