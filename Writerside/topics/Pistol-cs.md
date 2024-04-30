# Pistol.cs
<show-structure depth="2" />

## Description
The script used by the player's pistol.

## Script
```C#
using AI;
using Tutorial;
using UnityEngine;

namespace Weapons
{
    public class Pistol : BaseWeapon
    {
        public override void Fire()
        {
            if (tutorialController && !tutorialController.hasFiredPistolYet)
                tutorialController.EnemyChecks["Fired"] = true;
            if (weaponAction != WeaponState.Idle) return;
            GetWeaponSpread(spawnPosition);
            playerController.activeCinemachineBrain.TryGetComponent<Camera>(out var activeCam);
            var rayOrigin = new Ray(activeCam.transform.position, activeCam.transform.forward);
            if (Physics.Raycast(rayOrigin, out RaycastHit hit, weaponRange, layersToHitScan) &&
                shootingType == ShootingType.Hitscan)
            {
                switch (hit.transform.tag)
                {
                    case "EnemyMesh":
                        var collidedEnemyMesh = hit.transform.parent.gameObject;
                        collidedEnemyMesh.GetComponent<EnemyHealth>().Damage(weaponDamage);
                        break;
                    case "Enemy":
                        var collidedEnemy = hit.transform.gameObject;
                        collidedEnemy.GetComponent<EnemyHealth>().Damage(weaponDamage);
                        break;
                    case "TutorialEnemy":
                        var tutorialEnemy = hit.transform.gameObject;
                        tutorialEnemy.GetComponent<TutorialEnemy>().Die();
                        break;
                    case null when tutorialController:
                        tutorialController.ActuallyAim();
                        break;
                    default:
                        if (!tutorialController) break;
                        tutorialController.ActuallyAim();
                        break;
                }
            }
            else if (tutorialController)
                tutorialController.ActuallyAim();

            Instantiate(pistolBulletCasing, bulletCasingSpawnPos.position, transform.rotation);
            base.Fire();
        }
    }
}
```
{collapsed-title="Pistol.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
Fire()
: Called when the player shoots their pistol.
If in tutorial, and player has not fired the pistol yet, then set the relevant
check to true.
Checks if the weapon action is currently idle, and then proceeds to try and get 
the current main camera.
This function then proceeds to raycast and then check the tags of the hit objects.
If they hit an Enemy relevant tag, then it will call the Damage function on their attatched Enemy
Health component.
If it's during the tutorial, then will call the relevant functions.
Then instantiates the pistol bullet casing.

