# CanvasScript.cs
<show-structure depth="2" />

## Description

This script controls the UI throughout play-through.

## Script
```C#
using System;
using System.Collections;
using Player;
using TMPro;
using Tutorial;
using UnityEngine;
using UnityEngine.UI;

namespace UI
{
    public class CanvasScript : MonoBehaviour
    {
        [Header("Images")] 
        [SerializeField] private Image reloadBar;
        [SerializeField] private Image healthBar;

        [Header("Panels")] 
        [SerializeField] private GameObject ammoPanel;
        [SerializeField] private GameObject healthPanel;
        [SerializeField] private GameObject enemiesPanel;


        [Header("Text")] 
        [SerializeField] private TextMeshProUGUI ammoReporter;
        [SerializeField] private TextMeshProUGUI enemiesToKill;

        [Header("Required Components")] 
        [SerializeField] private TutorialEnemyController tutorialEnemyController;
        [SerializeField] private TutorialController tutorialController;

        private PlayerController _player;
        private PlayerShooting _playerShooting;
        private PlayerHealth _playerHealth;

        private bool _currentlyReloading;
        private bool _enemyKillChallenge;

        private int _numberOfEnemies;


        private void Start()
        {
            _player = GameObject.FindGameObjectWithTag("Player").GetComponent<PlayerController>();
            _playerShooting = _player.playerShooting;
            _playerHealth = _player.playerHealth;
            reloadBar.fillAmount = 0f;
            ammoPanel.SetActive(false);
            enemiesPanel.SetActive(false);
            healthPanel.SetActive(true);
        }

        private void FixedUpdate()
        {
            if (ammoPanel.activeSelf && Math.Abs(reloadBar.fillAmount - 1f) < 0.01f && !_currentlyReloading)
                reloadBar.gameObject.SetActive(false);

            if (_playerShooting.HasWeapon() && !ammoPanel.activeSelf)
                ammoPanel.SetActive(true);

            if (healthPanel.activeSelf)
                healthBar.fillAmount = _playerHealth.CurrentHealth / _playerHealth.MaxHealth;

            switch (_enemyKillChallenge)
            {
                case true when !enemiesPanel.activeSelf:
                    enemiesPanel.SetActive(true);
                    break;
                case true when enemiesPanel.activeSelf:
                    enemiesToKill.text =
                        $"Remaining: \n{tutorialEnemyController.EnemiesRemaining} / {_numberOfEnemies}";
                    break;
                case false when enemiesPanel.activeSelf:
                    enemiesPanel.SetActive(false);
                    break;
            }

            if (_playerShooting.HasWeapon() && ammoPanel.activeSelf)
                ammoReporter.text =
                    $"{_playerShooting.CurrentWeapon.CurrentPrimaryAmmo} / {_playerShooting.CurrentWeapon.CurrentSecondaryAmmo}";
        }


        public void Reload(float reloadTime)
        {
            _currentlyReloading = true;
            reloadBar.gameObject.SetActive(true);
            LeanTween.value(reloadBar.gameObject, 0f, 1f, reloadTime).setOnUpdate(val =>
            {
                var i = reloadBar.fillAmount;
                i = val;
                reloadBar.fillAmount = i;
            }).setOnComplete(() => { _currentlyReloading = false; });
        }

        public void ShowKillChallengeUI(int enemies)
        {
            _numberOfEnemies = enemies;
            StartCoroutine(EnemyKillChallenge());
        }

        private IEnumerator EnemyKillChallenge()
        {
            _enemyKillChallenge = true;
            yield return new WaitUntil(() => tutorialEnemyController.EnemiesRemaining == 0);
            _enemyKillChallenge = false;
            tutorialController.EnemyChallengeComplete();
        }
    }
}
```
{collapsed-title="CanvasScript.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Finds the player and gets the PlayerController component.
Sets the relevant variables, changes the fill rate of the reload bar to zero,
and then disables the ammo and enemies remaining UI elements.
Enables the player health UI elements.

FixedUpdate()
: Checks if the player is currenty reloading.
If the player has finished reloading and the reload bar is full, then disable the ammo UI elements.
If the player has a weapon and the ammo panel is disabled, then enable it.
If the health panel is active, then update the fill amount of the health bar depending on the players current health.
If player is currently doing an enemy kill challenge, and the UI elements are disabled, then enable it.
If true, then update the text depending on the number of enemies remaining.
If false, then disable the UI elements.
Also update the player ammo text if the player has a weapon equipped.

EnemyKillChallenge()
: Sets the kill challenge to true, and then waits until no more enemies for the challenge remain.
Then sets the kill challenge indicator to false, and calls EnemyChallenge complete on the tutorial controller.

## Public Methods
{type="wide" sorted="asc"}
Reload()
: Displays the weapon reloading UI elements.
uses LeanTween to tween the fill rate of the reload bar, depending on the time that has passed.
Once completed, then update a boolean to state that the player is no longer reloading.

ShowKillChallengesUI()
: Takes an integer input to determine how many enemies have to be killed for the challenge.
Then calls the EnemyKillChallenge coroutine.

## Variables
{type="wide" sorted="asc"}
reloadBar
: The bar to be displayed when the player is reloading.

healthbar
: The bar to display the players current health value.

ammoPanel
: The parent object of all the player weapon UI elements.

healthPanel
: The parent object of all the player health UI elements.

enemiesPanel
: The parent object of all the enemy challenge UI elements.

ammoReporter
: The text that displays the current ammo of the weapon being used by the player.

enemiesToKill
: The text displaying how many enemies are needed to complete the enemy kill challenge.

tutorialEnemyController
: The tutorial enemy controller component within the scene.

tutorialController
: The component that controls the tutorial scene.

_player
: The player controller component of the player.

_playerShooting
: The player shooting component of the player.

_playerHealth
: The player health component of the player.

_currentlyReloading
: Is the player currently reloading.

_enemyKillChallenge
: Is the player currently doing an enemy kill challenge.

_numberOfEnemies
: Number of enemies at the start of the kill challenge.