# Game Script

This is the script I have used for my tutorial scene. This is shown via text dialogue on the screen, acting as the game
telling the player what to do. I implemented this into my [TutorialController.cs Script](TutorialController-cs.md) via 
the use of dictionaries. Each section / segment of dialogue is in its own dictionary, allowing me to understand
what sections are being displayed on the screen at a certain point of time during gameplay.

## Dialogue

These are sorted by their index value, to show the rough order that this segments of dialogue appear as. In some segments
i.e. the dialogue displayed when the player arrives at the larger island, is dependent on the actions the player takes
when the game tells the player to shoot the non-hostile NPC.

### Introduction 

| Index | Text                                                        |
|:-----:|-------------------------------------------------------------|
|   0   | Welcome to this tutorial!                                   |
|   1   | I'll be your teacher today.                                 |
|   2   | First, lets familiarize ourselves with this Games Controls. |

### Input Prompts - Movement Tutorial

| Index | Text                        |
|:-----:|-----------------------------|
|   0   | Press W to move Forward     |
|   1   | Press S to move Backwards   |
|   2   | Press A to move Left        |
|   3   | Press D to move Right       |
|   4   | Press Space to Jump         |
|   5   | Movement Tutorial Complete! |

### Floating Walls Appear

| Index | Text                                             |
|:-----:|--------------------------------------------------|
|   0   | Huh, moving, floating walls. Didn't expect that. |
|   1   | Try wall running to the next island.             |
|   2   | Jump between the walls by pressing Space.        |
|   3   | You did it! Nice work.                           |
|   4   | Time to explore this island.                     |


### Player Arrives at Larger Island

| Index | Text                                           |
|:-----:|------------------------------------------------|
|   0   | Oh, a free gun!                                |
|   1   | Press F to pickup the gun.                     |
|   2   | Look nearby the campfire, there's a person.    |
|   3   | Shoot the person by pressing Mouse1.           |
|   4   | SHOOT. THEM.                                   |
|   5   | It'd help if you actually aimed at the person. |
|   6   | Good Job.                                      |
|   7   | So uhhh, what now...                           |
|   8   | Come here often?                               |

### Enemy Island Appears

| Index | Text                             |
|:-----:|----------------------------------|
|   0   | Ah. Shit.                        |
|   1   | These guys don't seem too happy. |
|   2   | Time to kill them I guess.       |

### Upon Killing the New Enemies

| Index | Text                                                                       |
|:-----:|----------------------------------------------------------------------------|
|   0   | That's those guys taken care of.                                           |
|   1   | Huh, what's that device over there?                                        |
|   2   | I should press this button.                                                |
|   3   | Hmm. It's doing nothing.                                                   |
|   4   | Nevermind, spoke too soon.                                                 |
|   5   | A giant portal! Lets go through it. Nothing bad ever happens with portals. |