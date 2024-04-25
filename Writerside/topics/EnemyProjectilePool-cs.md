# EnemyProjectilePool.cs

## Description

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