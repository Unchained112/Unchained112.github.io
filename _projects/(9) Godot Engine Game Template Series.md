---
name: Simple Game Templates for Godot Engine
tools: [Godot Engine, Python]
image:
description: A series of Godot engine game templates
external_url: 
---

## Simple main menu template for Godot 4

- [Playable demo at itch.io](https://unchain112.itch.io/simple-concept-template-main-menu)
- [Godot Asset Library page](https://godotengine.org/asset-library/asset/2578)
- [Github repo](https://github.com/Unchained112/SimpleTemplateMainMenu)

The default play scene includes a white box that can be moved with W, A, S, D.

Features:

- A basic start menu with Play, Options, and Quit buttons.
- A basic options menu with three tabs:
- Video tab: Fullscreen, Borderless, Vsync
- Music tab: Master, Music, Sound Effects
- Control tab: Up, Down, Left, Right
- A sample control testing scene (Press the Play button in the start menu)
- An auto-loaded Scene Manager
- Persistence (Not available in the web build)
- An auto-loaded Audio Manager
- Support for keyboard-only control (grab-focus in every menu)

PS:

- Persistence and Scene Manager are both located in Utilities.
- Persistence is not enabled by default; set it in Utilities.gd using is_persistence."


## A simple concept godot game template for 2D Top-Down-Shooter.

- [Playable demo at itch.io](https://unchain112.itch.io/simple-concept-template-2d-top-down-shooter)
- [Godot Asset Library page](https://godotengine.org/asset-library/asset/2460)
- [Github repo](https://github.com/Unchained112/SimpleTopDownShooterTemplate2D)

Features:
- Ready to use right away.
- A simple character including two parts: a rotating body for gun / space ship, a left-right body.
- A simple enemy with moving animation.
- Damage feedbacks including damage number, blink animation (done by the shader), a cross displayed when - enemy killed.
- Moving trail (Particle effect).
- Bullet related particle effects (shot, hit).
- Camera shake when enemy killed.
- Blood particle effect.
- Simple sound effect.

PS:

-The character sprites are Polygon2D node. If you need replace it with your own, remember to edit the animation if the sprite is changed. The the window scratch is set to be "viewport" and "expand".

