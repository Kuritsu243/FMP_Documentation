# DeathScreen.cs

## Description

## Script
```C#
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


        private void Start()
        {
            deathText.enabled = true;
            loadingText.enabled = false;
            retryBtn.onClick.AddListener(RetryGame);
            quitBtn.onClick.AddListener(CloseGame);

            crossFadeObj.GetComponent<Animator>();
        }

        private void RetryGame()
        {
            LoadPreviousScene();
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