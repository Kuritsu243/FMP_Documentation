# TutorialController.cs
<show-structure depth="2" />

## Description
This is the script to control all the logic during the tutorial scene.
## Script
```C#
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using Environment;
using Player;
using TMPro;
using UnityEngine;
using UnityEngine.Serialization;
using UnityEngine.UI;

namespace Tutorial
{
    /** sources used:
     * https://stackoverflow.com/questions/70073128/how-to-check-if-all-values-of-a-c-sharp-dictionary-are-true
     * https://imran-momin.medium.com/dictionaries-unity-c-69b48448445f
     * https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.keyvaluepair-2?view=netframework-4.8
     */
    public static class ImageTweening
    {
        public static void ClearAlpha(ref Image img, bool loop)
        {
            var tempImg = img;
            if (loop)
            {
                LeanTween.value(img.gameObject, img.color.a, 0f, 3f).setOnUpdate(val =>
                {
                    Color c = tempImg.color;
                    c.a = val;
                    tempImg.color = c;
                }).setLoopPingPong();
            }
            else
            {
                LeanTween.value(img.gameObject, img.color.a, 0f, 3f).setOnUpdate(val =>
                {
                    Color c = tempImg.color;
                    c.a = val;
                    tempImg.color = c;
                });
            }

            img = tempImg;
        }

        public static void ClearTextAlpha(ref TextMeshProUGUI text, bool loop)
        {
            var tempTxt = text;

            if (loop)
            {
                LeanTween.value(text.gameObject, 1, 0f, 1.5f).setOnUpdate(val =>
                {
                    Color c = tempTxt.color;
                    c.a = val;
                    tempTxt.color = c;
                }).setLoopPingPong();
            }
            else
            {
                LeanTween.value(text.gameObject, 1, 0f, 1.5f).setOnUpdate(val =>
                {
                    Color c = tempTxt.color;
                    c.a = val;
                    tempTxt.color = c;
                });
            }

            text = tempTxt;
        }


        public static void FillAlpha(ref Image img, bool loop)
        {
            var tempImg = img;
            if (loop)
            {
                LeanTween.value(img.gameObject, img.color.a, 1f, 3f).setOnUpdate(val =>
                {
                    Color c = tempImg.color;
                    c.a = val;
                    tempImg.color = c;
                }).setLoopPingPong();
            }
            else
            {
                LeanTween.value(img.gameObject, img.color.a, 1f, 3f).setOnUpdate(val =>
                {
                    Color c = tempImg.color;
                    c.a = val;
                    tempImg.color = c;
                });
            }

            img = tempImg;
        }

        public static void AlphaPrompt(ref TextMeshProUGUI text, ref Image imgMain, ref Image imgAlt, bool loop)
        {
            ClearTextAlpha(ref text, loop);
            ClearAlpha(ref imgMain, loop);
            FillAlpha(ref imgAlt, loop);
        }


        public static void ChangePrompt(ref TextMeshProUGUI text, ref GameObject oldPrompt, ref GameObject newPrompt,
            ref Image newImg, ref Image newImgAlt, ref Image oldImg, ref Image oldImgAlt,
            ref Dictionary<int, string> textList, int textIndex)
        {
            var tempTxt = text;
            ClearAlpha(ref oldImg, false);
            ClearAlpha(ref oldImgAlt, false);
            ClearTextAlpha(ref text, false);
            LeanTween.cancel(text.gameObject);
            LeanTween.cancel(oldImg.gameObject);
            LeanTween.cancel(oldImgAlt.gameObject);
            LeanTween.value(text.gameObject, 1, 1f, 1.5f).setOnUpdate(f =>
            {
                Color c = tempTxt.color;
                c.a = f;
                tempTxt.color = c;
            });

            text.text = textList[textIndex];
            oldPrompt.SetActive(false);
            newPrompt.SetActive(true);

            ClearTextAlpha(ref text, true);
            ClearAlpha(ref newImg, true);
            FillAlpha(ref newImgAlt, true);
        }

        public static void ChangeTextPromptOnly(ref TextMeshProUGUI text, ref Dictionary<int, string> textList,
            int textIndex)
        {
            var tempTxt = text;
            ClearTextAlpha(ref text, false);
            LeanTween.value(text.gameObject, 1f, 1f, 1.5f).setOnUpdate(f =>
            {
                Color c = tempTxt.color;
                c.a = f;
                tempTxt.color = c;
            });
            text.text = textList[textIndex];
            ClearTextAlpha(ref text, true);
        }
    }


    public class TutorialController : MonoBehaviour
    {
        [SerializeField] private Canvas canvas;
        [SerializeField] private PlayerController playerController;
        [SerializeField] private FloatingWallController floatingWallController;
        [SerializeField] private GameObject tutorialPistol;
        [SerializeField] private TutorialEnemyController tutorialEnemyController;
        [SerializeField] private GameObject endComputer;

        [FormerlySerializedAs("Prompt_W")] [Header("Input Prompts")] 
        [SerializeField] private GameObject promptW;
        [SerializeField] private GameObject promptS;
        [SerializeField] private GameObject promptA;
        [SerializeField] private GameObject promptD;
        [SerializeField] private GameObject promptJump;
        [SerializeField] private GameObject promptComplete;
        [SerializeField] private Image keyPressW;
        [SerializeField] private Image keyPressWalt;
        [SerializeField] private Image keyPressS;
        [SerializeField] private Image keyPressSalt;
        [SerializeField] private Image keyPressA;
        [SerializeField] private Image keyPressAalt;
        [SerializeField] private Image keyPressD;
        [SerializeField] private Image keyPressDalt;
        [SerializeField] private Image keyPressSpace;
        [SerializeField] private Image keyPressSpacealt;
        [SerializeField] private Image keyPressComplete;
        [SerializeField] private Image keyPressCompletealt;

        [Header("Tutorial Text Prompts")] 
        [SerializeField] private TextMeshProUGUI tutorialTextHint;

        [Header("Islands")] 
        [SerializeField] private GameObject enemyIsland;

        [Header("Portal")] 
        [SerializeField] private GameObject portal;
        [SerializeField] private SpriteRenderer portalSpriteRenderer;


        private HighlightWeapon _pistolOutline;
        private HighlightComputer _computerOutline;
        private bool _areWallsAppearing;
        private bool _isWeaponGlowing;
        private bool _hasEnemyIslandAppeared;
        public bool hasFiredPistolYet;


        public enum NextKeyPress
        {
            Forward,
            Backwards,
            Left,
            Right,
            Jump,
            Complete
        }

        public NextKeyPress nextKeyToPress;
        public Dictionary<string, bool> TutorialChecks;
        public Dictionary<string, bool> EnemyChecks;
        public Dictionary<string, bool> WallRunChecks;
        private Dictionary<int, string> _introductionTexts;
        private Dictionary<int, string> _inputPromptTexts;
        private Dictionary<int, string> _wallRunPromptTexts;
        private Dictionary<int, string> _weaponPromptTexts;
        private Dictionary<int, string> _enemyIslandTexts;
        private Dictionary<int, string> _challengeCompleteTexts;

        private void Start()
        {
            promptW.SetActive(false);
            promptS.SetActive(false);
            promptA.SetActive(false);
            promptD.SetActive(false);
            promptJump.SetActive(false);
            promptComplete.SetActive(false);
            enemyIsland.SetActive(false);
            portal.SetActive(false);


            TutorialChecks = new Dictionary<string, bool>
            {
                { "IntroductionComplete", false },
                { "Forward", false },
                { "Backwards", false },
                { "Left", false },
                { "Right", false },
                { "Jump", false }
            };

            WallRunChecks = new Dictionary<string, bool>
            {
                { "FirstWall", false },
                { "SecondWall", false },
                { "IslandReached", false }
            };

            EnemyChecks = new Dictionary<string, bool>
            {
                { "Equipped", false },
                { "Fired", false },
                { "Missed", false },
                { "Killed", false }
            };

            _introductionTexts = new Dictionary<int, string>
            {
                { 0, "Welcome to this tutorial!" },
                { 1, "I'll be your teacher today." },
                { 2, "First, lets familiarize ourselves with this Games Controls." }
            };


            _inputPromptTexts = new Dictionary<int, string>
            {
                { 0, "Press W to move Forward" },
                { 1, "Press S to move Backwards" },
                { 2, "Press A to move Left" },
                { 3, "Press D to move Right" },
                { 4, "Press Space to Jump" },
                { 5, "Movement Tutorial Complete!" },
            };

            _wallRunPromptTexts = new Dictionary<int, string>
            {
                { 0, "Huh, moving, floating walls. Didn't expect that." },
                { 1, "Try wall running to the next island." },
                { 2, "Jump between the walls by pressing Space." },
                { 3, "You did it! Nice work." },
                { 4, "Time to explore this island." }
            };

            _weaponPromptTexts = new Dictionary<int, string>
            {
                { 0, "Oh, a free gun!" },
                { 1, "Press F to pickup the gun." },
                { 2, "Look nearby the campfire, there's a person." },
                { 3, "Shoot the person by pressing Mouse1." },
                { 4, "SHOOT. THEM." },
                { 5, "It'd help if you actually aimed at the person." },
                { 6, "Good Job." },
                { 7, "So uhhh, what now..." },
                { 8, "Come here often?" }
            };

            _enemyIslandTexts = new Dictionary<int, string>
            {
                { 0, "Ah. Shit." },
                { 1, "These guys don't seem too happy." },
                { 2, "Time to kill them I guess." }
            };

            _challengeCompleteTexts = new Dictionary<int, string>
            {
                { 0, "That's those guys taken care of. " },
                { 1, "Huh, what's that device over there?" },
                { 2, "I should press this button." },
                { 3, "Hmm. It's doing nothing." },
                { 4, "Nevermind, spoke too soon." },
                { 5, "A giant portal! Lets go through it. Nothing bad ever happens with portals." }
            };


            tutorialTextHint.text = _introductionTexts[0];
            _pistolOutline = tutorialPistol.GetComponent<HighlightWeapon>();
            _computerOutline = endComputer.GetComponent<HighlightComputer>();
            tutorialEnemyController = GetComponent<TutorialEnemyController>();
            ImageTweening.ClearTextAlpha(ref tutorialTextHint, true);
            ImageTweening.ClearAlpha(ref keyPressW, true);
            ImageTweening.FillAlpha(ref keyPressWalt, true);
            nextKeyToPress = NextKeyPress.Forward;
            StartCoroutine(IntroductionText());
        }


        public bool IntroComplete()
        {
            return TutorialChecks["IntroductionComplete"];
        }

        public void OtherIslandReached()
        {
            WallRunChecks["IslandReached"] = true;
            if (!_isWeaponGlowing)
                StartCoroutine(StartWeaponTutorial());
        }

        public void PistolCollected()
        {
            EnemyChecks["Equipped"] = true;
            StartCoroutine(PistolRelatedDialogue());
        }

        public void ActuallyAim()
        {
            if (tutorialTextHint.text != _weaponPromptTexts[4]) return;
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 5);
        }

        public void TutorialEnemyKilled()
        {
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 6);
            StartCoroutine(SpawnEnemyIsland());
        }

        public void EnemyChallengeComplete()
        {
            StartCoroutine(EnemiesAreKilled());
        }

        private IEnumerator EnemiesAreKilled()
        {
            tutorialTextHint.gameObject.SetActive(true);
            ImageTweening.ClearTextAlpha(ref tutorialTextHint, true);
            yield return new WaitForSeconds(0.8f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _challengeCompleteTexts, 0);
            yield return new WaitForSeconds(2.5f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _challengeCompleteTexts, 1);
            _computerOutline.OutlineComputer();
        }

        private IEnumerator SpawnPortal()
        {
            yield return new WaitForSeconds(1.2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _challengeCompleteTexts, 4);
            portal.SetActive(true);
            LeanTween.value(portal, 0f, 1f, 3.5f).setOnUpdate(f =>
            {
                Color c = portalSpriteRenderer.color;
                c.a = f;
                portalSpriteRenderer.color = c;
            }).setOnComplete(() =>
            {
                endComputer.LeanMoveLocalY(3.25f, 0.4f).setOnComplete(() =>
                {
                    endComputer.LeanRotateX(-25f, 0.5f).setOnComplete(() =>
                    {
                        endComputer.LeanMoveLocalY(3.825f, 0.4f).setOnComplete(() =>
                        {
                            endComputer.LeanRotateX(-45f, 0.25f).setOnComplete(() =>
                            {
                                endComputer.LeanMoveLocalX(-8.5f, 0.25f).setOnComplete(() =>
                                {
                                    endComputer.SetActive(false);
                                    ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint,
                                        ref _challengeCompleteTexts, 5);
                                });
                            });
                        });
                    });
                });
            });
        }

        private IEnumerator SpawnEnemyIsland()
        {
            yield return new WaitForSeconds(3f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 7);
            yield return new WaitForSeconds(2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 8);
            enemyIsland.SetActive(true);
            yield return new WaitForSeconds(1f);
            LeanTween.moveY(enemyIsland, -2.5f, 5f).setOnComplete(() =>
            {
                LeanTween.moveX(enemyIsland, -2f, 3f);
                _hasEnemyIslandAppeared = true;
            });
            yield return new WaitUntil(() => _hasEnemyIslandAppeared);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _enemyIslandTexts, 0);
            yield return new WaitForSeconds(2f);
            LeanTween.moveX(enemyIsland, -2f, 3f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _enemyIslandTexts, 1);
            yield return new WaitForSeconds(3f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _enemyIslandTexts, 2);
            yield return new WaitForSeconds(1.2f);
            tutorialEnemyController.StartKillChallenge();
            LeanTween.cancel(tutorialTextHint.gameObject);
            LeanTween.value(tutorialTextHint.gameObject, 1f, 0f, 1.5f).setOnUpdate(f =>
            {
                Color c = tutorialTextHint.color;
                c.a = f;
                tutorialTextHint.color = c;
            }).setOnComplete(() => tutorialTextHint.gameObject.SetActive(false));
        }


        public void ComputerInteracted()
        {
            _computerOutline.StopOutline();
            StartCoroutine(FinalPrompts());
        }

        private IEnumerator FinalPrompts()
        {
            yield return new WaitForSeconds(1f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _challengeCompleteTexts, 3);
            yield return new WaitForSeconds(2f);
            StartCoroutine(SpawnPortal());
        }

        private IEnumerator CompleteFirstPrompt()
        {
            yield return new WaitForSeconds(2f);
            LeanTween.value(tutorialTextHint.gameObject, 1f, 0f, 1.5f).setOnUpdate(f =>
            {
                Color c = tutorialTextHint.color;
                c.a = f;
                tutorialTextHint.color = c;
            }).setOnComplete(() =>
                {
                    LeanTween.cancel(keyPressComplete.gameObject);
                    LeanTween.cancel(keyPressCompletealt.gameObject);
                    promptComplete.SetActive(false);
                    StartCoroutine(StartWallRunPrompt());
                }
            );
        }

        private IEnumerator StartWallRunPrompt()
        {
            yield return new WaitForSeconds(2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _wallRunPromptTexts, 0);
            yield return new WaitForSeconds(2.8f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _wallRunPromptTexts, 1);
            yield return new WaitUntil(() => WallRunChecks["FirstWall"]);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _wallRunPromptTexts, 2);
            yield return new WaitUntil(() => WallRunChecks["SecondWall"]);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _wallRunPromptTexts, 3);
            yield return new WaitUntil(() => WallRunChecks["IslandReached"]);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _wallRunPromptTexts, 4);
        }

        private void FixedUpdate()
        {
            var allIsTrue = TutorialChecks.Values.All(value => value);
            if (allIsTrue && !_areWallsAppearing)
                StartCoroutine(MakeWallsAppear());
        }

        private IEnumerator IntroductionText()
        {
            yield return new WaitForSeconds(2.5f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _introductionTexts, 1);
            yield return new WaitForSeconds(2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _introductionTexts, 2);
            yield return new WaitForSeconds(1.6f);
            StartCoroutine(InputPrompts());
        }

        private IEnumerator InputPrompts()
        {
            ImageTweening.ClearTextAlpha(ref tutorialTextHint, false);
            yield return new WaitForSeconds(2f);
            tutorialTextHint.text = _inputPromptTexts[0];
            promptW.SetActive(true);
            ImageTweening.AlphaPrompt(ref tutorialTextHint, ref keyPressW, ref keyPressWalt, true);
            TutorialChecks["IntroductionComplete"] = true;
            yield return new WaitUntil(() => TutorialChecks["Forward"]);
            ImageTweening.ChangePrompt(ref tutorialTextHint, ref promptW, ref promptS, ref keyPressS,
                ref keyPressSalt, ref keyPressW, ref keyPressWalt, ref _inputPromptTexts, 1);
            nextKeyToPress = NextKeyPress.Backwards;
            yield return new WaitUntil(() => TutorialChecks["Backwards"]);
            ImageTweening.ChangePrompt(ref tutorialTextHint, ref promptS, ref promptA, ref keyPressA,
                ref keyPressAalt, ref keyPressS, ref keyPressSalt, ref _inputPromptTexts, 2);
            nextKeyToPress = NextKeyPress.Left;
            yield return new WaitUntil(() => TutorialChecks["Left"]);
            ImageTweening.ChangePrompt(ref tutorialTextHint, ref promptA, ref promptD, ref keyPressD,
                ref keyPressDalt, ref keyPressA, ref keyPressAalt, ref _inputPromptTexts, 3);
            nextKeyToPress = NextKeyPress.Right;
            yield return new WaitUntil(() => TutorialChecks["Right"]);
            ImageTweening.ChangePrompt(ref tutorialTextHint, ref promptD, ref promptJump, ref keyPressSpace,
                ref keyPressSpacealt, ref keyPressD, ref keyPressDalt, ref _inputPromptTexts, 4);
            nextKeyToPress = NextKeyPress.Jump;
            yield return new WaitUntil(() => TutorialChecks["Jump"]);
            nextKeyToPress = NextKeyPress.Complete;
            ImageTweening.AlphaPrompt(ref tutorialTextHint, ref keyPressSpace, ref keyPressSpacealt, false);
            tutorialTextHint.text = _inputPromptTexts[5];
            promptJump.SetActive(false);
            promptComplete.SetActive(true);
            ImageTweening.ClearTextAlpha(ref tutorialTextHint, true);
            ImageTweening.ClearAlpha(ref keyPressComplete, true);
            StartCoroutine(CompleteFirstPrompt());
        }

        private IEnumerator MakeWallsAppear()
        {
            _areWallsAppearing = true;
            yield return new WaitForSeconds(2f);
            floatingWallController.TriggerWallMovement();
        }

        private IEnumerator StartWeaponTutorial()
        {
            yield return new WaitForSeconds(1.2f);
            _isWeaponGlowing = true;
            StartCoroutine(ShowWeaponText());
            yield return new WaitForSeconds(1.2f);
            if (_pistolOutline)
                _pistolOutline.OutlineWeapon();
        }

        private IEnumerator ShowWeaponText()
        {
            yield return new WaitForSeconds(1.6f);
            tutorialTextHint.gameObject.SetActive(true);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 0);
            if (EnemyChecks["Fired"]) yield break;
            yield return new WaitForSeconds(2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 1);
        }

        private IEnumerator PistolRelatedDialogue()
        {
            yield return new WaitForSeconds(1.2f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 2);
            yield return new WaitForSeconds(1.8f);
            ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 3);
            var timerStart = Time.time;
            yield return new WaitUntil(() => Time.time - timerStart > 5f || EnemyChecks["Fired"]);
            switch (EnemyChecks["Fired"])
            {
                case false when !EnemyChecks["Killed"]:
                    ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 4);
                    break;
                case true when !EnemyChecks["Killed"]:
                    ImageTweening.ChangeTextPromptOnly(ref tutorialTextHint, ref _weaponPromptTexts, 5);
                    break;
            }
        }
    }
}
```
{collapsible="true" collapsed-title="TutorialController.cs"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Disables the tutorial prompt UI elements.
Creates dictionaries with the dialogue to be shown during this tutorial.
Sets the current text to the first index of the introduction.
Stores references to the necessary parts required by this script.
Sets up tweening for the text to fade out and in repeatedly.
Calls the introduction coroutine, and sets up the required key prompts.

EnemiesAreKilled()
: Enables the tutorial text hint,
and then shows the dialogue required once the player has killed the enemies during the kill challenge.

SpawnPortal()
: Spawns the portal at the end of the game.
Tweens the portal materials alpha so that it appears that it fades into the scene.
Sucks the computer into the portal.
Updates the text displayed throughout this.

SpawnEnemyIsland()
: Displays the text prompts relevant to the enemy island appearing.
Moves the island to appear.
Starts the kill challenge, and checks when it is completed. 

FinalPrompts()
: Displays the final prompts at the end of the tutorial before the portal appears.

CompleteFirstPrompt()
: Hides the input prompt UI elements.
starts the next tutorial section by making the walls appear.

StartWallRunPrompt()
: Updates the dialogue displayed, dependent on when the player has reached certain sections of the wall running tutorial.

FixedUpdate()
: Constantly checks if all the prompt entries in the dictionary are true.
If so, then makes the walls appear.

IntroductionText()
: Controls the dialogue to be displayed at the start of the game. 

InputPrompts()
: Controls the dialogue during the input tutorial.

MakeWallsAppear()
: Waits two seconds before making the walls appear, and continuing the tutorial.

StartWeaponTutorial()
: Starts the weapon tutorial and tells the player to pick-up the weapon.
Enables the pistol outline.

ShowWeaponText()
: Continued prompts once the player has picked up the weapon.

PistolRelatedDialogue()
: Tells the player to shoot the NPC.
Updates dialogue if they miss, or don't shoot the enemy.
Also updates the text once the player kills the enemy.

## Public Methods
{type="wide" sorted="asc"}
IntroComplete()
: Returns whether the player has completed the introduction or not.

OtherIslandReached()
: Tells the game that the player has reached the second island, then continues the weapon tutorial.

PistolCollected()
: Tells the game that the player has collected the weapon, then continues the weapon dialogue.

ActuallyAim()
: Displays relevant dialogue if the player misses the enemy when shooting for the first time.

TutorialEnemyKilled()
: Displays relevant dialogue to when the player kills the non-hostile NPC, and continues the dialogue.

EnemyChallengeComplete()
: Resumes the dialogue prompts once the player has killed the enemies from the kill challenge.

ComputerInteracted()
: Displays the relevant dialogue once the player interacts with the computer at the end of the tutorial.

ClearAlpha()
: Fades an images alpha to zero, and loops dependent on input.

ClearTextAlpha()
: Fades a text's alpha to zero, and loops dependent on input.

FillAlpha()
: Fades an images alpha to full, and loops dependent on input.

AlphaPrompt()
: Fades text and image, and fills an image all at the same time.

ChangePrompt()
: Fades out the old dialogue, cancels the tweens,
updates the dialogue, and then sets up the tweens on that dependent on input.

ChangeTextPromptOnly()
: Fades out the old text, updates the dialogue, and starts tweens on the new dialogue.

## Variables

### Enums
{type="wide" sorted="asc"}
NextKeyPress
: The next key input the player needs to do.

### Components
{type="wide" sorted="asc"}
playerController
: The player controller script attached to the player.

canvas
: The canvas component attached to the UI.

floatingWallController
: The component that controls the floating walls.

tutorialEnemyController
: The component that controls the enemies during the tutorial.

portalSpriteRenderer
: The sprite renderer component that displays the portal.

_pistolOutline
: The component that controls the outline of the weapon.

_computerOutline
: The component that controls the outline of the computer.

### Images
{type="wide" sorted="asc"}
keypressW
: The component displaying the W keyboard image.

keypressWalt
: The component displaying the W keyboard background image.

keypressS
: The component displaying the S keyboard image.

keypressSalt
: The component displaying the S keyboard background image.

keypressA
: The component displaying the A keyboard image.

keypressAalt
: The component displaying the A keyboard background image.

keypressD
: The component displaying the D keyboard image.

keypressDalt
: The component displaying the D keyboard background image.

keypressSpace
: The component displaying the Spacebar keyboard image.

keypressSpacealt
: The component displaying the Spacebar keyboard background image.

keypressComplete
: The component displaying the check image.

keypressCompleteAlt
: The component displaying the check background image.

### Game Objects
{type="wide" sorted="asc"}
promptW
: The parent object of the UI elements for the press W prompts.

promptS
: The parent object of the UI elements for the press S prompts.

promptA
: The parent object of the UI elements for the press A prompts.

promptD
: The parent object of the UI elements for the press D prompts.

promptJump
: The parent object of the UI elements for the press space prompts.

promptComplete
: The parent object of the UI elements for the intro complete prompts.

tutorialPistol
: The pistol to be picked up by the player during the tutorial.

endComputer
: The computer to be interacted with at the end of the tutorial.

enemyIsland
: The enemy island Game Object.

portal
: The portal Game Object.

### Text
{type="wide" sorted="asc"}
tutorialTextHint
: The text component being shown throughout the tutorial. 

### Booleans
{type="wide" sorted="asc"}
_areWallsAppearing
: Are the walls to wall-run from appearing.

_isWeaponGlowing
: Is the weapon glowing.

_hasEnemyIslandAppeared
: Has the enemy island appeared yet.

hasFiredPistolYet
: Has the player fired the pistol yet.

### Dictionaries
{type="wide" sorted="asc"}
TutorialChecks
: The checks during the start of the game. 

EnemyChecks
: The checks once the player picks up the weapon.

WallRunChecks
: The checks whilst the player is wall-running.

_introductionTexts
: The texts to be displayed at the start of the game.

_inputPromptTexts
: The texts to be displayed during the input tutorial.

_wallRunPromptTexts
: The texts to be displayed whilst wall-running.

_weaponPromptTexts
: The texts to be displayed when the player first finds the weapon, and has to kill the NPC.

_enemyIslandTexts
: The texts to be displayed when the enemy island appears.

_challengeCompleteTexts
: The texts to be displayed upon the enemies being killed, and the portal appearing.

