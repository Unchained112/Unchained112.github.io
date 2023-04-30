---
title: Modern Game Engine Course Notes 05
tags: [Computer Graphics, Game Engine, Technology]
style: border
color: primary
description: Modern Game Engine - From Getting Started To Practice is a free public course about game engine. This part focus on the effects.
---

Course from: [BoomingTech](https://games104.boomingtech.com/sc/)
## Effects


### History of Particle System

The origin of the particle system can be traced back to the movie industry:
    
    “A particle system is a collection of many many minute particles that together represent a fuzzy object. Over a period of time, particles are generated into a system, move and change from within the system, and die from the system.”

    — William Reeves, "Particle Systems—A Technique for Modeling a Class of Fuzzy Objects," ACM Transactions on Graphics 2:2 (April 1983), 92.

### Particle

A particle in game is usually a sprite or 3D model, with the following attributes:
- Position
- Velocity
- Size
- Color
- Lifetime

### Particle

A particle in game is usually a sprite or 3D model, with the following attributes:
- Position
- Velocity
- Size
- Color
- Lifetime

Particle's lifespan 
![Particle Life]({{site.baseurl}}/assets/GameEngine/ParticleLife.png)

### Particle Emitter 

Particle Emitter is used to define the particles simulation
- Specify the spawn rules
- Specify simulation logic
- Describe how to render particles

**Particle System**
A particle system is a collection of individual emitters

![Particle System]({{site.baseurl}}/assets/GameEngine/ParticleSystem.png)

Particle system is crucial to any 3D effects.

The spawn position (single position spawn, spawn based on area, spawn based on mesh), and spawn modes (continuous, burst) will affect the how it looks in the end.

**Simulation** controls how the particles change over time.

### Particle Type

![Particle Type]({{site.baseurl}}/assets/GameEngine/ParticleType.png)


