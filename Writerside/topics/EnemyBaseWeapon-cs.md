# EnemyBaseWeapon.cs

## Description

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