# PlayerProjectilePool.cs


## Description
This script stores a list of GameObjects that become enabled and disabled when the player shoots a weapon set
to projectile mode. 

## Script
```C#
using System;
using System.Collections.Generic;
using UnityEngine;

namespace Player
{
    public class PlayerProjectilePool : MonoBehaviour
    {
        public static PlayerProjectilePool SharedInstance;
        public List<GameObject> pooledProjectiles;

        [SerializeField] private GameObject objectToPool;
        [SerializeField] private int amountToPool;

        private GameObject projParent;

        private void Awake()
        {
            SharedInstance = this;
        }

        private void Start()
        {
            projParent = GameObject.FindGameObjectWithTag("ProjectilePool");
            pooledProjectiles = new List<GameObject>();
            for (int i = 0; i < amountToPool; i++)
            {
                var tmp = Instantiate(objectToPool, projParent.transform);
                tmp.SetActive(false);
                pooledProjectiles.Add(tmp);
            }
        }

        public GameObject GetPooledProjectile()
        {
            for (int i = 0; i < amountToPool; i++)
            {
                if (!pooledProjectiles[i].activeInHierarchy)
                    return pooledProjectiles[i];
            }

            return null;
        }
    }
}
```
{collapsible="true" collapsed-title="PlayerProjectilePool.cs"}

## Public Methods

{type="wide" sorted="asc"}
GetPooledProjectile()
: Returns the next non-active projectile in the scene hierarchy.

## Variables

{type="wide" sorted="asc"}
pooledProjectiles
: List of Game Objects - stores the projectiles to be used by the player

objectToPool
: The Game Object being pooled i.e. The Projectile Prefab

amountToPool
: An integer defining the size of the Game Object list - how many projectiles to pool

projParent
: The parent game object that will contain all the projectiles to be pooled.