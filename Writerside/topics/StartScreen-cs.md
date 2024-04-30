# StartScreen.cs
<show-structure depth="2" />

## Description

## Script
```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using TMPro;
using UnityEditor;
using UnityEngine;
using UnityEngine.Audio;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

namespace UI
{
    // src https://www.youtube.com/watch?v=CE9VOZivb3I
    public class StartScreen : MonoBehaviour
    {
        [SerializeField] private GameObject crossFadeObj;
        [SerializeField] private TextMeshProUGUI loadingText;
        [Header("Buttons")]
        [SerializeField] private Button startBtn;
        [SerializeField] private Button aboutBtn;
        [SerializeField] private Button closeBtn;
        [SerializeField] private Button settingsBtn;
        [SerializeField] private Button applyBtn;
        [SerializeField] private Button aboutCloseBtn;
        [SerializeField] private Button settingsCloseBtn;   
        [Header("Settings Menu")] 
        [SerializeField] private TMP_Dropdown resolutionDropdown;
        [SerializeField] private Slider volumeSlider;
        [SerializeField] private Toggle fullscreenToggle;
        [SerializeField] private AudioMixer audioMixer;
        [SerializeField] private float currentVolume;
        [Header("UI Panel Objects")]
        [SerializeField] private GameObject aboutMenu;
        [SerializeField] private GameObject settingsMenu;
        private List<Resolution> _screenResolutions;
        private static readonly int Start1 = Animator.StringToHash("Start");
        private Animator _crossFadeAnim;
        private int _currentResIndex;
        private bool _hasFaded;
        private void Start()
        {
            loadingText.enabled = false;
            startBtn.onClick.AddListener(StartGame);
            closeBtn.onClick.AddListener(CloseGame);
            aboutBtn.onClick.AddListener(ShowAboutMenu);
            aboutCloseBtn.onClick.AddListener(CloseAboutMenu);
            settingsBtn.onClick.AddListener(ShowSettingsMenu);
            settingsCloseBtn.onClick.AddListener(CloseSettingsMenu);
            resolutionDropdown.onValueChanged.AddListener(SetResolution);
            fullscreenToggle.onValueChanged.AddListener(ToggleFullscreen);
            volumeSlider.onValueChanged.AddListener(SetVolume);
            applyBtn.onClick.AddListener(SaveSettings);
            if (!crossFadeObj.activeSelf) crossFadeObj.SetActive(true);
            if (aboutMenu.activeSelf) aboutMenu.SetActive(false);
            if (settingsMenu.activeSelf) settingsMenu.SetActive(false);
            InitializeResolutions();
            LoadSettings(_currentResIndex);
            _crossFadeAnim = crossFadeObj.GetComponent<Animator>();
        }
        
        private void StartGame()
        {
            LoadNextScene();
        }
        
        private void ShowAboutMenu()
        {
            ToggleButtonInteractivity(false);
            if (!CheckButtonInteractivity()) return;
            CloseAllMenus();
            aboutMenu.SetActive(true);
        }

        private void CloseAboutMenu()
        {
            ToggleButtonInteractivity(true);
            if (!aboutMenu.activeSelf) return;
            aboutMenu.SetActive(false);
        }

        private void ShowSettingsMenu()
        {
            ToggleButtonInteractivity(true);
            if (!CheckButtonInteractivity()) return;
            CloseAllMenus();
            settingsMenu.SetActive(true);
        }

        private void CloseSettingsMenu()
        {
            ToggleButtonInteractivity(true);
            if (!settingsMenu.activeSelf) return;
            settingsMenu.SetActive(false);
        }

        private bool CheckButtonInteractivity()
        {
            return !(settingsMenu.activeSelf || aboutMenu.activeSelf);
        }

        private void ToggleButtonInteractivity(bool yn)
        {
            startBtn.interactable = yn;
            aboutBtn.interactable = yn;
            closeBtn.interactable = yn;
            settingsBtn.interactable = yn;
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

        private void LoadNextScene()
        {
            StartCoroutine(LoadLevel(SceneManager.GetActiveScene().buildIndex + 1));
        }

        private void CloseAllMenus()
        {
            settingsMenu.SetActive(false);
            aboutMenu.SetActive(false);
            ToggleButtonInteractivity(true);
        }
        
        private void ToggleFullscreen(bool isFullscreen)
        {
            Screen.fullScreen = isFullscreen;
        }
                
        private void SetResolution(int resolutionIndex)
        {
            var resolution = _screenResolutions[resolutionIndex];
            Screen.SetResolution(resolution.width, resolution.height, Screen.fullScreen);
        }
        
        private void SetVolume(float volume)
        {
            audioMixer.SetFloat("Volume", volume);
            currentVolume = volume;
        }

        private void SaveSettings()
        {
            PlayerPrefs.SetInt("FullScreenPreference", Convert.ToInt32(Screen.fullScreen));
            PlayerPrefs.SetInt("ResolutionPreference", resolutionDropdown.value);
            PlayerPrefs.SetFloat("VolumePreference", currentVolume);
        }

        private void LoadSettings(int resolutionIndex)
        {
            Screen.fullScreen = PlayerPrefs.HasKey("FullScreenPreference") &&
                                Convert.ToBoolean(PlayerPrefs.GetInt("FullScreenPreference"));

            resolutionDropdown.value = PlayerPrefs.HasKey("ResolutionPreference")
                ? PlayerPrefs.GetInt("ResolutionPreference")
                : resolutionIndex;

            volumeSlider.value = PlayerPrefs.HasKey("VolumePreference") ? PlayerPrefs.GetFloat("VolumePreference") : 1f;
        }

        private void InitializeResolutions()
        {
            resolutionDropdown.ClearOptions();
            var options = new List<string>();
            _screenResolutions = Screen.resolutions.ToList();
            foreach (var t in _screenResolutions)
            {
                var option = t.width + " x " + t.height;
                options.Add(option);
                if (t.width == Screen.currentResolution.width &
                    t.height == Screen.currentResolution.height)
                {
                    _currentResIndex = _screenResolutions.IndexOf(t);
                }
            }
            resolutionDropdown.AddOptions(options);
            resolutionDropdown.RefreshShownValue();
        }

        private IEnumerator LoadLevel(int levelIndex)
        {
            loadingText.enabled = true;
            _crossFadeAnim.SetTrigger(Start1);
            yield return new WaitForSeconds(1f);
            SceneManager.LoadSceneAsync(levelIndex);
        }
    }
}

```
{collapsed-title="StartScreen.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: disables the loading text.
Adds listeners to all the buttons to be used within this scene.
Enables / Disables the relevant UI elements.
Initializes the resolutions that can be used by the game.
Loads the current settings saved via player prefs.
Stores a reference to the scene transition UI elements.

StartGame()
: calls the LoadNextScene function.

ShowAboutMenu()
: Shows the about menu.
Disables button interactivity, and if any UI elements are already open then closes them.
Enables the About Menu.

CloseAboutMenu()
: Enables button interactivity.
Disables the About Menu.

ShowSettingsMenu()
: Toggles button interactivity.
Closes all menus, then activates the settings menu.

CloseSettingsMenu()
: Toggles button interactivity.
Disables the settings menu.

CheckButtonInteractivity()
: Checks if the settings menu or about menu is active in the scene.

ToggleButtonInteractivity()
: Sets the interactivity of the UI buttons dependent on the boolean value passed through as input.

CloseGame()
: Checks if in editor or a deployed build.
If in editor, exit play mode.
If in build, close the game.

LoadNextScene()
: Calls the LoadLevel corotuine, with the next scene index passed through as input.

CloseAllMenus()
: Enables button interactivity, and closes all UI panels.

ToggleFullScreen()
: Updates whether the game is full screen or not.

SetResolution()
: Updates the resolution based on what has been selected.
Uses the index of the res, and then gets the width and height from the index.

SetVolume()
: Changes the volume value of the Audio Mixer to the value passed through as input.

SaveSettings()
: Stores the current settings via PlayerPrefs.

LoadSettings()
: Uses the values stored via PlayerPrefs to load the previous settings that the player / game has set.

InitilizeResolutions()
: Gets a list of resolutions, and converts it to a list.
Then for each resolution available, converts it to something more legible in text,
and adds it as an option to the dropdown used to set the resolution.
Refreshes the dropdown menu values.

LoadLevel()
: Enables the loading text, and starts the scene transition.
Waits for a second before loading the next scene.

## Variables
{type="wide" sorted="asc"}
crossFadeObj
: The parent object for the scene transition UI elements.

loadingText
: The text used to indicate the game is loading a scene.

startBtn
: The button used to start the game.

aboutBtn
: The button used to view the about menu.

closeBtn
: The button used to close the game.

settingsBtn
: The button used to view the settings menu.

applyBtn
: The button used to update the active settings.

aboutCloseBtn
: The button used to close the about menu.

settingsCloseBtn
: The button used to close the settings menu.

resolutionDropdown
: The dropdown list to display resolutions.

volumeSlider
: The slider used to control the games volume.

fullScreenToggle
: The toggle used to set full screen or not.

audioMixer
: The audio mixer controlled by the game, handles volume.

currentVolume
: The volume at the start.

aboutMenu
: The parent game object to show the about menu UI elements.

settingsMenu
: The parent game object to show the settings menu UI elements.

_screenResolutions
: The list of resolutions for the game.

Start1
: The hashed reference for the trigger of the scene transition animator.

_crossFadeAnim
: The animator component for the scene transitions.

_currentResIndex
: The current index of the active resolution.

_hasFaded
: Has the scene transition faded already.