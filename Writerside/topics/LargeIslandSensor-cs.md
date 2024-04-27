# LargeIslandSensor.cs
<show-structure depth="2" />

## Description

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