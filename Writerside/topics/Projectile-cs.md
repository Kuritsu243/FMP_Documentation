# Projectile.cs
<show-structure depth="2" />

## Description

This script is attached to any of the weapon projectiles used within the game.

## Script
```C#
using AI;
using UnityEngine;

namespace Weapons
{
    public class Projectile : MonoBehaviour
    {
        protected float ProjectileDamage { get; private set; }
        protected Collider ProjectileCollider;
        private Rigidbody _projectileRigidbody;

        public void Initialize(float damage, float projSpeed, float despawnTime, Vector3 spawnDir)
        {
            ProjectileDamage = damage;
            ProjectileCollider = GetComponent<Collider>();
            _projectileRigidbody = GetComponent<Rigidbody>();
            Invoke(nameof(Despawn), despawnTime);
            _projectileRigidbody.velocity = (spawnDir + transform.forward) * projSpeed;
        }

        public void Despawn()
        {
            gameObject.SetActive(false);
        }

        public virtual void OnTriggerEnter(Collider other)
        {
            switch (other.transform.root.tag)
            {
                case "Player":
                    Physics.IgnoreCollision(other, ProjectileCollider);
                    break;
                case "Enemy":
                    if (other.transform.root.TryGetComponent<EnemyHealth>(out var enemyHealthScript))
                        enemyHealthScript.Damage(ProjectileDamage);
                    Despawn();
                    break;
            }
        }
    }
}
```
{collapsible="true" collapsed-title="Projectile.cs"}

## Public Methods
{type="wide" sorted="asc"}
Initialize()
: Takes all the relevant variables as input, required to fire the projectile and for it to function in the scene.

Despawn()
: Disables the object within the scene.

OnTriggerEnter()
: Checks the collided tag of the object.
If player, then ignore the collision.
If Enemy, then attempt to get the Enemy Health component and calls the Damage function.

## Variables
{type="wide" sorted="asc"}

ProjectileDamage
: The damage the projectile will do.

ProjectileCollider
: The collision component attached to the game object.

_projectileRigidbody
: The rigidbody component attatched to the game object.