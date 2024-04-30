# LargeIslandSensor.cs
<show-structure depth="2" />

## Description
Checks if the player has reached the large island during the tutorial scene.
## Script
```C#
using UnityEngine;

namespace Tutorial
{
    public class LargeIslandSensor : MonoBehaviour
    {
        [SerializeField] private TutorialController tutorialController;

        private void OnTriggerEnter(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            tutorialController.OtherIslandReached();
            Destroy(gameObject);

        }

        private void OnTriggerStay(Collider other)
        {
            if (!other.transform.parent.gameObject.CompareTag("Player")) return;
            tutorialController.OtherIslandReached();
            Destroy(gameObject);
        }
    }
}
```
{collapsed-title="LargeIslandSensor.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
OnTriggerEnter()
: Checks if the player has collided,
if so then indicate to the tutorial controller that the player has reached the island,
and if they have then destroy the sensor game object.

OnTriggerStay()
: Checks if the player has collided,
if so then indicate to the tutorial controller that the player has reached the island,
and if they have then destroy the sensor game object.

## Variables
{type="wide" sorted="asc"}
tutorialController
: The tutorial controller script.