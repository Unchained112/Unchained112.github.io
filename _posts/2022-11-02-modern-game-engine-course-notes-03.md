---
title: Modern Game Engine Course Notes 03
tags: [Computer Graphics, Game Engine, Technology]
style: border
color: primary
description: Modern Game Engine - From Getting Started To Practice is a free public course about game engine. This part focus on animation system.
---

Course from: [BoomingTech](https://games104.boomingtech.com/sc/)

## Animation System

### Basics of Animation Technology

Fundamental base: The persistence of vision & Illusory motion (Phi phenomenon)

Challenges in Game Animation

1. Interactive and dynamic animation
    - Vary according to the interaction
    - Cooperate with other gameplay systems
    - Make adjustments in complex environments
    
2. Real-time
    - Compute per frame
    - Massive animation data(Disk and memory)

3. Realism 
    - More vivid expression 
    - More authentic experience 

**2D Animation - Sprite animation**

The electronic equivalent to cel animation
- A sprite is a small bitmap that can be overlaid on top of a background image without disrupting it
- The sequence of frames was designed so that it animates smoothly even when it is repeated indefinitely

Application
- 2D character
    - Sprite on 2D background image
    - Sprite on top of 3D rendered environment
- Game effect
    - Sprite sheet texture for particles

Live2D
A technology to generate 2D animation without 3D model
- Usually refer to eponymous software series employing the technology created by Live2D Ltd.
- Could develop dynamic character, especially anime-style character without a 3D model.

- By applying translation, rotation and transformation to different parts and layers of image.
- Combined with real-time motion capture, could be used for Vtubing.

Make a Live2D animation

Prepare resources
- Dividing origin character image into different parts
- Set "draw order" to each parts for further use

Transform image by using control points for parts
- "ArtMesh" could be automated generated for each parts, which defined by vertices, edges and polygons
- Control points could be used to help transforming "ArtMesh"

Set animation "key frame"
- Set "key frame" to help animation interpolation

**3D Animation Techniques in Games**

DoF(Degrees of Freedom)
- refers to the number of independent variables or parameters of a system
- 6 DoFs per object or sub-part

Rigid Hierarchical Animation
- The earliest approach to 3D character animation
- A character is modeled as a collection of rigid pieces
- The rigid pieces are constrained to one another in a hierarchical fashion

Per-vertex Animation
- Most flexible(3 DoFs per vertex)
- Mostly implemented by Vertex Animation Texture(VAT)
- Suitable for complex morphing
- Need massive data

Morph Target Animation
- A variation on Per-vertex Animation
    - Use key frames with LERP instead of sequence frames(e.g. 30 frames per second)
- Suitable for facial expression
    - Per-Vertex Animation + Key Frame LERP -> Morph Target Animation

3D Skinned Animation
- Mesh(or skin) is bound to the joints of the skeleton
- Each vertex can be weighted to multiple joints

Advantages
- Need less data than per-vertex animation
- Mesh can be animated in a natural way (like human “skin”)

2D Skinned Animation - Derived from 3D skinned animation
- Break up character into various body parts
- Create body part meshes and piece them together
- Rigging, skinning and animation

Physics-based Animation
- Ragdoll
- Cloth and Fluid simulation
- Inverse Kinematics (IK)

Animation Content Creation
- Digital Content Creator + Animator
- Motion Capture

**Skinned Animation Implementation**

1. Create mesh for a binding pose
2. Create a binding skeleton for the mesh
3. “Paint” per-vertices skinning weights to related skeleton
4. Animate skeleton to desired pose
5. Animate skinned vertices by skeleton and skinning weights

Different Spaces
- Local space, Model space and World space

![Different Spaces]({{site.baseurl}}/assets/GameEngine/DifferentSpaces.png)

Skeleton for Creatures - Comprised of a hierarchy of rigid pieces known as joints
- One joint is selected as the root
- Every joint has a parent joint except the root

Joint vs. Bone
- The joints are the objects directly manipulated by the animator to control motion
- The bones are the empty space between the joints

Humanoid Skeleton in Real Game - Number of joints in a humanoid skeleton
- Normal : 50~100 joints
- May more than 300+ joints include facial joints and gameplay joints

Joints for Game Play -Additional joints
- Weapon joint
- Mount joint

Where to Start the Skeleton – Root Joint

Root joint
- The center of the feet
- Convenient to touch the ground

Pelvis joint
- The first child joint of the root joint
- Human upper and lower body separation

Bind Animation for Objects 
- Attach two skeleton's bind point

Bind Pose – T-pose vs. A-pose

The pose of the 3D mesh prior to being bound to the skeleton
- Keep the limbs away from the body and each other, making the process of binding the vertices to the joints easier
- Usually close to natural pose

T-pose vs A-pose
- Shoulders in A-pose are more relaxed
- Easy to deformations in A-pose

Skeleton Pose: A skeleton is posed by transform its joints from the bind pose
- Joint Pose (9 DoFs)
    - Orientation (3 DoFs) 
    - Position (3 DoFs)
    - Scale (3 DoFs)

**Math of 3D Rotation**

![2D Rotation]({{site.baseurl}}/assets/GameEngine/2DRotation.png)

![3D Rotation]({{site.baseurl}}/assets/GameEngine/3DRotation.png)

Euler Angle provides a brief description of 3D rotation and is widely used in many fields.

Problems of Euler Angle
- Gimbal Lock: Gimbal Lock occurs because of the loss of one DoF
- Hard to interpolate: Singularity problem make it hard to interpolate
- Difficult for rotation combination: Rotation combination need rotation matrix
- Hard to rotate by certain axis: Easy to rotate by x,y,z axis but hard to others

Quaternion

Complex Number and 2D Rotation

![2D Rotation with Complex Number]({{site.baseurl}}/assets/GameEngine/Complex2DRot.png)

![Quaternion]({{site.baseurl}}/assets/GameEngine/Quaternion.png)

![Rotation by Quaternion]({{site.baseurl}}/assets/GameEngine/RotQuaternion.png)

![Quaternion to Rotation Matrix]({{site.baseurl}}/assets/GameEngine/RotQuaternionMat.png)

![Quaternion Rotation Math]({{site.baseurl}}/assets/GameEngine/RotQuMath.png)

![Given Axis Rotation by Quaternion]({{site.baseurl}}/assets/GameEngine/RotQuaternionAxis.png)

**Joint Pose**

Joint Pose - Orientation
- Rotation -> Change the Orientation of joints
- Most skeleton poses change orientations of joints only

Joint Pose - Position
- Translate -> change position
- Translate point P to point P' by vector T
- Usually not changed in humanoid skeleton except the pelvis, facial joint and other special joints
- Used for stretching models

Joint Pose - Scale
- Scale -> change the size of the model
- Uniform vs. Non-uniform Scale
- Widely used in facial animation
- Uniform and non-uniform scale facial joints

Joint Pose - Affine Matrix

![Affine Matrix]({{site.baseurl}}/assets/GameEngine/AffineMatrix.png)

Joint Pose Interpolation - Local Space vs. Model Space
- Local Space
    - Less data with delta transform
    - Convenient for interpolation or blend
- Model Space
    - Incorrect for interpolation

Single Joint Skinning - Attach the vertices of a mesh to a posed skeleton
- Each vertex can be bound to one or more joints with a weight parameter
- The vertex position in each bound joint's local space is fixed

Quaternion Interpolation of Rotation
NLERP vs. SLERP
- NLERP
    - Non-constant angular speed
    - Almost constant angular speed when θ is small
- SLERP
    - Constant angular speed
    - May have zero-divide problem when θ is small
- Combination
    - Widely used in 3A-game development
    - Use SLERP when θ is large, and NLERP when θ is almost zero

Simple Animation Runtime Pipeline

![Animation Runtime Pipeline]({{site.baseurl}}/assets/GameEngine/AnimationRuntimePipeline.png)

Catmull-Rom Spline (used for compressing animation)
- Fitting Process
- Make a Catmull-Rom spline with the middle 2 control points at both ends of the original curve
- Iteratively add control points like binary search
- Calculate inner curve by the most closet 4 points
- Repeat until the error of each frame is under the threshold

### Advanced Animation Technology

**Animation Blending**

Math of Blending: LERP
- Use LERP to get intermediate frame from poses of different clips Weight is controlled by game parameters, i.e, character speed

Align Blend Timeline
![Align Blend Timeline]({{site.baseurl}}/assets/GameEngine/AlignBlendTimeline.png)

**Blend Space**

1D Blend Space: Directional Movement

Players can move forward from multiple angles

We can blend any angle from three clips:
- Strafe Left clip
- Run Forward clip
- Strafe Right clip

The technique is called 1D Blend Space.

2D Blend Space:

- Since the movement speed in the lateral direction is lower in the forward direction, the character should enter the running state in a lower speed in the lateral direction. (Delaunay Triangulation)

Skeleton Masked Blending:

![Skeleton Masked Blending]({{site.baseurl}}/assets/GameEngine/SkeletonMaskedBlending.png)

Additive Blending:

- Add a difference clip into a regular clip to produce a new clip.

- Additive Blending introduces a new kind of animation called a difference clip, which represents the difference between two regular animation clips.

- A difference clip can be added into a regular animation clip in order to produce interesting variations in the pose and movement of the character.

- Additive blends are more likely to have abnormal bone results.

**Animation State Machine**

Case: Jumping

- Three clips: Start jump -> In the air -> Land
- Blend Space is synchronous, but jump is stateful
- We usually model the jumping action via a finite state machine, commonly known as the Action State Machine (ASM)

ASM Definition:

- ASM consists of nodes and transitions
- Node types
    - Blend space 
    -  Clip
- Transition type
    - simply “pop” from one state to another
    - cross-fade from one state to the next
    - Special transitional states

Cross Fades: Two common ways 
- Smooth transition
    - restriction: the two clips must be looping animations, and their timelines must be synchronized
- Frozen transition
- Different cross fades curve could be used for different demands

Animation State Machine in Unreal

![Animation State Machine in Unreal]({{site.baseurl}}/assets/GameEngine/UnrealASM.png)

Layered ASM:

- different parts of a character’s body to be doing different, independent or semi- independent actions simultaneously

**Animation Blend Tree**


Blend Tree

- Structure layered ASMs and operations as a tree
    - Inspired by expression tree
    - Easy to understand for animators
- For a blend tree
    - Non-terminal nodes and terminal nodes (leaf nodes)
    - The result of each non-terminal node is a pose

LERP Blend Node

- Binary LERP node
    - Basic non-terminal node in blend tree
    - LERP two input poses with weight β into one output pose

Usually extended to handle multiple inputs (e.g. Ternary/Quad LERP node)

Additive Blend node
- Basic non-terminal node in blend tree
- Add the second input pose (usually a difference one) into the first input pose by weight β

Express Layered ASM in Blend Tree
- Use a blend tree to describe the desired final pose of ASMs

Blend Tree Nodes：

Terminal node (Leaf Nodes)
- Clip
- Blend Space
- ASM

Non-terminal node (No-Leaf Nodes)
- Binary LERP blend node
- Ternary (triangular) LERP blend node
- Binary additive blend node

**Inverse Kinematics (IK)**

Basic Concepts

- End-effector
    - The bone which is expected to be moved to a desired position
- IK (Inverse Kinematics) 
    - The use of kinematic equations to determine the joint parameters of a manipulator so that the end-effector moves to a desired position
- FK (Forward Kinematics) 
    - The use of the kinematics equations of a robot to compute the position of the end-effectors from specified values for the joint parameters

Tone Bones IK
- 3D space
- Determine the final pose by a vector 

Complexity of Multi-Joint IK Solving
- Computation cost: high dimension non-linear function solving in real-time
- May have multiple solutions, unique solution or no solution

Check Reachability of the Target

Constraints of Joints

Heuristics Algorithm

Why? Too many joints + constraints, difficult to solve with analysis method

Basic Idea
- Designed to solve problem in faster and more efficient fashion by sacrificing optimality, accuracy, precision, or completeness for speed
    - Approximation
    - Global optimality is not guaranteed
    - Iteration is usually used with a maximum limit

CCD (Cyclic Coordinate Decent)
- Principle
    - From joint-to-joint, rotates the end-effector as close as possible to the target, solves IK problem in orientation space
- Reachability
    - Algorithm can stop after certain number of iterations to avoid unreachable target problem
- Constraints
    - Angular limits is allowed, by checking after each iteration

Optimized CCD:

1. Add tolerance regions to each bone's goal
    - Each bone stops rotating and moves onto the next bone within tolerance region
    - Helps to produce poses that are less rigid and more comfortable looking
2. Use under-damped angle scaling
    - Each joint moves only a small amount toward the goal and distributes the movement across multiple bones
    - Produce less abrupt joint changes and more smooth and casual poses for character movement

FABRIK (Forward And Backward Reaching Inverse Kinematics) 
- Principle: Instead of orientation space, solves IK problem in position space
- Reachability: Algorithm can stop after certain number of iterations to avoid unreachable target problem
- Cons: Not guarantee convergence

FABRIKF with Constraints: Re-positioning
- Joint restrictions can be enforced at each step by taking the resultant orientation and forcing it to stay with in the valid range

Multiple End-Effectors
- May result in conflict between goals, which can not be achieved simultaneously
- May use a priority or a weighted approach

IK with Multiple End-Effectors
- If a shared bone needs to be moved, the end-effector that is updated last will get priority and the other bones will be pulled away

Other IK Solutions:

Physics-based Method
- More natural
- Usually need lots of computation if no optimization

PBD(Position Based Dynamics)
- Different from traditional physics-based method
- Better visual performance
- Lower computational cost

Fullbody IK in UE5
- XPBD(Extended PBD)

Animation Pipeline with Blending and IK

![Animation Pipeline with Blending and IK]({{site.baseurl}}/assets/GameEngine/AnimaitionPipelineIK.png)

**Facial Animation**

Face is Driven by Complex Muscle System Facial Muscles
- 43 Muscles
- Variant shape, strength and movement
- Work together to make expressions

High Precision Requirements -- Minor change makes difference:
- Voluntary / Forced
- Natural / Intentional
- Sometimes shows quite opposite expressions

Facial Action Coding System (From Movie Industry)
- Facial Action Coding System (FACS) is a system to taxonomize human facial movements by their appearance on the face.
- There are 46 basic movements named action units(AU).
- An expression can be considered as a combination of some of the basic movements.

28 Core Action Units
- Apple Inc. extracted the 28 core AUs
- 23 Symmetric AUs are divided into two basic actions
- Basic actions set varies accoring to the animation production requirements

FACS In Morph Target Animation
- Create AU key poses only store vertexes different from the neutral pose (Additive Blending)

UV Texture Facial Animation
- Using a series of texture maps applied to a simple head shape

**Animation Re-targeting**

Share Animation Among Characters
- Allow animations to be reused between characters (save animator's work)
- Adapt motion captured animations to different characters (reduce the cost)
- Terminology: 
    - Source Character
    - Target Character
    - Source Animation
    - Target Animation

- Ignore Offset Between Source and Target Joints
- Keep Orientation in Different Binding Pose (Relative movement)

Process Tracks -- Handle animation tracks respectively
- Rotation track comes from source animation
    - Keep joint orientation in animation
- Translation track comes from target skeleton
    - Keep the proportion of target skeleton 
- Scale track comes from source animation
    - Keep the scale in animation

Align Movement by Pelvis Height -- The movement of the character
- Usually controlled by displacement curve or motor system at runtime
    - Displacement Curve is extracted from the pelvis pose in animation 
- Needs to be scaled by the proportion of the pelvis

Unresolved Problems of Re-targeting
- Self mesh penetration
- Self contact constrains (eg. the hands when clap)
- The balance of the target character
