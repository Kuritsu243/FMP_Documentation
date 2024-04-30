# WinScreen.cs
<show-structure depth="2" />

## Description
The script used for the Win Screen.

## Script
```C#
using System;
using UnityEditor;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

namespace UI
{
    public class WinScreen : MonoBehaviour
    {
        [SerializeField] private Button retryBtn;
        [SerializeField] private Button quitBtn;

        private void Start()
        {
            retryBtn.onClick.AddListener(RetryGame);
            quitBtn.onClick.AddListener(CloseGame);
        }

        private static void CloseGame()
        {
            switch (Application.platform)
            {
#if UNITY_EDITOR
                case RuntimePlatform.LinuxEditor:
                case RuntimePlatform.WindowsEditor:
                    EditorApplication.ExitPlaymode();
                    break;
#endif
                case RuntimePlatform.LinuxPlayer:
                case RuntimePlatform.WindowsPlayer:
                    Application.Quit();
                    break;
            }
        }

        private static void RetryGame()
        {
            SceneManager.LoadScene("startScene");
        }
    }
    
}
```
{collapsed-title="WinScreen.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Adds the listeners to the buttons.

CloseGame()
: If in editor, exit play mode.
If deployed build, quit the application.

RetryGame()
: Loads the start scene.

## Variables
{type="wide" sorted="asc"}
retryBtn
: The button used to restart the game.

quitBtn
: The button used to close the game.
