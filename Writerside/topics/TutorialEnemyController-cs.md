# TutorialEnemyController.cs

## Description

## Script
```C#
using System.Collections.Generic;
using AI;
using UI;
using UnityEngine;

namespace Tutorial
{
    public class TutorialEnemyController : MonoBehaviour
    {
        [SerializeField] private List<EnemyController> tutorialEnemies;
        [SerializeField] private CanvasScript canvasScript;

        public int EnemiesRemaining { get; set; }

        private void Start()
        {
            EnemiesRemaining = tutorialEnemies.Count;
        }

        public void EnemyKilled(EnemyController enemyKilled)
        {
            EnemiesRemaining--;
            tutorialEnemies.Remove(enemyKilled);
        }


        public void StartKillChallenge()
        {
            canvasScript.ShowKillChallengeUI(EnemiesRemaining);
            foreach (var enemy in tutorialEnemies)
                enemy.EnableEnemy();
        }
    }
}
```
{collapsible="true" collapsed-title="TutorialEnemyController.cs"}