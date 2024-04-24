# PlayerHealth.cs

## Description
This script is used to manage the Player's health in-game. It calls the required functions for the Player Death to happen.

## Script
```C#
using System;
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;

namespace Player
{
    public class PlayerHealth : MonoBehaviour
    {

        [SerializeField] private float maxHealth = 10;
        
        public float CurrentHealth { get; set; }

        public float MaxHealth
        {
            get => maxHealth;
            set => maxHealth = value;
        }


        public void Damage(float amount)
        {
            CurrentHealth -= amount;
            if (CurrentHealth <= 0) Die();
        }

        private void Start()
        {
            CurrentHealth = maxHealth;
        }

        private void Die()
        {
            LoadNextScene();
        }

        private void LoadNextScene()
        {
            Cursor.lockState = CursorLockMode.None;
            SceneManager.LoadScene(2);
        }

        private IEnumerator LoadLevel(int levelIndex)
        {
            // todo: add crossfade
            
            SceneManager.LoadSceneAsync(levelIndex);
            yield break;
        }

    }
}
```
{collapsible="true" collapsed-title="PlayerHealth.cs"}

## Public Methods

{type="wide" sorted="asc"}
Damage()
: Lowers the Player's health by the value of the float as an input.

## Private Methods
{type="wide" sorted="asc"}
Start()
: Called on game start, sets the Players Current Health to the value of the Max Health.

Die()
: Calls the function to load the Death Scene

LoadNextScene()
: Enables the cursor and loads the death scene.

LoadLevel() - **Not Implemented**
: Originally was going to be used to fade the scene to black before loading the Death Scene.

## Variables
{type="wide" sorted="asc"}
maxHealth
: The value that the player's max health will be upon game start.

CurrentHealth
: Encapsulated value that stores the player's current health.

MaxHealth
: Encapsulated and public variable for the maxHealth variable.

