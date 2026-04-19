# Custom Effect

{% hint style="warning" %}
Work in progress, the feature is not part of main branch!

You can download binaries with the test version of the feature in the release page of the openmobu repository

[Concept of custom effect video](https://youtu.be/muRfrkq16Bc)
{% endhint %}

Custom effect gives you a way to make a new effect for the post processing system. The effect is based on fragment glsl shaders.

{% hint style="info" %}
There is a big collection of shader examples at [shadertoy](https://shadertoy.com/) resource

Below you can find a topic on how to adapt a shadertoy shader
{% endhint %}



## Assets

* User Effect
* pre-defined effects: Blur Shader and Mix Shader

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### User Effect

In a user effect, you can define a shader file as a fragment GLSL shader to process the full-screen image. The file path can be specified relative to the folder where the scene is saved, or relative to the plugins folder. For example, _GLSL/simple130.glslv_ is a file distributed with the plugins as a default vertex shader, and in most cases it should be left unchanged.

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

**Export Shader Scheme** is a debug option that processes the shader and outputs its uniforms to a text file in json format.

**Number of Passes** defines how many times the image is processed using the provided shader. On each iteration, a system uniform `iPass` represents the current iteration index.

**Resolution** can be used to downsample the processed image. For certain effects, such as blur, processing the full-resolution image is unnecessary; using a reduced resolution (e.g., half size) is often sufficient and more efficient.

### Blur Effect

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

This is a built-in custom effect that can be part of a custom effects processing chain. It provides several additional options:

**Input Texture** – If left empty, the full-screen image from the previous effect is used. If a scene texture is specified, the video from that texture is used as the input. If you assign another effect or custom effect object here, it will be used as the input for this blur effect, forming a processing chain. The input effect (and any of its dependencies) is processed first, followed by the current effect.

**Blur Scale** – Defines the scale factor of the applied blur.

### Mix Effect

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

This effect is used to mix two input images. It provides two input parameters: **Input Texture** and **Second Texture**.

For each input, you can specify:

* **Empty** – uses the previous texture from the post-processing chain
* **Scene texture** – uses a video/image texture as input
* **Another effect** – uses the result of a processed effect as input for that channel

**Bloom** is an optional mode, and **Inverse** modifies how the two images are combined.

By default, the two textures are combined using multiplication. When **Inverse** is enabled, the result is computed by multiplying the inverse of the first texture with the second texture.

When **Bloom** is enabled, the mixing changes to an additive model: the first texture is multiplied by a bloom tone, and the second texture is multiplied by a bloom stretch, and the results are then added together.

### Examples

You can find example scenes in the openmobu repositiory / MB\_Scenes folder

<figure><img src="../../../.gitbook/assets/customeffect_originalImage.jpg" alt=""><figcaption><p>original scene without post processing</p></figcaption></figure>

<div><figure><img src="../../../.gitbook/assets/customeffect_fxaa.jpg" alt=""><figcaption><p>fxaa effect</p></figcaption></figure> <figure><img src="../../../.gitbook/assets/customeffect_godrays.jpg" alt=""><figcaption><p>god rays effect</p></figcaption></figure> <figure><img src="../../../.gitbook/assets/customeffect_sketchy.jpg" alt=""><figcaption><p>sketchy effect</p></figcaption></figure> <figure><img src="../../../.gitbook/assets/customeffect_sketchy2.jpg" alt=""><figcaption><p>sketchy effect 2</p></figcaption></figure></div>



## Uniforms

A GLSL shader contains **uniforms** that define inputs provided either by the user or by the system (via predefined uniform names). These uniforms are parsed into a shader scheme, and the corresponding object properties are created automatically.

For `sampler2D` uniforms, which represent bound textures, an object list property is exposed. There are several ways to define the input texture:

* **Leave it empty** – the texture from the previously processed effect in the post-processing chain will be used. For this purpose, you can use predefined system names such as `inputSampler` or `iChannel0`.
* **Attach a scene texture** – the video/image from that texture will be bound as the input to the shader.
* **Attach another effect object** – the result of that effect’s processing will be used as the input texture for the shader.

### System Uniforms

| name               | declaration                           | description                                                                                                                                                                                                                                                  |
| ------------------ | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| inputSampler       | uniform sampler2D inputSampler;       | this is an input image from the processing chain. The image of all previous applied effects during frame render.                                                                                                                                             |
| iChannel0          |                                       | same as inputSampler                                                                                                                                                                                                                                         |
| depthSampler       | uniform sampler2D depthSampler;       | this is a scene depth texture sampler, based on scene raw depth information                                                                                                                                                                                  |
| linearDepthSampler | uniform sampler2D linearDepthSampler; | a depth texture converted into a linear space [https://web.archive.org/web/20130416194336/http://olivers.posterous.com/linear-depth-in-glsl-for-real](https://web.archive.org/web/20130416194336/http://olivers.posterous.com/linear-depth-in-glsl-for-real) |
| maskSampler        | uniform sampler2D maskSampler;        | binded mask texture for a shader processing. mask is defined per post processing object                                                                                                                                                                      |
| normalSampler      | uniform sampler2D normalSampler;      | not supported yet                                                                                                                                                                                                                                            |
| useMasking         | uniform float useMasking;             | float uniform \[0; 1] to define if the mask have to be used                                                                                                                                                                                                  |
| upperClip          | uniform float upperClip;              | this is an upper clip image level. defined in a texture coord space to skip processing                                                                                                                                                                       |
| lowerClip          | uniform float lowerClip;              | this is a lower clip image level. defined in a texture coord space to skip processing                                                                                                                                                                        |
| gResolution        | uniform vec2 gResolution;             | vec2 that contains processing absolute resolution, like 1920x1080                                                                                                                                                                                            |
| iResolution        |                                       | same as gResolution                                                                                                                                                                                                                                          |
| uInvResolution     | uniform vec2 uInvResolution;          | 1.0 / iResolution.xy                                                                                                                                                                                                                                         |
| texelSize          | uniform vec2 texelSize;               | vec2 of a texel size, computed as 1/resolution                                                                                                                                                                                                               |
| iTime              | uniform float iTime;                  | compatible with shadertoy, float, shader playback time (in seconds)                                                                                                                                                                                          |
| iDate              | uniform vec4 iDate;                   | compatible with shadertoy, vec4, (year, month, day, time in seconds)                                                                                                                                                                                         |
| cameraPosition     | uniform vec3 cameraPosition;          | current camera position in world space                                                                                                                                                                                                                       |
| modelView          | uniform mat4 modelView;               | current camera modelview matrix                                                                                                                                                                                                                              |
| projection         | uniform mat4 projection;              | current camera projection matrix                                                                                                                                                                                                                             |
| modelViewProj      | uniform mat4 modelViewProj;           | current camera modelview-projection matrix                                                                                                                                                                                                                   |
| invModelViewProj   |                                       |                                                                                                                                                                                                                                                              |
| prevModelViewProj  |                                       | modelview-projection matrix from a previous frame (used in motion blur)                                                                                                                                                                                      |
| zNear              |                                       | camera near plane                                                                                                                                                                                                                                            |
| zFar               |                                       | camera far plane                                                                                                                                                                                                                                             |



### Uniform Naming Rules

During compilation the shader effect is going to pick the system uniforms and user uniforms. For user uniforms is going to make object properties, so that user could customize the input values for the shader render.

There is a way to customize the ui elements based on a given special postfix to a uniform name

| Uniform type | Postfix  | Description                                                                                      |
| ------------ | -------- | ------------------------------------------------------------------------------------------------ |
| float        | \_slider | make a UI with clamped range \[0; 100] and convert it into \[0; 1] as output                     |
| float        | \_flag   | make a checkbox and cast the on/off flag to a float \[0; 1]                                      |
| vec2         | \_wstoss | make a vector3 ui element and convert value from a world space into vec2 uniform in screen space |
| vec3         | \_color  | make a color RGB picker and output as vec3                                                       |
| vec4         | \_color  | make a color RGBA picker and output as vec4                                                      |





## Shadertoy shaders and adaptation for a custom effect

### ShaderToy compatibility mode

<figure><img src="../../../.gitbook/assets/Снимок экрана_20260111_132017.png" alt=""><figcaption></figcaption></figure>

Turn on the correspondent flag in your user effect shader object to make a header and footer to support a code from shader toy effect.



### Steps to manually adjust your shader

First of all, we have to add a header information about shader version and input, output values. In our case the input value is vec2 TexCoord which represents a processed texel position in uv space \[0; 1] and output is vec4 FragColor which is our computed texel color.

```
#version 140

in vec2 TexCoord;
out vec4 FragColor;
```

The main entry function have to be renamed from&#x20;

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
```

into

```
void main(void)
```

The output variable to store a texel color is called FragColor with capital letter while shadertoy shader has fragColor with a lower case.

The input TexCoord is in uv space, while shadertoy shader operates with fragCoord in image space. In order to convert from uv space into image space, we have to multiply our tex coord by image resolution.

```
vec2 fragCoord = TexCoord * iResolution.xy;
```

where iResolution is system uniform that have to be defined in the top of the shader

```
uniform vec2 iResolution;
```

For shaders that require buffers, you need to create each buffer as a separate GLSL fragment shader and use them to compose an effect shader. Then, connect these shaders together in the execution chain. When you have a `sampler2D` uniform, it will be exposed as a list object property in MotionBuilder, allowing another shader or texture to be used to compute that input.



