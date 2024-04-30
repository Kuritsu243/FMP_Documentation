# HighlightWeapon.cs
<show-structure depth="2" />

## Description
This script is used to highlight the weapon that the player collects during the tutorial.
## Script

```C#
using System.Collections;
using UnityEngine;

namespace Tutorial
{
    public class HighlightWeapon : MonoBehaviour
    {
        [SerializeField] private float targetOutlineWidth;
        [SerializeField] private Color outlineColor;
        [SerializeField] private float timeToTake;
        [SerializeField] private float pulseTime;
        
        
        private Outline _weaponOutline;
        

        private void Start()
        {
            _weaponOutline = GetComponent<Outline>();
            _weaponOutline.OutlineWidth = 0;
            _weaponOutline.enabled = false;
            _weaponOutline.OutlineColor = outlineColor;
        }

        public void OutlineWeapon()
        {
            _weaponOutline.enabled = true;
            LeanTween.value(_weaponOutline.gameObject, _weaponOutline.OutlineWidth, targetOutlineWidth, 2f)
                .setOnUpdate(
                    f =>
                    {
                        _weaponOutline.OutlineWidth = f;
                    })
                .setLoopPingPong();
        }

        private IEnumerator LerpHighlight()
        {
            var elapsedTime = 0f;
            while (_weaponOutline.OutlineWidth < targetOutlineWidth)
            {
                _weaponOutline.OutlineWidth = Mathf.Lerp(2f, targetOutlineWidth, elapsedTime / timeToTake);
                elapsedTime += Time.deltaTime;
                yield return null;
            }

            StartCoroutine(PulseOutline());
            yield return null;
        }

        private IEnumerator PulseOutline()
        {
            var timeTaken = 0f;
            while (_weaponOutline.OutlineWidth > 0f)
            {
                _weaponOutline.OutlineWidth = Mathf.Lerp(_weaponOutline.OutlineWidth, 0f, timeTaken / pulseTime);
                timeTaken += Time.deltaTime;
                yield return null;
            }
            StartCoroutine(LerpHighlight());
            yield return null;

        }
    }
}
```
{collapsible="true" collapsed-title="HighlightWeapon.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets the outline component, and disables it.
Updates the colour of the outline.

LerpHighlight()
: Lerps the current outline width of the weapon whilst the outline is enabled.
Calls the pulse outline coroutine.
Replaced by a leantween ping pong.

PulseOutline()
: Lerps the current outline width of the weapon whilst the outline is enabled.
Calls the LerpHighlight coroutine.
Replaced by a leantween ping pong.

## Public Methods
{type="wide" sorted="asc"}

OutlineWeapon()
: Enables the weapon outline.
Tweens the weapon outline width via LeanTween.

## Variables
{type="wide" sorted="asc"}
targetOutlineWidth
: The target outline width to have.

outlineColor
: The colour to be used for the outline.

timeToTake
: How long should be taken between the weapon having no outline and having a full outline.

pulseTime
: How long to pulse the weapon.

