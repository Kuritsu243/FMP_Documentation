# FloatingWallController.cs
<show-structure depth="2" />

## Description
Used to move the walls in the tutorial scene, and make them appear.

## Script
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace Environment
{
    public class FloatingWallController : MonoBehaviour
    {

        [SerializeField] private List<GameObject> childObjs;

        public void TriggerWallMovement()
        {
            StartCoroutine(MoveObjectsY());
        }

        private IEnumerator MoveObjectsY()
        {
            foreach (var childObj in childObjs)
            {
                yield return new WaitForSeconds(0.35f);
                LeanTween.moveLocalY(childObj, 0, 3);
            }
        }
    }
}
```
{collapsible="true" collapsed-title="FloatingWallController.cs"}

## Public Methods
{type="wide" sorted="asc"}
TriggerWallMovement()
: Calls the MoveObjectsY coroutine.

## Private Methods
{type="wide" sorted="asc"}
MoveObjectsY()
: For each Game object to be moved, wait 0.35 seconds before moving the object over three seconds.

## Variables
{type="wide" sorted="asc"}
childObjs
: A list of game objects to be moved by this script.