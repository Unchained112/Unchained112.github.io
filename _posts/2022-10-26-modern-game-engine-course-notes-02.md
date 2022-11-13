---
title: Modern Game Engine Course Notes 02
tags: [Computer Graphics, Game Engine, Technology]
style: border
color: primary
description: Modern Game Engine - From Getting Started To Practice is a free public course about game engine. This part focus on rendering techniques.
---

Course from: [BoomingTech](https://games104.boomingtech.com/sc/)

## Rendering on Game Engine

A heavily optimized practical software framework to fulfill the critical rendering requirements of games on modern hardware (PC, console and mobiles).

![Outline of Rendering]({{site.baseurl}}/assets/GameEngine/OutlineofRendering.png)

### Building Blocks of Rendering

Pipeline and data: Vertex Data -> Triangle Data -> Material Parameters -> Textures
- Projection Transform and Rasterization
- Shading
- Texture Sampling

### Understand the Hardware 

SIMD and SIMT 
Single Instruction Multiple Data
Single Instruction Multiple Threads

GPU Architecture

![GPU Arch]({{site.baseurl}}/assets/GameEngine/GPUArch.png)

Data Flow from CPU to GPU

- CPU and Main Memory: Data load / unload; Data preparation
- CPU to GPU: High latency / Limited Bandwidth
- GPU and Video Memory: High Performance Parallel Rendering

*Try not to read memory from GPU*

### GPU Bounds and Performance

Application performance is limited by:
- Memory bounds
- ALU bounds
- TMU (Texture Mapping Unit) bounds
- BW ( Bandwidth) bounds

### Renderable 

Game object - Components - Mesh Render Component.

Vertex and Index Buffer: Vertex data is accessed through index.

Materials: determine the appearance of objects, and how objects interact with light.
- Phong Model
- PBR Model
- Subsurface Material

Various Textures in Materials: Albedo, Normal, Metallic, Roughness, AO.

Variety of Shaders

### Render Objects in Engine

Model assets are made based on local coordinate systems, and eventually are rendered in screen space through transformation and projection.

Display different textures on a single model with submesh!

Resource pool: Instance - use handle to reuse resources

**Definition and Instance, vital concept for game engine!**

Within a scene, using resource pools, all the materials are sorted. 

GPU Batch Rendering. 

### Visibility Culling

For each view, there are a lot of objects which are not needed to be rendered.

Using the simplest bound to create culling (BVH, PVS Potential Visibility Set).

Construction and Insertion of BVH in Game Engine

### Texture Compression

Traditional image compression like JPG and PNG
- Good compression rates
- Image quality
- Designed to compress or decompress an entire image

In game texture compression
- Decoding speed
- Random access
- Compression rate and visual quality - Encoding speed

Common block-based compression format
- On PC, BC7 (modern) or DXTC (old) formats
- On mobile, ASTC (modern) or ETC / PVRTC (old) formats

### Cluster-Based Mesh Pipeline

Nanite:
- Hierarchical LOD clusters with seamless boundary
- Don’t need hardware support, but using a hierarchical cluster culling on the precomputed BVH tree by persistent threads (CS) on GPU instead of task shader

## Lighting, Materials and Shaders

Lighting
- Photon emit, bounce, absorb and perception is the origin of everything in rendering.

Material
- How matter react to photon

Shader
- How to train and organize those micro-slaves to finish such a vast and dirty computation job between photon and materials

The Rendering Equation
![Rendering Equation]({{site.baseurl}}/assets/GameEngine/RenderingEq.png)

The 1st Challenge: How to get incoming radiance for any given incoming direction.
- 1a Visibility to Lights
- 1b Light Source Complexity (point light, direction light)

The 2nd Challenge: Integral of lighting and scatting function on hemisphere is expensive.
![2nd Challenge]({{site.baseurl}}/assets/GameEngine/2ndChallenge.png)

The 3rd Challenge: To evaluate incoming radiance, we have to compute yet another integral, i.e. rendering equation is recursive.
![3rd Challenge]({{site.baseurl}}/assets/GameEngine/3rdChallenge.png)

### Blinn-Phong Model: Simple and Useful

Ambient + Diffuse + Specular = Blinn-Phong Reflection

`L = k_a * I_a + k_d * (I / r^2) + k_s * * (I / r^2) * max(0, dot(N, H))^2`

Problem of Blinn-Phong: 
- The energy is not conservative: the reflected energy should not be greater than incoming energy; some materials cannot be properly modeled by artists

### Shadow

Already obsolete method: planar shadow, shadow volume, projective texture.

Shadow Map:
![Shadow Map]({{site.baseurl}}/assets/GameEngine/ShadowMap.png)

### Pre-computed Global Illumination

Lightmap:
- Pros
    - Very efficient on runtime
    - Bake a lot of fine details of GI on environment
- Cons
    - Long and expensive precomputation (lightmap farm) 
    - Only can handle static scene and static light
    - Storage cost on package and GPU

Light Probes + Reflection Probes
- Pros
    - Very efficient on runtime
    - Can be applied to both static and dynamic objects
    - Handle both diffuse and specular shading
- Cons
    - A bunch of SH light probes need some precomputation
    - Can not handle fine detail of GI. I.e, soft shadow on overlapped structures

### Physical-Based Material

BRDF Model Based on Microfacet
![BRDF]({{site.baseurl}}/assets/GameEngine/BRDF.png)

Disney Principled BRDF

Principles to follow when implementing model:
- Intuitive rather than physical parameters should be used
- There should be as few parameters as possible
- Parameters should be zero to one over their plausible range
- Parameters should be allowed to be pushed beyond their plausible range where it makes sense
- All combinations of parameters should be as robust and plausible as possible

PBR Specular Glossiness
![BRDF]({{site.baseurl}}/assets/GameEngine/PBR_SG.png)

PBR Metallic Roughness

**Two most important models for learners**

To find out more details by yourself: Image-Based Lighting (IBL), Cascade Shadow.

Summary of Popular AAA Rendering
- Lightmap + Light probe
- PBR + IBL
- Cascade shadow + VSSM

### The Challenges and Fun of Rendering the Beautiful Mother Nature

Environment Components in Games: 

- Sky and Cloud
- Vegetation
- Terrain

**Terrain**

- Simple method: Heightfield - Mesh grids
- Adaptive Mesh Tessellation
- Two Golden Rules of Optimization
    - View-dependent error bound: Distance to camera FoV; Error compare to ground truth (pre-computation)
- Triangle-based Subdivision 
    - Subdivision and T-Junctions: Continuously partitioning triangles and their children based on the idea of binary trees
- QuadTree-Based Subdivision (used more common in the industry)
    - Pros: Easy to construct; Easy management of data under geospatial, including objects culling and data streaming
    - Cons: Mesh subdivision is not as flexible as triangle mesh; The grid level of the leaf nodes needs to beconsistent
    - Solve T-Junctions among Quad Grids: Mesh LoD Stitching
- Triangulate Irregular Network (TIN)
- Real-Time Deformable Terrain

- Non-Heightfield Terrain
- Dig a Hole in Terrain: Cull a terrain vertex by outputting NaN from the vertex shader
- Crazy Idea - Volumetric Representation: Marching Cubes 

- Paint Terrain Material
- Advanced Texture Splatting: Blending with Height (add bias)
- Sampling from Material Texture Array
- Parallax and Displacement Mapping

- Virtual Texture
    - Build a virtual indexed texture to represent all blended terrain materials for whole scene
    - Only load materials data of tiles based on view- depend LOD
    - Pre-bake materials blending into tile and store them into physical textures
- VT Implementation, DirectStorage & DMA
- Floating-point Precision Error
- Camera-Relative Rendering
    - Translates objects by the negated world space camera position before any other geometric transformations affect them
    - It then sets the world space camera position to 0 and modifies all relevant matrices accordingly

**Vegetation**

- Tree Rendering: Tree LOD: From mesh to billboard
- Decorator Rendering
- Road and Decals Rendering:
    - Spline-based Roda Editing and Sculpturing Height Field
    - Splatting Road and Decals on Virtual Texture

**Sky**

![Analytic Atmosphere Appearance Modeling]({{site.baseurl}}/assets/GameEngine/AnaylticSky.png)

Participating Media
- Volume filled with particles
- Interact differently with light depending on its composition

![Participating Media]({{site.baseurl}}/assets/GameEngine/ParticipatingMedia.png)

![Volume Rendering Equation]({{site.baseurl}}/assets/GameEngine/VRE.png)

Real Physics in Atmosphere

Scattering Types
- Rayleigh Scattering: Scattering of light by particles that have a diameter much smaller than the wavelength of the radiation (eg. air molecules).
- Mie scattering: Scattering of light by particles that have a diameter similar to or larger than the wavelength of the incident light (eg. aerosols).

Variant Air Molecules Absorption
- Ozone (O3): Absorb strongly at longer wavelengths to filter out the reds, oranges, yellows
- Methane (CH4): Well-known for absorbing red light

Ray Marching
- Ray marching is a popular method to integrate function along a path
- We use ray marching to calculate final radiance for a given point by single scattering
- The integrated radiance is usually stored in look-up tables (LUT)

Precomputed Atmospheric Scattering

Those knowledge is too complex for me to understand, thus I just link the reference for further reading:

- [https://ebruneton.github.io/precomputed_atmospheric_scattering/](https://ebruneton.github.io/precomputed_atmospheric_scattering/)
- [A Scalable and Production Ready Sky and Atmosphere Rendering Technique](https://diglib.eg.org/bitstream/handle/10.1111/cgf14050/v39i4pp013-022.pdf)

**Cloud**

Billboard Cloud

- Pros
    - Efficient
- Cons
    - Limited visual effect
    - Limited cloud type

Volumetric Cloud Modeling

- Pros
    - Realistic cloud shapes
    - Large scale clouds possible
    - Dynamic weather supported
    - Dynamic volumetric lighting and shadowing
- Cons
    - Efficiency must be considered

Noise Function

- Perlin Noise
- Worley Noise: Voronoi

Cloud Density Model

![Ray Marching Cloud]({{site.baseurl}}/assets/GameEngine/RayMarchingCloud.png)


## Render Pipeline, Post-process and Everything

### Ambient Occlusion

![Ambient Occlusion]({{site.baseurl}}/assets/GameEngine/AO.png)

Precomputed AO: Using ray tracing to compute the AO offline and store the result into texture, which is widely used in object modeling process
- Extra storage cost
- Only apply to static object

Screen Space Ambient Occlusion (SSAO)
- Generate N random samples in a sphere around each pixel p in view space
- Test sample occlusions by comparing depth against depth buffer
- Average visibility of sample points to approximate AO

SSAO+
- Recall the AO equation is acutally done on the normal-oriented hemisphere

HBAO Horizon-based Ambient Occlusion
- Use the depth buffer as a heightfield on 2D surface
- Rays that below the horizon angle are occluded
- Trace rays directly in 2D and approximate AO from horizon angle

GTAO - Ground Truth-based Ambient Occlusion
- GTAO introduces the missing cosine factor, removes the attenuation function, and add a fast approximation of multi bounce
- Add multiple bounces by fitting a cubic polynomial per albedo

Ray-Tracing Ambient Occlusion
- Casting rays from each screen pixel using RTT hardware 
    - 1 spp(sample per-pixel) works well for far-field occlusion
    - With 2-4 spp, can recover detailed occlusion in contact region

### Fog

Depth Fog
- Linear fog: `factor = (end-z)/(end-start)`
- Exp fog: `factor = exp(- density*z)`
- Exp Squared fog: `factor = exp(- (density*z)^2)`
 
Height Fog
![Height Fog]({{site.baseurl}}/assets/GameEngine/HeightFog.png)

Voxel-based Volumetric Fog
- Clip space volumes
    1. Material properties <= (Input data: Participating media entities)
    2. Froxel light scattering <= (Input data: Lighting and shadow information)
    3. Final integration

### Anti-aliasing

Reason of Aliasing
- Aliasing is a series of rendering artifact which is caused by high-frequency signal vs. insufficient sampling of limited rendering resolutions

Anti-aliasing
- The general strategy of screen-based antialiasing schemes is using a sampling pattern to get more samples and then weight and sum samples to produce a pixel color
 
Super-sample AA (SSAA) and Multi-sample AA (MSAA)
- SSAA, 4x rendering resolution 4x z-buffer and framebuffer, 4x rasterization and pixel shading
- MSAA, only multi-sampling necessary pixels 4x z-buffer and framebuffer, 4x rasterization and 1+ x pixel shading

FXAA (Fast Approximate Anti-aliasing)
- Anti-aliasing based on 1x rendered image
    - Find edge pixels by luminance
    - Compute offset for every edge pixel
    - Re-sample edge pixel by its offset to blend with a neighbor

TAA (Temporal Anti-aliasing)
- Utilize spatial-temporal filtering methods to improve AA stability in motion

### Post-process

Post-process in 3D Graphics refers to any algorithm that will be applied to the final image. It can be done for stylistic reasons (color correction, contrast, etc.) or for realistic reasons (tone mapping, depth of field, etc.)

What is Bloom
- The physical basis of bloom is that, in the real world, lenses can never focus perfectly
- Even a perfect lens will convolve the incoming image with an Airy disk

Bloom
1. Detect Bright Area by Threshold
2. Gaussian Blur / Pyramid Gaussian Blur
3. Bloom Composite

### Tone Mapping

- No way to directly display HDR image in a SDR device
- The purpose of the Tone Mapping function is to map the wide range of high dynamic range (HDR) colors into standard dynamic range (SDR) that a display can output

![Filmic Curve]({{site.baseurl}}/assets/GameEngine/FilmicCurve.png)

ACES Academy Color Encoding System
- Primarily for Film & Animation
- Interesting paradigms and transformations

The useful bits
- Applying Color Grading in HDR is good
- The idea of a fixed pipeline up to the final OTD transforms stage is good
- Separates artistic intent from the mechanics of supporting different devices

HDR and SDR Pipeline
- Visual consistency between HDR / SDR
- Similar SDR results to previous SDR color pipeline
- High quality
- High performance
- Minimal disruption to art teams
    - Simple transition from current color pipeline
    - Minimal additional overhead for mastering HDR and SDR

### Color Grading

Lookup Table (LUT)
- LUT is used to remap the input color values of source pixels to new output values based on data contained within the LUT
- A LUT can be considered as a kind of color preset that can be applied to image or footage

### Rendering Pipeline

Rendering pipeline is the management order of all rendering operation execution and resource allocation

Forward rendering

```
for n meshes 
    for m lights
        color += shading(mesh, light)
```

Sort and Render Transparent after Opaque Objects

Deferred Rendering

![Deferred Rendering]({{site.baseurl}}/assets/GameEngine/DeferredRendering.png)

- Pros
    - Lighting is only computed for visible fragments
    - The data from the G-Buffer can be used for post-
    - processing
- Cons
    - High memory and bandwidth cost
    - Not supporting transparent object
    - Not friendly to MSAA

Advanced rendering techniques for further reading:
- Tile-based Rendering (used in mobile devices)
- Forward+ (Tile-based Forward) Rendering
- Cluster-based Rendering
- Visibility Buffer (more triangle faces than pixels)

Challenges
- Complex parallel work needs to synchronize with complex resource dependency 
- Large amount of transient resource whose lifetime is shorter than one frame 
- Complex resource state management
- Exploit newly exposed GPU features without extensive user low level knowledge

Advanced solutions for further reading
- Frame Graph: A Directed Acyclic Graph (DAG) of pass and resource dependency in a frame, not a real visual graph

Render to monitor
- V-Sync technology
- Variable Refresh Rate

