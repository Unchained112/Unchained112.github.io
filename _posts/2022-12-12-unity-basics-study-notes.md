---
title: Unity Basic Notes for Interview
tags: [Game Development, Unity]
style: fill
color: secondary
description: Basic Unity notes used as a structure to review for interview.
---

### GameObject

Unity’s GameObject class represents anything which can exist in a Scene.

GameObjects are the **fundamental objects** in Unity that represent characters, props and scenery. They do not accomplish much in themselves but they act as containers for Components, which implement the functionality.

To give a GameObject the properties it needs to become a light, or a tree, or a camera, you need to **add components** to it. Depending on what kind of object you want to create, you add different combinations of components to a GameObject.


### MonoBehaviour

The MonoBehaviour class is the **base class** from which every Unity script derives, by default. 

The MonoBehaviour class provides the framework which allows you to attach your script to a GameObject in the editor, as well as providing hooks into useful Events such as Start and Update.

The diagram below summarizes how Unity orders and repeats event functions over a script’s lifetime.

![Monobehaviour Flowchart]({{site.baseurl}}/assets/UnityCSharp/MonobehaviourFlowchart.svg)

- Coroutines: A coroutine allows you to **spread tasks across several frames**. In Unity, a coroutine is a method that can pause execution and return control to Unity but then continue where it left off on the following frame. However, it’s important to remember that coroutines **aren’t threads**. Synchronous operations that run within a coroutine still **execute on the main thread**.

- Event: The MonoBehaviour class provides access to a large collection of event messages, which allows you to execute your code based on what is currently happening in your project.

- Difference between `Awake()` and `Start()`: Awake is called first and, unlike Start, will be called even if the script component is disabled. Using Start and Awake together is useful for separating initialization tasks into two steps. For example, a script’s self-initialization (e.g. creating component references and initializing variables) can be done in Awake before another script attempts to access and use that data in Start, avoiding errors. Besides, Start can be called as a coroutine.

*I make a metaphor, the Unity Engine gives the developer an empty rail tracks. GameObjects are those trains that could run on those tracks. To build your own tracks, the MonoBehaviour base class gives you a frame of a train, which allows you easily fill it up to build your own trains.*


### Prefab

Unity’s Prefab system allows you to create, configure, and **store a GameObject** complete with all its components, property values, and child GameObjects as a **reusable Asset**. The Prefab Asset acts as a template from which you can create new Prefab instances in the Scene. You can override settings on individual prefab instances if you want some instances of a Prefab to differ from others. You can also create variants of Prefabs which allow you to group a set of overrides together into a meaningful variation of a Prefab. You should also **use Prefabs** when you want to instantiate GameObjects at **runtime** that did **not exist** in your Scene at the start.

Some common examples of Prefab use include:
- Environmental Assets - for example a certain type of tree used multiple times around a level (as seen in the screenshot above).
- Non-player characters (NPCs) - for example a certain type of robot may appear in your game multiple times, across multiple levels. They may differ (using overrides) in the speed they move, or the sound they make.
- Projectiles - for example a pirate’s cannon might instantiate a cannonball Prefab each time it is fired.
- The player’s main character - the player prefab might be placed at the starting point on each level (separate Scenes) of your game.


### Unity Built-in Render Pipeline

![Unity Built-in Render Pipeline]({{site.baseurl}}/assets/UnityCSharp/Unity-Built-In-Render-Pipeline.png)

### Possible Questions

Can threads be used to modify a Texture on runtime?
- No, Unity's renderer runs on the main thread. 

Can threads be used to move a GameObject on the scene?
- No. Fetching the Transform reference isn’t thread safe in Unity.

Explain why deferred lighting optimizes scenes with a lot of lights and elements.
- Deferred Lighting makes the scene render all pixels without illumination (which is fast), and with extra information (at a cost of low overhead), it calculates the illumination step only for the **pixels of the screen buffer** (which is less than all pixels processed for each element). 

Explain why `Time.deltaTime` should be used to make things that depend on time operate correctly.
- Real time applications, such as games, have a variable FPS. They sometimes run at 60FPS, or when suffering slowdowns, they will run on 40FPS or less. Then for example, for a transformation within a fixed time (1s), `Time.deltaTime` allows you to achieve this without worrying the changing FPS.

Can two GameObjects, each with only an SphereCollider, both set as trigger and raise OnTrigger events? 
- No. Collision events between two objects can only be raised when one of them has a `RigidBody` attached to it. This is a common error when implementing applications that use “physics.”

Which of the following examples will run faster?
1. 1000 `GameObjects`, each with a `MonoBehaviour` implementing the `Update` call back.
2. One `GameObject` with one `MonoBehaviour` with an Array of 1000 classes, each implementing a custom `Update()` callback.
-  Example 2 is faster. The `Update` callback is called using a C# Reflection, which is significantly slower than calling a function directly. In our example, 1000 GameObjects each with a `MonoBehaviour` means 1000 Reflection calls per frame. Creating one `MonoBehaviour` with one `Update`, and using this single callback to `Update` a given number of elements, is a lot faster, due to the direct access to the method.

Explain, in a few words, what roles the inspector, project and hierarchy panels in the Unity editor have. Which is responsible for referencing the content that will be included in the build process?
- The inspector panel allows users to modify numeric values (such as position, rotation and scale), drag and drop references of scene objects (like Prefabs, Materials and Game Objects), and others. Also it can show a custom-made UI, created by the user, by using Editor scripts.
- The project panel contains files from the file system of the assets folder in the project’s root folder. It shows all the available scripts, textures, materials and shaders available for use in the project.
- The hierarchy panel shows the current scene structure, with its GameObjects and its children. It also helps users organize them by name and order relative to the GameObject’s siblings. Order dependent features, such as UI, make use of this categorization.
- The panel responsible for **referencing content** in the build process is the **hierarchy panel**. The panel contains references to the objects that exist, or will exist, when the application is executed. When building the project, Unity searches for them in the project panel, and adds them to the bundle.

Explain the issue with the code below and provide an alternative implementation that would correct the problem.
```c#
using UnityEngine;
using System.Collections;

public class TEST : MonoBehaviour {
    void Start () {
        transform.position.x = 10;
    }
}
```

- The issue is that **you can’t modify the position from a transform directly**. This is because the position is actually a **property (not a field)**. Therefore, when a getter is called, it invokes a method which returns a Vector3 copy which it places into the stack. So basically what you are doing in the code above is assigning a member of the struct a value that is in the stack and that is later removed. Correct way:

```c#
using UnityEngine;
using System.Collections;

public class TEST : MonoBehaviour {
   void Start () {
        transform.position = new Vector3(10, transform.position.y, transform.position.z);
    }
}
```

### Reference

1. [Unity Documentation](https://docs.unity3d.com/Manual/)
2. [12 Essential Unity or Unity3D Interview Questions](https://www.toptal.com/unity-unity3d/interview-questions)