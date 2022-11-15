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

![PiccoloEditor]({{site.baseurl}}/assets/GameEngineAssignment/Editor.png)

## Color Grading 

In pilot/engine/shader/glsl/color_grading.frag, my first version of implementation is

```cpp
layout(input_attachment_index = 0, set = 0, binding = 0) uniform highp subpassInput in_color;
layout(set = 0, binding = 1) uniform sampler2D color_grading_lut_texture_sampler;
layout(location = 0) out highp vec4 out_color;

void main()
{
    highp ivec2 lut_tex_size = textureSize(color_grading_lut_texture_sampler, 0);
    highp float _COLORS      = float(lut_tex_size.y);
    highp vec4 color         = subpassLoad(in_color).rgba;
    
    highp float cell         = floor(color.b * 15.0); // len, 15 currently
    highp float x_half       = 0.5 / 256.0; // read from center 0.5/width
    highp float y_half       = 0.5 / 16.0; // read from center 0.5/height
    highp vec2 uv            = vec2(
        x_half + color.r / _COLORS * (15.0 / _COLORS) + cell / _COLORS,
        y_half + color.g * (15.0 / _COLORS));
    
    highp vec4 color_sampled = texture(color_grading_lut_texture_sampler, uv);
    out_color = color_sampled;
}
```

The result looks like

![Result 1]({{site.baseurl}}/assets/GameEngineAssignment/Res1.png)

However, this is method did not interpolate the color between blue channel. To achieve this, we do another interpolation between both ceil and floor cells.

```cpp
layout(input_attachment_index = 0, set = 0, binding = 0) uniform highp subpassInput in_color;
layout(set = 0, binding = 1) uniform sampler2D color_grading_lut_texture_sampler;
layout(location = 0) out highp vec4 out_color;

void main()
{
    highp ivec2 lut_tex_size = textureSize(color_grading_lut_texture_sampler, 0);
    highp float _COLORS      = float(lut_tex_size.y);
    highp vec4 color         = subpassLoad(in_color).rgba;
    
    highp float cell         = color.b * 15.0; // for interpoation
    highp float cell_l       = floor(color.b * 15.0); // len, 15 currently
    highp float cell_r       = ceil(color.b * 15.0);
    
    highp float x_half       = 0.5 / 256.0; // read from center 0.5/width
    highp float y_half       = 0.5 / 16.0; // read from center 0.5/height
    
    highp vec2 uv_l          = vec2(
        x_half + color.r / _COLORS * (15.0 / _COLORS) + cell_l / _COLORS,
        y_half + color.g * (15.0 / _COLORS));
    highp vec2 uv_r          = vec2(
        x_half + color.r / _COLORS * (15.0 / _COLORS) + cell_r / _COLORS,
        y_half + color.g * (15.0 / _COLORS));
    
    highp vec4 color_l       = texture(color_grading_lut_texture_sampler, uv_l);
    highp vec4 color_r       = texture(color_grading_lut_texture_sampler, uv_r);
    highp vec4 mid_color     = mix(color_l, color_r, fract(cell));
    
    out_color = mid_color;
}
```

The result now looks like:

![Result 2]({{site.baseurl}}/assets/GameEngineAssignment/Res2.png)

The difference in the cloud is noticeable. The interpolation makes it more natural and smooth.








### Reference

[Grading tutorial](https://defold.com/tutorials/grading/)