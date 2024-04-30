# WeaponScript.cs
<show-structure depth="2" />

## Description
Unused script initially intended to be used for weapons. 
## Script

```C#
using UnityEngine;

namespace Weapons
{
    public class WeaponScript : MonoBehaviour
    {

        [SerializeField] private BaseWeapon weapon;
    }
}
```
{collapsible="true" collapsed-title="WeaponScript.cs"}

## Variables
{type="wide" sorted="asc"}

weapon
: The weapon to be used.