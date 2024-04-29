# EnemyProjectilePool.cs
<show-structure depth="2" />

## Description

The script for pooling enemy projectiles.

## Script
```C#
using System.Collections.Generic;
using UnityEngine;

namespace Weapons.Enemy
{
    // src = https://learn.unity.com/tutorial/introduction-to-object-pooling
    public class EnemyProjectilePool : MonoBehaviour
    {
        public List<GameObject> pooledProjectiles;

        [SerializeField] private GameObject objectToPool;
        [SerializeField] private int amountToPool;

        private GameObject _projParent;

        private void Start()
        {
            _projParent = GameObject.FindGameObjectWithTag("ProjectilePool");
            pooledProjectiles = new List<GameObject>();
            for (var i = 0; i < amountToPool; i++)
            {
                var tmp = Instantiate(objectToPool, _projParent.transform);
                tmp.SetActive(false);
                pooledProjectiles.Add(tmp);
            }
        }

        public GameObject GetPooledProjectile()
        {
            for (var i = 0; i < amountToPool; i++)
            {
                if (!pooledProjectiles[i].activeInHierarchy)
                    return pooledProjectiles[i];
            }

            return null;
        }
    }
}
```
{collapsed-title="EnemyProjectilePool.cs" collapsible="true"}

## Public Methods
{type="wide" sorted="asc"}
GetPooledProjectile()
: Goes through the list of objects that have been pooled, and finds the first in the list that currently are not active in the
scene. Returns the found pooled projectile GameObject.

## Private Methods
{type="wide" sorted="asc"}
Start()
: Finds the projectile pool Game Object that the instantiated projectiles will be parented under. Creates a new list, instantiates new game objects,
disables them, and then appends them to the newly created list.

## Variables
{type="wide" sorted="asc"}
pooledProjectiles
: A list of GameObjects, containing pooled projectiles that are ready to be used by the enemy Game Objects.

objectToPool
: The selected object that will be used for pooling.

amountToPool
: The number of objects to pool.

_projParent
: The parent object that all pooled objects will be childed under in the scene hierarchy.