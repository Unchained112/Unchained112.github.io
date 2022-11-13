---
title: Modern Game Engine Course Notes 03
tags: [Computer Graphics, Game Engine, Technology]
style: border
color: primary
description: Modern Game Engine - From Getting Started To Practice is a free public course about game engine. This part focus on physics system.
---

Course from: [BoomingTech](https://games104.boomingtech.com/sc/)

## Physics System

### Basic Concepts

**Physics Actors and Shapes**

Actor – Summary

- Static Actor
    - Not moving
- Dynamic Actor
    - Can be affected by forces/torques/impulses
- Trigger 
    - Like static actor, not moving
    - But not blocking
    - Notifies when actors enter or exit
- Kinematic Actor
    - Ignoring physics rules
    - Controlled by gameplay logic directly

Actor Shapes

![Actor Shapes]({{site.baseurl}}/assets/GameEngine/ActorShape.png)

Wrap Objects with Physics Shapes
- Approximated Wrapping
- Don’t need to be perfect
- Simplicity
- Prefer simple shapes (avoid triangle mesh if possible)
- Least shapes

Shape Properties
- Mass and density
- Friction and restitution

**Forces**

Force
- We can apply forces to give dynamic objects accelerations, therefore affecting their movements
- Examples • Gravity
- Drag
- Friction

Impulse
- We can change velocity of actors immediately by applying impulses
- E.g. simulating an explosion

**Movements**

Newton’s 1st Law of Motion 

`v(t + dt) = v(t), x(t + dt) = x(t) + v(t)dt`

Newton’s 2nd Law of Motion 

`F = ma, a(t) = dv(t)/dt = d^2x(t)/dt^2`

Example of Simple Movement
- Position
- Orientation
- Linear Velocity
- Angular Velocity

Euler's Method

Explicit (Forward) Euler’s Method

```
v(t + dt) = v(t) + F(t)dt / M
x(t + dt) = x(t) + v(t)dt
```

Pros:
- Easy to calculate, efficient

Cons:
- Poor stability
- Energy growing as time progresses

Implicit (Backward) Euler’s Method

```
v(t + dt) = v(t) + F(t + dt)dt / M
x(t + dt) = x(t) + v(t + dt)dt
```

Pros:
- Unconditionally stable

Cons:
- Expensive to solve
- Challenging to implement when non-linearity presents
- Energy attenuates as time
progresses

Semi-implicit Euler’s Method

```
v(t + dt) = v(t) + F(t)dt / M
x(t + dt) = x(t) + v(t + dt)dt
```

- Conditionally stable
- Easy to calculate, efficient
- Preserves energy as time progresses

**Rigid Body Dynamics**

Rigid body Dynamics

![Rigid body Dynamics]({{site.baseurl}}/assets/GameEngine/RigidbodyDynamics.png)

```
// Orientation:

Matrix
R = [r_xx, r_yx, r_zx
     r_xy, r_yy, r_zy
     r_xz, r_yz, r_zz]
Quaternion
q = (s, v)


// Angular Velocity
omega = (d theta) / dt  = (v x r) / ||r||^2

// Angular Acceleration 
alpha = (d omega) / dt = (a x r) / ||r||^2

// Rotational Inertia
I = R I_0 R^T

// Angular Momentum
L = I omega

// Torque
tau = r x F = dL/dt
```
![Rigid Body Summary]({{site.baseurl}}/assets/GameEngine/RigidSummary.png)

**Collision Detection**

Collision Detection – Two Phases
- Broad phase
    - Find intersected rigid body AABBs
    - Potential overlapped rigid body pairs
- Narrow phase
    - Detect overlapping precisely
    - Generate contact information

Broad Phase
- Objective
    - Find intersected rigid body AABBs
    - Potential overlapped rigid body pairs
- Two approaches
    - Space partitioning: i. e. Boundary Volume Hierarchy (BVH) Tree
    - Sort and Sweep

Broad Phase - BVH 
![Collision Detection BVH]({{site.baseurl}}/assets/GameEngine/CollisionDetectionBVH.png)

Broad Phase - Sort and Sweep

1. Sorting Stage (Initialize)

For each axis
- Sort AABB bounds along each axis when initializing the scene
- Check AABB bounds of actors along each axis
- A_max ≥ B_max indicates potential overlap of A and B

2. Sweeping Stage (Update)
- Only check swapping of bounds
    - temporal coherence
    - local steps from frame to frame
- Swapping of min and max indicates add/delete potential overlap pair from overlaps set
- Swapping of min and min or max and max does not affect overlaps set

Narrow Phase – Objectives
- Detect overlapping precisely
- Generate contact information
    - Contact manifold: approximated with a set of contact points
    - Contact normal
    - Penetration depth

Narrow Phase – Three approaches
- Basic Shape Intersection Test
- Minkowski Difference-based Methods & GJK Algorithm
- Separating Axis Theorem

**Collision Resolution**

Three approaches
- Applying Penalty Force
- Solving Velocity Constraints
- Solving Position Constraints (will be covered in the next lecture)

Solving Constraints
- Modelling constraints based on Lagrangian mechanics
    - Collision constraints: 
        - Non-penetration  
        - Restitution
        - Friction
- Iterative solver

Approaches:
- Sequential impulses
- Semi-implicit integration
- Non-linear Gauss-Seidel Method

Characteristics:
- Fast, stable for most cases
- Commonly used in most physics engines

**Scene Query**

Raycast

- Intersect a user-defined ray with the whole scene
- Point, direction, distance and query mode can be defined
- Multiple hits
- Closet hit
- Any hit

Sweep
- Geometrically similar to raycast
- Shape and pose can be defined
- Box, sphere, capsule and convex

Overlap
- Search a region enclosed by a specified shape for any overlapping objects in the scene
- Box, sphere, capsule and convex

Collision Group
- Actor has a collision group property 
    - Player: Pawn
    - Obstacle: Static 
    - Movable box: Dynamic 
    - Trigger box: Trigger
    - ...

- Scene query can filter collision groups 
    - Player moving query collision group: (Pawn, Static, Dynamic)
    - Trigger query collision group: (Pawn)
    - ...

**Efficiency, Accuracy, and Determinism**

Simulation Optimization - Island

Simulation Optimization – Sleeping
- Simulating and solving all rigid bodies uses lots of resources
- Introducing sleeping
    - A rigid body does not move for a period of time
    - Until some external force acts on it

Continuous Collision Detection 
- Thin obstacle vs. fast moving actors 
- Tunneling
- Solution to tunneling
    - Let it be - something unremarkable
    - Make the floor thicker - boundary air wall
- Time-of-Impact (TOI) – Conservative advancement
    - Estimate a “safe” time substep A and B won’t collide
    - Advance A and B by the “safe” substep
    - Repeat until the distance is below a threshold

Deterministic Simulation 
- Multiplayer game with gameplay-impacting physics
- Small error causes butterfly effect • Synchronizing states requires bandwidth
- Synchronizing inputs requires deterministic simulations

Same old states + same inputs = same new states

Requirements:
- Fixed step of physics simulation
- Deterministic simulation solving sequence
- Float point consistency

### Applications

**Character Controller**

Character Controller vs. Rigid Body Dynamics
- Controllable rigid body interaction
- Almost infinite friction / No restitution
- Accelerate and brake change direction almost instantly and teleport

Legacy Hack in Character Control
- A lot of carefully tweaked values provide a good feeling
- Legacy code in the industry

Build a Controller in Physics System
- Kinematic Actor
    - Not affected by physics rules
    - Push other objects
- Shape: Humanoids 
    - Capsule
    - Box
    - Convex

Collide with environment
- Collision detection with environment 
    - Sweep test
- Auto slide with wall
    - Calculate tangent direction
    - Move along tangent direction

Auto Stepping and its Problem
- Sweep with step offset
- Virtual gap

Slope Limits and Force Sliding Down
- Max climb slopes
- Slide down on steep slopes

Controller Volume Update
- Change the controller volume size at runtime, e.g. crouching
- Overlap test before update to avoid insertion inside objects

Controller Push Objects
- Hit Callback when character controller collides with dynamic actor
- Apply force to dynamic actor

Standing on Moving Platform

**Ragdoll**

Map Skeleton to Rigid Bodies
- Bind key joints with rigid bodies

Importance of Joint Constraints
- The constraints should match the anatomical skeleton
- Adjust by developer

Carefully Tweaked Constraints
- The rigid bodies should fit the mesh as much as possible

Animating Skeleton by Ragdoll
- Update skeleton per frame
    - Intermediate joints -> Bind pose
    - Active joints -> Rigid body pose    
    - Leaf joints -> Animation pose

Blending between Animation and Ragdoll
- Kinematic state ragdoll
    - Rigid bodies are driven by animation
- Dynamic state ragdoll
    - Rigid bodies are simulated by physics

Powered Ragdoll – Physics-Animation Blending
- Blend between the animation pose and the physics pose

**Clothing**

Animation-based Cloth Simulation
- Pipeline
    - Animators produce the animation of bones
    - Generate more animation data via DCC tools
    - Engine replays the animation when running
- Pros
    - Cheap
    - Controllable
- Cons
    - Not realistic
    - Could not interact with environment
    - The designation of clothes is limited

Rigid Body-based Cloth Simulation
- Pipeline
    - The bones of cloth are bound with rigid bodies and constraints
    - The effect are solved by physics engine
- Pros
    - Cheap
    - Interactive
- Cons
    - Undetermined quality
    - Work load for animators
    - Not robust
    - Needs physics engine with high performance

Mesh-based Cloth Simulation
1. Render mesh -> Physics mesh
2. Paint cloth simulation constraints, add maximum radius constraints to each vertex
3. Set cloth physical material
4. Cloth Solver - Mass-Spring system:
    - Spring force: `F_s = k_spring dx`
    - Spring damping force: `F_D = -k_damping v`

![Cloth Solver]({{site.baseurl}}/assets/GameEngine/ClothSolver.png)

Verlet Integration
- Verlet Integration does not need to consider about velocity when calculate, so it is faster.

```
x(t + dt) = 2x(t) - x(t - dt) + a(t)(dt)^2
```

Cloth Solver - Position Based Dynamics (Advanced choice)

Self Collision
- As a kind of flexible material, cloth can fold and collide with itself
- This is pretty tricky in real-time game physics simulation
- Common solutions:
    - Make the cloth thicker (simplest)
    - Use many substeps in one physics simulation step
    - Enforce maximal velocity
    - Introduce contact constraints and friction constraints

**Destruction**

Destruction is Important
- Not only a visual effect
- Making the game world much more vivid and immersive
- A key mechanism in many games 

Chunk Hierarchy
- Organize the fractured chunks level by level
- Different damage threshold for each level

Connectivity Graph - Construct connectivity graph for chunks at the deepest level
- One node per chunk
- One edge if two chunks share a face
- Update at runtime

Connectivity Value - The value on each edge in the connectivity graph
- How much damage needed to break the edge
- Update after each damage
- Break the edge when the value goes to 0

Damage Calculation

1. Calculate damage from impulse at the impact point 
- `I` : the applied impulse (e.g. by collision)
- `H` : the material hardness of the rigid body 
- The damage at the impact point is `D = I / H`

2. Damage distribution
![Damage Distribution]({{site.baseurl}}/assets/GameEngine/DamageDistribution.png)

Build Chunks by Voronoi Diagram
- A partition of a plane into regions close to each of seeds

Voronoi Cell
- the region for each seed
- Points in the cell are closer to the seed than to any other

Fracturing with Voronoi Diagram - 2D Mesh

Pick N random points within the bounding rect of the mesh
- Construct the Voronoi diagram
- Intersect each Voronoi cell with the mesh to get all fractured chunks

Fracturing with Voronoi Diagram - 3D Mesh

Similar to the 2D situation, but not trivial
- New triangles need to be generated for all fracture surfaces

Generate triangles for all fracture surfaces
- Usually by Delaunay Triangulation(is dual to Voronoi diagram)
- New texture and texture coordinates

Different Fracture Patterns with Voronoi Diagram
- Uniform random pattern
- Clustered pattern
- Radial pattern 
- etc.

Handle destruction after collision
- New rigid bodies may be generated after destruction

Make it more realistic - Fracture is not enough
- Sound effects
- Particle effects
- Navigation updated

Issues with Destruction
- Add destruction with caution
    - Numerous debris may cause larger performance overhead
- Empirical when artists design the destruction effect
    - Many parameters to be tuned, e.g. fracture parameters
    - Produce performance highly depends on the authoring tool

**Vehicle**

Vehicle Mechanism Modeling
- A rigid body actor
    - Shapes for the chassis and wheels
    - Scene query for the suspension simulation

Traction Force
![Traction Force]({{site.baseurl}}/assets/GameEngine/TractionForce.png)

Suspensions Force
![Suspensions Force]({{site.baseurl}}/assets/GameEngine/SuspensionsForce.png)

Tire Forces
![Tire Forces]({{site.baseurl}}/assets/GameEngine/TireForces.png)

Center of Mass 
- Affects handling, acceleration, and traction 
- Should be a tunable value
- Affects the stability of the vehicle in the air
    - front-heavy -> dive
    - rear-heavy -> stabilize
- Affects vehicle steering control
    - front-heavy -> understeering
    - rear-heavy -> oversteering

Weight Transfer
- Affects the maximum traction force per wheel

Steering angles 
- Same steering angle causes slipping
- Ackermann steering

**Advanced Physics : PBD/XPBD**

*Note: only for reference*

Position Based Dynamics PBD - Constraints Projection

![PBD]({{site.baseurl}}/assets/GameEngine/PBD.png)

Advantages of PBD
- Projecting constraints to position corrections
- Fast, stable for most cases
- Hard to control constraint satisfaction
- Cannot prioritize collision constraints over others
- Commonly used for cloth simulation in games
- NVIDIA FleX

Extended Position Based Dynamics (XPBD)
- Use compliances as inverse of constraint stiffness to handle infinite stiffness constraints (rigid body)
- Reintroduce rigid body orientation to XPBD for rigid body simulation application
- Unreal Chaos physics engine

