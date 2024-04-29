# BaseWeapon.cs
<show-structure depth="2" />

## Description

The base weapon class for all weapons used in the game, both by the enemies and by the player.

## Script
```C#
using System.Collections;
using Player;
using Tutorial;
using UnityEngine;


namespace Weapons
{
    public class BaseWeapon : MonoBehaviour
    {
        public enum WeaponState
        {
            Firing,
            Reloading,
            Idle,
            NoAmmo
        }

        public enum ShootingType
        {
            Hitscan,
            Projectile
        }

        public float weaponReloadTime;
        public float weaponFireRate;
        public int weaponDamage;
        public int weaponRange;
        public int maxPrimaryAmmo;
        public int maxSecondaryAmmo;
        public int shotgunPelletCount;
        public GameObject weaponProjectile;
        public LayerMask layersToHitScan;
        public Vector3 weaponSpread;
        public WeaponState weaponAction;
        public ShootingType shootingType;
        public AudioClip weaponSound;
        public AudioClip reloadSound;

        [Header("Projectile Specific Settings")] 
        [SerializeField] private float projectileSpeed;
        [SerializeField] private float projectileDespawnTime;

        [Header("Bullet Casing Settings")] 
        public GameObject pistolBulletCasing;
        public GameObject shotgunBulletCasing;
        public Transform bulletCasingSpawnPos;

        [Header("Tutorial Related Settings")] 
        public TutorialController tutorialController;
        public Transform spawnPosition;
        public PlayerController playerController;


        public int CurrentPrimaryAmmo { get; set; }

        public int CurrentSecondaryAmmo { get; set; }

        protected float ProjectileSpeed => projectileSpeed;

        protected float ProjectileDespawnTime => projectileDespawnTime;

        public virtual void Reload()
        {
            if (weaponAction == WeaponState.Reloading) return;
            if (CurrentPrimaryAmmo == maxPrimaryAmmo) return;
            weaponAction = WeaponState.Reloading;
            switch (CurrentPrimaryAmmo)
            {
                case <= 0 when CurrentSecondaryAmmo <= 0:
                    weaponAction = WeaponState.NoAmmo;
                    return;
            }

            playerController.audioSource.PlayOneShot(reloadSound);
            var newAmmo = Mathf.Clamp(CurrentPrimaryAmmo + CurrentSecondaryAmmo, 0, maxPrimaryAmmo);
            StartCoroutine(ReloadCooldown(newAmmo));
            if (transform.root.CompareTag("Player")) playerController.canvasScript.Reload(weaponReloadTime);
        }

        public virtual void Fire()
        {
            if (weaponAction != WeaponState.Idle) return;
            if (CurrentPrimaryAmmo <= 0)
                return;
            if (playerController)
                playerController.audioSource.PlayOneShot(weaponSound);
            CurrentPrimaryAmmo--;

            StartCoroutine(WeaponCooldown());
        }

        private IEnumerator WeaponCooldown()
        {
            weaponAction = WeaponState.Firing;
            var cooldown = weaponFireRate / 10f;
            yield return new WaitForSeconds(cooldown);
            weaponAction = WeaponState.Idle;
        }

        private IEnumerator ReloadCooldown(int newPrimary)
        {
            yield return new WaitForSeconds(weaponReloadTime);
            CurrentSecondaryAmmo -= maxPrimaryAmmo - CurrentPrimaryAmmo;
            CurrentSecondaryAmmo = Mathf.Clamp(CurrentSecondaryAmmo, 0, maxSecondaryAmmo);
            CurrentPrimaryAmmo = newPrimary;
            CurrentPrimaryAmmo = Mathf.Clamp(CurrentPrimaryAmmo, 0, maxPrimaryAmmo);
            weaponAction = WeaponState.Idle;
        }

        protected Vector3 GetWeaponSpread(Transform weaponSpawnPos)
        {
            var direction = weaponSpawnPos.forward;
            direction += new Vector3(
                Random.Range(-weaponSpread.x, weaponSpread.x),
                Random.Range(-weaponSpread.y, weaponSpread.y),
                Random.Range(-weaponSpread.z, weaponSpread.z)
            );
            return direction;
        }
    }
}
```
{collapsed-title="BaseWeapon.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
Reload()
: Reload the weapon. Check if ammo is already full or if already reloading. Plays the reload sound, and calculates the new
value. Start the reload cooldown, and if the player uses the weapon, update the UI elements to visualise the reload time.

Fire()
: Fires the weapon. Make sure the weapon is currently able to shoot. If used by player, then play the firing audio, and proceeds to
decrement the primary ammo value. Starts the weapon firing cooldown.

## Private Methods
{type="wide" sorted="asc"}
WeaponCooldown()
: Updates the weapon state to show it is currently shooting. Calculates the weapon cooldown based upon the fire-rate. Waits for that amount
of time, and then returns the weapon to the idle state.

ReloadCooldown()
: Receives an integer input of the updated primary ammo value. Waits for the specified reload duration, then calculates the new secondary and primary ammo, and then returns the weapon back to 
the idle state.

## Protected Methods
{type="wide" sorted="asc"}
GetWeaponSpread()
: Calculates the weapon spread. Randomises the Vectors X, Y and Z axis by using both negative and positive values of the spread value. Returns
the new direction.

## Components
{type="wide" sorted="asc"}

tutorialController
: The tutorial scene manager component.

playerController
: The player controller component.


## Public Variables
{type="wide" sorted="asc"}
weaponAction
: Used to store the current state of the weapon. Uses an enum structure, so can only be pre-determined states.

shootingType
: Determines whether the weapon will be using projectiles, or use raycasts (hitscan).

weaponReloadTime
: The time to take when reloading.

weaponFireRate
: How fast the weapon can be fired.

weaponDamage
: The damage the weapon will do.

weaponRange
: The max range the weapon can reach.

maxPrimaryAmmo
: The maximum value that can be stored in one magazine before reloading.

maxSecondaryAmmo
: The maximum value of ammo that can be carried for reloading.

shotgunPelletCount
: The amount of pellet projectiles to be shot by the shotgun weapon.

weaponProjectile
: The projectile GameObject used by the weapon.

layersToHitScan
: The layers that the raycast can hit, and the layers that can be ignored.

weaponSpread
: The max radius of spread / scatter the weapon has.

weaponSound
: The sound to be played when the weapon is fired.

reloadSound
: The sound to be played when the weapon is reloaded.

pistolBulletCasing
: The bullet casing to be spawned upon firing a pistol.

shotgunBulletCasing
: The bullet casing to be spawned upon firing a shotgun.

bulletCasingSpawnPos
: The location on the weapon for the weapon casing to be ejected from.

spawnPosition
: The spawn position for the projectiles or raycasts to originate from.

## Private Variables
{type="wide" sorted="asc"}

projectileSpeed
: How fast the projectile objects will travel at.

projectileSpawnTime
: The amount of lifetime the projectile can have in-game.

## Encapsulated Variables
{type="wide" sorted="asc"}

CurrentPrimaryAmmo
: Public reference to the weapons current primary ammo.

CurrentSecondaryAmmo
: Public reference to the weapons current secondary ammo.

ProjectileSpeed
: Public reference to the weapons projectile speed.

ProjectileDespawnTime
: Public reference to the weapons despawn time.


