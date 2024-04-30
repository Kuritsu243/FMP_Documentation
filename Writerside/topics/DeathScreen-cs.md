# DeathScreen.cs
<show-structure depth="2" />

## Description

The script that controls the UI elements displayed upon the player dying in-game.

## Script
```C#
using System;
using System.Collections;
using TMPro;
using UnityEditor;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

namespace UI
{
    public class DeathScreen : MonoBehaviour
    {

        [SerializeField] private Button retryBtn;
        [SerializeField] private Button quitBtn;
        [SerializeField] private TextMeshProUGUI deathText;
        [SerializeField] private TextMeshProUGUI loadingText;
        [SerializeField] private GameObject crossFadeObj;

        private Image _crossFadeImg;


        private void Start()
        {
            deathText.enabled = true;
            loadingText.enabled = false;
            retryBtn.onClick.AddListener(RetryGame);
            quitBtn.onClick.AddListener(CloseGame);
            Cursor.lockState = CursorLockMode.None;
            _crossFadeImg = crossFadeObj.GetComponent<Image>();
        }

        private void RetryGame()
        {
            LoadPreviousScene();
        }

        private void FixedUpdate()
        {
            if (_crossFadeImg.color.a == 0)
                crossFadeObj.SetActive(false);
        }

        private static void CloseGame()
        {
            switch (Application.platform)
            {
#if UNITY_EDITOR
                case RuntimePlatform.WindowsEditor:
                case RuntimePlatform.LinuxEditor:
                    EditorApplication.ExitPlaymode();
                    break;
#endif
                case RuntimePlatform.WindowsPlayer:
                case RuntimePlatform.LinuxPlayer:
                    Application.Quit();
                    break;
            }
        }
        
        private void LoadPreviousScene()
        {
            StartCoroutine(LoadLevel(SceneManager.GetActiveScene().buildIndex - 1));
        }

        private IEnumerator LoadLevel(int levelIndex)
        {
            deathText.enabled = false;
            loadingText.enabled = true;
            retryBtn.gameObject.SetActive(false);
            quitBtn.gameObject.SetActive(false);
            yield return new WaitForSeconds(1f);
            SceneManager.LoadSceneAsync(levelIndex);
        }
    }
}
```
{collapsible="true" collapsed-title="DeathScreen.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Enables the death text, disables the loading text,
and adds listeners to the two buttons that will be shown during the death screen.
Unlocks the cursor, and gets the components required to have scene transitions.

RetryGame()
: Calls the LoadPreviousScene function.

FixedUpdate()
: Checks if the alpha of the cross-fade image is zero, if it is, then disable the cross-fade game object.

CloseGame()
: Used to exit the game.
Switches between the platform it is currently on,
and if it is being played within the Unity editor on either Linux or Windows, then exit play mode.
If being ran on a deployed build, then actually quit the application.

LoadPreviousScene()
: Starts the coroutine to load the previous level.
Uses a scene index of the final scene subtract one.

LoadLevel()
: Disables the death text.
Enables the loading text, disables the buttons and waits for a second, before loading the previous scene.

## Variables
{type="wide" sorted="asc"}
retryBtn
: The button used to restart the game.

quitBtn
: The button used to close the game.

deathText
: The text displaying that the player died.

loadingText
: The text displayed when loading a scene.

crossFadeObj
: The parent game object of the scene transition UI elements.

_crossFadeImg
: The image component used for scene transitions.
