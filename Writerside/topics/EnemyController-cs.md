# EnemyController.cs
<show-structure depth="2" />

## Description
This script controls all enemies within the game.
## Script
```C#
using System.Collections;
using System.Linq;
using Tutorial;
using UnityEngine;
using UnityEngine.AI;

namespace AI
{
    public static class GameObjectExtensions
    {
        public static GameObject GetChildWithTag(this GameObject parent, string tag)
        {
            var t = parent.transform;
            return (from Transform tr in t where tr.CompareTag(tag) select tr.gameObject).FirstOrDefault();
        }
    }

    public class EnemyController : MonoBehaviour
    {
        [SerializeField] private float playerDetectionRange;
        [SerializeField] private float pauseBeforeAttack;
        [SerializeField] private float timeBetweenAttacks;
        [SerializeField] private TutorialEnemyController tutorialEnemyController;
        private Animator _enemyAnimator;
        private bool _canMove = true;
        private EnemyShooting _enemyShooting;
        private float _velocity;
        private GameObject _player;
        private GameObject _enemyMesh;
        private NavMeshAgent _navMeshAgent;
        private Quaternion _enemyRotation;
        private Transform _enemyTransform;
        private Vector3 _targetPoint;
        private Vector3 _previousPos;

        // ReSharper disable once NotAccessedField.Local
        private TutorialEnemy _tutorialEnemy;
        private static readonly int Velocity = Animator.StringToHash("velocity");
        public bool IsTutorial { get; private set; }
        public TutorialEnemyController TutorialEnemyManager => tutorialEnemyController;


        private void Start()
        {
            _enemyMesh = gameObject.GetChildWithTag("EnemyMesh");
            _player = GameObject.FindGameObjectWithTag("PlayerMesh");
            _navMeshAgent = GetComponent<NavMeshAgent>();
            _enemyShooting = GetComponent<EnemyShooting>();
            _enemyAnimator = GetComponentInChildren<Animator>();
            IsTutorial = TryGetComponent(out _tutorialEnemy);
            if (!IsTutorial) return;
            _navMeshAgent.enabled = false;
            _enemyShooting.enabled = false;
            _canMove = false;
        }

        public void EnableEnemy()
        {
            _navMeshAgent.enabled = true;
            _enemyShooting.enabled = true;
            _canMove = true;
        }

        private void FixedUpdate()
        {
            if (!_enemyShooting.enabled) return;
            if (_enemyShooting.CurrentWeapon.CurrentPrimaryAmmo == 0)
            {
                _enemyShooting.Reload();
                return;
            }

            if (Vector3.Distance(transform.position, _player.transform.position) > playerDetectionRange) return;
            if (IsFacingPlayer() && _enemyShooting.CanAttack) PrepareToShoot();
            if (_targetPoint == _player.transform.position) return;
            _targetPoint = _player.transform.position;
            if (_canMove) _navMeshAgent.SetDestination(_targetPoint);
        }

        private void LateUpdate()
        {
            _velocity = (_enemyMesh.transform.position - _previousPos).magnitude / Time.deltaTime;
            _previousPos = _enemyMesh.transform.position;
            _enemyAnimator.SetFloat(Velocity, _velocity);
        }

        private bool IsFacingPlayer()
        {
            return Vector3.Dot(transform.forward, (_player.transform.position - transform.position).normalized) > 0.95f;
        }

        private void PrepareToShoot()
        {
            _navMeshAgent.isStopped = true;
            _canMove = false;
            transform.LookAt(_player.transform);
            StartCoroutine(WaitBeforeAttack());
        }

        private IEnumerator WaitBeforeAttack()
        {
            if (!_enemyShooting.CanAttack) yield break;
            yield return new WaitForSeconds(pauseBeforeAttack);
            _enemyShooting.Fire();
            _navMeshAgent.isStopped = false;
            _canMove = true;
            StartCoroutine(TimeBeforeAttacks());
        }

        private IEnumerator TimeBeforeAttacks()
        {
            _enemyShooting.CanAttack = false;
            yield return new WaitForSeconds(timeBetweenAttacks);
            _enemyShooting.CanAttack = true;
        }
    }
}
```
{collapsible="true" collapsed-title="EnemyController.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets all the components required by the script and stores them as variables.
if in tutorial, disables navmesh, tells the game they can't move, and disables the shooting component.

EnableEnemy()
: Enables the enemy, by enabling the nav mesh and enemy shooting components,
and indicates to the enemy that they can move.

FixedUpdate()
: Checks if the enemy ammo is zero, and if so calls the reload function.
Checks distance between themselves and the player,
and if they're in range then sets the target destination to move to be the players location.
If they are currently facing the player and can shoot, then call the PrepareToShoot function.

LateUpdate()
: Calculates the velocity, and passes it through to the animator component to update the current animation clip / state.

IsFacingPlayer()
: Returns whether the enemy is currently facing the player or not.

PrepareToShoot()
: Disables movement, looks at the player and then calls the coroutine to wait before attacking.

WaitBeforeAttack()
: Pauses for a pre-determined amount of time, before shooting.
Re-enables movement, and starts the attack cooldown.

TimeBeforeAttacks()
: Updates values to indicate that the enemy can no longer shoot,
waits for a certain amount of time, and then resets the value to tell the enemy script that they can shoot again.