# Portal.cs
<show-structure depth="2" />

## Description
This script is used to check when the player has finished the level.
## Script
```C#
using UnityEngine;
using UnityEngine.SceneManagement;

namespace Environment
{
    public class Portal : MonoBehaviour
    {
        private void OnTriggerEnter(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            SceneManager.LoadScene("winScene");
        }
        private void OnTriggerStay(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            SceneManager.LoadScene("winScene");
        }
    }
}

```
{collapsed-title="Portal.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
OnTriggerEnter()
: Checks if the collided object is the player.
If it is, then load the win scene.

OnTriggerStay()
: Checks if the collided object is the player.
If it is, then load the win scene.