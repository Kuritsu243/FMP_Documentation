# EnemyShooting.cs
<show-structure depth="2" />

## Description
The enemy shooting script, controlling the weapons used by the enemy.
## Script
```C#
using System;
using UnityEngine;
using Weapons.Enemy;

namespace AI
{
    public class EnemyShooting : MonoBehaviour
    {
        private enum WeaponType
        {
            Pistol,
            Shotgun
        }

        [SerializeField] private WeaponType weaponType;
        [SerializeField] private EnemyPistol pistol;
        [SerializeField] private EnemyShotgun shotgun;

        public EnemyBaseWeapon CurrentWeapon { get; private set; }

        public bool CanAttack { get; set; }

        public void Start()
        {
            CanAttack = true;
            switch (weaponType)
            {
                case WeaponType.Pistol:
                    EquipWeapon(pistol);
                    break;
                case WeaponType.Shotgun:
                    EquipWeapon(shotgun);
                    break;
                default:
                    throw new ArgumentOutOfRangeException();
            }
        }

        private void EquipWeapon(EnemyBaseWeapon newWeapon)
        {
            CurrentWeapon = newWeapon;
            CurrentWeapon.CurrentPrimaryAmmo = CurrentWeapon.maxPrimaryAmmo;
            CurrentWeapon.CurrentSecondaryAmmo = CurrentWeapon.maxSecondaryAmmo;
            
        }

        public void Reload()
        {
            if (!CurrentWeapon) return;
            CurrentWeapon.Reload();
        }

        public void Fire()
        {
            if (!CurrentWeapon || !CanAttack) return;
            CurrentWeapon.Fire();
        }
    }
}
```
{collapsible="true" collapsed-title="EnemyShooting.cs"}

## Private Methods
{type="wide" sorted="asc"}
EquipWeapon()
: Equips a new weapon.
Sets the current weapon to the new weapon, and updates the ammo according to the values set by the weapon script.

## Public Methods
{type="wide" sorted="asc"}
Start()
: States that the enemy is able to attack.
Checks what weapon type the enemy has been configured to be, and equips the according weapon.

Reload()
: Calls the reload function on the current weapon the enemy has.

Fire()
: Calls the fire function on the current weapon the enemy has.

## Variables
{type="wide" sorted="asc"}
weaponType
: The weapon type to be used by the enemy.

pistol
: The pistol prefab to be equipped by the enemy.

shotgun
: The shotgun prefab to be equipped by the enemy.

CurrentWeapon
: The currently equipped weapon.

CanAttack
: Can the enemy currently attack.

