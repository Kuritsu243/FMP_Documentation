# EnemyHealth.cs
<show-structure depth="2" />

## Description
This script controls the enemies health in-game.
## Script
```C#
using System.Collections;
using UnityEngine;
using UnityEngine.AI;

namespace AI
{
    public class EnemyHealth : MonoBehaviour
    {
        [SerializeField] private float maxHealth = 5f;
        private Animator _enemyAnimator;
        private bool _hasDied;
        private Collider _enemyCollider;
        private EnemyShooting _enemyShooting;
        private EnemyController _enemyController;
        private float CurrentHealth { get; set; }
        private NavMeshAgent _navMeshAgent;
        private static readonly int IsDead = Animator.StringToHash("isDead");
        
        private void Start()
        {
            CurrentHealth = maxHealth;
            _enemyController = GetComponent<EnemyController>();
            _enemyShooting = GetComponent<EnemyShooting>();
            _enemyAnimator = GetComponentInChildren<Animator>();
            _enemyCollider = GetComponentInChildren<Collider>();
            _navMeshAgent = GetComponent<NavMeshAgent>();
        }
        
        private void FixedUpdate()
        {
            if (CurrentHealth <= 0 && !_hasDied) StartCoroutine(Die());
        }
        public void Damage(float value)
        {
            CurrentHealth -= value;
        }

        private IEnumerator Die()
        {
            _hasDied = true;
            if (_enemyController.IsTutorial)
                _enemyController.TutorialEnemyManager.EnemyKilled(_enemyController);
            
            _enemyAnimator.SetBool(IsDead, true);
            Destroy(_navMeshAgent);
            Destroy(_enemyShooting);
            Destroy(_enemyController);
            Destroy(_enemyCollider);
            yield return new WaitForSeconds(5f);
            Destroy(gameObject);
        }
    }
}

```
{collapsed-title="EnemyHealth.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Sets current health to the value of max health,
while also storing variables references to all the components required by this script.

FixedUpdate()
: Constantly checks if the enemy health has reached zero.
If it has, then start the Die coroutine.


Die()
: Indicates that the enemy has died, if in tutorial then tell the tutorial controller an enemy has been killed.
Sets the current animation clip to the enemy death, while also destroying the functional components on the enemy.
Waits for five seconds, before destroying the full object within the game's scene.

## Public Methods
{type="wide" sorted="asc"}
Damage()
: Decreases the current health value by the float input. 

## Variables
{type="wide" sorted="asc"}
maxHealth
: The max health the enemy can have.

_enemyAnimator
: The animator component used by the enemy.

_hasDied
: Has the enemy died.

_enemyCollider
: The collider component attached to the enemy.

_enemyShooting
: The enemy shooting component.

_enemyController
: The enemy controller component.

CurrentHealth
: The current health of the enemy.

_navMeshAgent
: The nav mesh agent component used by the enemy.

IsDead
: Hashed reference to the boolean trigger on the animator component.
