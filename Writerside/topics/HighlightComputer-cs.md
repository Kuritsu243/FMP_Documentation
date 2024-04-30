# HighlightComputer.cs
<show-structure depth="2" />

## Description
This script is used to draw an outline on the computer at the end of the tutorial level.
## Script
```C#
using UnityEngine;

namespace Tutorial
{
    public class HighlightComputer : MonoBehaviour
    {
        [SerializeField] private float targetOutlineWidth;
        [SerializeField] private Color outLineColor;
        [SerializeField] private float timeToTake;

        private Outline _computerOutline;

        private void Start()
        {
            _computerOutline = GetComponent<Outline>();
            _computerOutline.OutlineWidth = 0;
            _computerOutline.enabled = false;
            _computerOutline.OutlineColor = outLineColor;
        }

        
        
        public void OutlineComputer()
        {
            _computerOutline.enabled = true;
            LeanTween.value(_computerOutline.gameObject, _computerOutline.OutlineWidth, targetOutlineWidth, timeToTake)
                .setOnUpdate(
                    f =>
                    {
                        _computerOutline.OutlineWidth = f;
                    })
                .setLoopPingPong();
        }

        public void StopOutline()
        {
            LeanTween.cancel(_computerOutline.gameObject);
            _computerOutline.enabled = false;
        }
        
    }
}
```
{collapsed-title="HighlightComputer.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets the outline component, and disables it.
Updates the colour of the outline.

## Public Methods
{type="wide" sorted="asc"}
OutlineComputer()
: Enables the computer outline.
Tweens the weapon outline width via LeanTween.

StopOutline()
: Cancels the outline.
Disables the outline object.
