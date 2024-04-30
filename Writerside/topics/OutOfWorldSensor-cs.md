# OutOfWorldSensor.cs
<show-structure depth="2" />

## Description
This script is used to check if the player has fallen out of the world.

## Script
```C#
using UnityEngine;
using UnityEngine.SceneManagement;

namespace Environment
{
    public class OutOfWorldSensor : MonoBehaviour
    {
        private void OnTriggerEnter(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            SceneManager.LoadSceneAsync("deathScene");

        }

        private void OnTriggerExit(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            SceneManager.LoadSceneAsync("deathScene");
        }

        private void OnTriggerStay(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            SceneManager.LoadSceneAsync("deathScene");
        }
    }
}
```
{collapsible="true" collapsed-title="OutOfWorldSensor.cs"}

## Private Methods
{type="wide" sorted="asc"}
OnTriggerEnter()
: Checks if the collided object is the player.
If it is, then load the death scene.

OnTriggerExit()
: Checks if the collided object is the player.
If it is, then load the death scene.

OnTriggerStay()
: Checks if the collided object is the player.
If it is, then load the death scene.