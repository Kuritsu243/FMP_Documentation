# TutorialEnemyController.cs
<show-structure depth="2" />

## Description
This is the controller for the enemies during the kill challenge in the tutorial scene.
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

## Private Methods
{type="wide" sorted="asc"}
Start()
: Sets the number of enemies remaining to the size of the tutorial enemies list.

## Public Methods
{type="wide" sorted="asc"}
EnemyKilled()
: Decrements the value of enemies remaining.
Removes the enemy passed through as input from the list.

StartKillChallenge()
: Calls relevant functions on the canvas component to display the UI elements needed for the kill challenge.
Enables all enemies in the enemies list.

## Variables
tutorialEnemies
: List storing the enemies to be used for the kill challenge. 

canvasScript
: The script that is used to control the UI elements in the game.

EnemiesRemaining
: The number of enemies remaining.