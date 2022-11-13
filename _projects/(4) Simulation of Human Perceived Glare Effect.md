---
name:  Simulation of Human Perceived Glare Effect
tools: [Image Processing, Computer Graphics, C++]
image: https://imgur.com/fG78Rqu.png
description: This is a project aims to simulate the human perceived glare effects given an image based on the biological and physical structure of human eyes.
---

## Foreword

To be honest, this is a late summary almost one year after I finished this project one year later. Since currently I am building up my personal website, I think it is a good time to revise it and see what I have learned through the project. This is a bachelor's thesis project in cooperation with Hasco Vision Technology and I worked as a team leader for this project under the supervision of a manager at Hasco Vision car light engineering team. Although it is not quite related to what I am perusing, I did work hard to solve the topic as a software engineer.

## Problem Statement

The glare effect is an illusion when a bright region appears self-luminous. One typical situation is when you are walking at night, you could be easily affected by a car's light that is driving towards you. This makes it a problem that our sponsor, Hasco Vision, wants to consider when designing a car light. To address this problem, we aim to simulate the process and generate the glare effect given an image.

![Car Light Glare](https://imgur.com/NISc3VM.png)

## Concept Generation

To start with, we think of the problem from two perspectives. On the one hand, we hope to simulate the procedure of the lighting passing through the human eyes, which requires a certain level of modeling of the human eye structure. On the other hand, if we want to implement such an effect practically, we could look at the problem from a computer graphics perspective. How do modern AAA games achieve a similar effect? 

By reviewing previous research, we have an overall idea of the solution. In computer graphics, generating blooming could be done in three procedures: detect the light area with a threshold; use gaussian blur to produce the bloom from the light area; add the bloom back to the image. Biologically, the scattering in the cornea, lens, and retina most contribute to the observed glare effect. Besides, the scattering of light could be modeled by a function called Point Spread Function. 

![Eye Structure](https://imgur.com/wIEowF9.png)

## Design Description

The algorithm works as follows.
1. Generate the Point Spread Function by multiplying modeled aperture image with a Fresnel term representing the scattering of light.  

![Generate PFS]({{site.baseurl}}/assets/Project4/Ag1.png)

2. Detect the light area with a threshold and get the light part image. This can be done by doing the Fourier Transform on the input image and filtering out the low-frequency part. (Our sponsor directly provide the car light image, so we didn't need this) 
3. Convolve the light part image (in grayscale) with the Point Spread Function to get the glare image. The convolution is done by multiplying two Fourier-Transformed images.

![Convolution]({{site.baseurl}}/assets/Project4/Ag2.png)

4. Adjust the glare image that matches the corresponding light frequency, and composite it with the original image.

![Composition]({{site.baseurl}}/assets/Project4/Ag3.png)

## Reference

1. [Learn OpenGL Tutorial: Bloom](https://learnopengl.com/Advanced-Lighting/Bloom)
2. Tobias Ritschel. Temporal Glare: Real-Time Dynamic Simulation of the Scattering in the Human Eye. Proceedings Eurographics 2009, Munich 30 March—3 April 2009.