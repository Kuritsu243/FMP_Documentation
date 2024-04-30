# TutorialEnemy.cs
<show-structure depth="2" />

## Description
The script attached to the non-hostile NPC during the tutorial scene.
## Script
```C#
using UnityEngine;

namespace Tutorial
{
    public class TutorialEnemy : MonoBehaviour
    {
        [SerializeField] private TutorialController tutorialController;
        [SerializeField] private TutorialEnemyController tutorialEnemyController;
        [SerializeField] private bool isHostile;


        private Animator _enemyAnimator;
        private Collider _enemyCollider;
        private static readonly int IsDead = Animator.StringToHash("isDead");

        private void Start()
        {
            _enemyAnimator = GetComponent<Animator>();
            _enemyCollider = GetComponentInChildren<Collider>();
        }

        public void Die()
        {
            if (isHostile) return;
            _enemyAnimator.SetBool(IsDead, true);
            tutorialController.EnemyChecks["Killed"] = true;
            tutorialController.TutorialEnemyKilled();
            Destroy(_enemyCollider);
        }
    }
}
```
{collapsed-title="TutorialEnemy.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Stores a reference to the needed components.

## Public Methods
{type="wide" sorted="asc"}
Die()
: Sets the trigger on the animator to play the death animation.
Updates a value on the tutorial controller to tell the script that the enemy has been killed.
Calls the function on the tutorial controller to indicate the enemy has been killed, then destroys the NPC's collider.

## Variables
{type="wide" sorted="asc"}
tutorialController
: The tutorial controller component 

tutorialEnemyController
: The enemy controller to be used during the tutorial.

isHostile
: Is the enemy hostile.

_enemyAnimator
: The animator component attached to the enemy.

_enemyCollider
: The collider component attached to the enemy.

IsDead
: Hashed reference to a trigger on the animator component.