# Sound Effects
<show-structure depth="2" />

## Description

To make my game feel more complete, I added a few sound effects to my game. I would have ideally wanted to implement even more, however due to 
time constraints I was unable to do so. I sourced all of my sounds from [FreeSound](https://freesound.org) - every sound published on this website is
published under the Creative Commons licenses that allow the re-use of these sounds within any projects.

## Weapon Sounds

These sounds are all one-shot audio clips within the scene.  These sounds are played by calling `PlayOneShot()` on the Player's Audio Source Component.

### Shotgun

I only managed to add one sound to my shotgun, played as a one shot whenever it is fired in-game.

<video src="shotgun_sound_showcase.mp4" />

### Pistol

I added two sounds to the pistol Game Object in my project, one for firing and one for reloading.

#### Firing Sound

<video src="pistol_firing_showcase.mp4" />

#### Reloading Sound

<video src="pistol_reloading_showcase.mp4" />

## Environment Sounds

These sound effects are set up by having an Audio Source component attached to the Game Object that will be playing these sounds. I have changed the value of the
Audio Source component's "Spatial Blend" variable to maximum, so that it gets louder / quieter depending on distance from the player:

![Showcase of the Audio Source Component settings used for environment sounds within my game](audio_source_component_settings.png)

### Campfire

I added a simple looping campfire sound effect, added to the campfire(s) in my scenes.

<video src="campfire_showcase.mp4" />

### Portal

I added an ambient sound effect to my portal(s) within my scenes.

<video src="portal_ambient_sound.mp4" />