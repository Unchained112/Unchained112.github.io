---
title: Modern Game Engine Assignment
tags: [Computer Graphics, Game Engine, Technology]
style: border
color: primary
description: Modern Game Engine - Assignment Report (just for myself).
---

## Install Piccolo Game Engine

[GitHub Repo for Piccolo Game Engine](https://github.com/BoomingTech/Piccolo)

Firstly, Following the instructions to set up the project for the device platform. 

In my case, I am using a MacBook Air M1. Everything is supposed to be fine when running 
```
cmake -S . -B build -G "Xcode"
```
However, my case report CMake error for no CMAKE_C_COMPILER and CMAKE_CXX_COMPILER. Command `sudo xcode-select --reset` is not working here. To solve it, I needed to first run 
```
xcrun -find c++
xcrun -find cc
```
Then, run 
```
cmake -D CMAKE_C_COMPILER="/path/to/your/c/compiler/executable" -D CMAKE_CXX_COMPILER="/path/to/your/cpp/compiler/executable" -S . -B build -G "Xcode"
```

Now, the project is ready to be open in Xcode. Here, there are also two settings one should pay attention:

1. Set the default C++ complier to be C++17 since this project utilized some new features.

![Compiler Version]({{site.baseurl}}/assets/GameEngineAssignment/CompilerVersion.png)

2. Set the PiccoloEditor project as the Startup project for building.

![PiccoloEditor]({{site.baseurl}}/assets/GameEngineAssignment/PiccoloEditor.png)

Now, the editor should looks like:



## Color Grading 