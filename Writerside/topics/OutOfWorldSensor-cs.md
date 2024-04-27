# OutOfWorldSensor.cs
<show-structure depth="2" />

## Description

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