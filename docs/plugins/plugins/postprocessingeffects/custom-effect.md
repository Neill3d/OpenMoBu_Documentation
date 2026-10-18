# Custom Effect

{% hint style="info" %}
[Concept of custom effect video](https://youtu.be/muRfrkq16Bc)
{% endhint %}

Custom effect gives you a way to make a new effect for the post processing system. The effect is based on fragment GLSL shaders. A custom effect can be used in two ways:

* as an effect in the post processing chain (**User Effect**), processing the full-screen image of a camera view
* as a texture (**Texture User Effect**), rendering the shader result into a texture that you can use in a material

{% hint style="info" %}
There is a big collection of shader examples at [shadertoy](https://shadertoy.com/) resource.

Below you can find a topic on how to adapt a shadertoy shader.
{% endhint %}

## Assets

* User Effect
* Texture User Effect
* pre-defined effects: Blur Shader and Mix Shader

![](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2F0Z6ZnPWE0fheEtqy1QDa%2Fimage.png?alt=media\&token=bea9ae7a-f68b-4374-bcd3-73ffe4fd8ade)

### User Effect

In a user effect, you can define a shader file as a fragment GLSL shader to process the full-screen image. The file path can be specified relative to the folder where the scene is saved, or relative to the plugins folder. For example, _GLSL/simple130.glslv_ is a file distributed with the plugins as a default vertex shader, and in most cases it should be left unchanged.

![](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FE8yKRECYLE97R5I9WURj%2Fimage.png?alt=media\&token=139615c8-928b-4452-af8e-35cf4719abcc)

**Export Shader Scheme** is a debug option that processes the shader and outputs its uniforms to a text file in json format.

**Number of Passes** defines how many times the image is processed using the provided shader. On each iteration, a system uniform `iPass` represents the current iteration index.

**Resolution** can be used to downsample the processed image. For certain effects, such as blur, processing the full-resolution image is unnecessary; using a reduced resolution (e.g., half size) is often sufficient and more efficient.

**ShaderToy Compatibility** wraps your shader with a header and a footer, so a code from shadertoy can be used as is (see Shadertoy shaders and adaptation).

**Status** shows a short report of the last shader compilation, including warnings, for example a system uniform with a wrong type, an unsupported uniform type or a property name that is already used by the object.

**Remove Unused Properties** deletes the properties of the uniforms that are no longer in the shader (see User properties).

### Texture User Effect

The Texture User Effect renders a user effect shader into a texture. It's not a screen space effect: it doesn't run the whole post processing chain and there is no scene camera. Use the result as a regular texture, for example in a material, to make procedural textures (a brick wall, a dissolve with a burning edge on a character texture, etc.).

You can create it in the Asset Browser: _Templates / Shading Elements / Texture User Effect_.

* **User Effect** – connect the user effect object with the shader to render.
* **Update Mode** – when the texture is re-rendered: on every frame (for time dependent shaders), when the inputs or the effect properties are changed, or manually with the **Refresh** action.
* **Input layer** – the first layer of the texture is used as the `inputSampler` image. The layer is optional: without it, `inputSampler` is a blank image filled with the texture **Background Color**, and the texture size is **Blank Width** × **Blank Height** (1024 × 1024 by default).
* **Status** – a short text about the state of the texture (no effect, Ok, etc.).

Differences from the User Effect in the chain:

* the depth, linear depth, normal and mask samplers are not available (there is no scene render pass)
* there is no scene camera, so the camera uniforms get defaults: identity matrices, the camera at the origin, `zNear = 1`, `zFar = 1000`, and all lens values are 0
* `iTime` is MotionBuilder system time

Sample shaders are in the repository, _MB\_Scenes/GLSL_ folder: _brick\_wall.glslf_ (a seamless procedural brick tile) and _dissolve.glslf_ (an alpha dissolve with a glowing burn edge). A sample scene is _MB\_Scenes/postProcessing\_textureUserEffect.fbx_.

### Blur Effect

![](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FumLE73grkioK1poWhhJ3%2Fimage.png?alt=media\&token=ac0fdcdc-e5ac-4e33-a705-58051305dd19)

This is a built-in custom effect that can be part of a custom effects processing chain. It provides several additional options:

**Input Texture** – If left empty, the full-screen image from the previous effect is used. If a scene texture is specified, the video from that texture is used as the input. If you assign another effect or custom effect object here, it will be used as the input for this blur effect, forming a processing chain. The input effect (and any of its dependencies) is processed first, followed by the current effect.

**Blur Scale** – Defines the scale factor of the applied blur.

### Mix Effect

![](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FZkcy2aykGbAM3MzlksGt%2Fimage.png?alt=media\&token=db114382-ef51-4e55-a833-95451fb3c482)

This effect is used to mix two input images. It provides two input parameters: **Input Texture** and **Second Texture**.

For each input, you can specify:

* **Empty** – uses the previous texture from the post-processing chain
* **Scene texture** – uses a video/image texture as input
* **Another effect** – uses the result of a processed effect as input for that channel

**Bloom** is an optional mode, and **Inverse** modifies how the two images are combined.

By default, the two textures are combined using multiplication. When **Inverse** is enabled, the result is computed by multiplying the inverse of the first texture with the second texture.

When **Bloom** is enabled, the mixing changes to an additive model: the first texture is multiplied by a bloom tone, and the second texture is multiplied by a bloom stretch, and the results are then added together.

### Examples

You can find example scenes in the openmobu repository / MB\_Scenes folder, and the shader files in MB\_Scenes / GLSL.

![original scene without post processing](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FE3RDOvpEJFcq2h6Kliqs%2Fcustomeffect_originalImage.jpg?alt=media\&token=4ae9623d-6ba5-4ddc-971e-111d537859fa)

![fxaa effect](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2F7blJr7qYeLNCBUBp9uCG%2Fcustomeffect_fxaa.jpg?alt=media\&token=586f1459-a0cf-4fbe-8c3e-f282fc666df9)

![god rays effect](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FAzcziWSILKRtUXY7URBu%2Fcustomeffect_godrays.jpg?alt=media\&token=34b25c3c-48eb-4995-a9fd-1ba2e1785ff3)

![sketchy effect](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FxlFCg23s0OcHqQHclbze%2Fcustomeffect_sketchy.jpg?alt=media\&token=11b380dd-a5a8-415f-8f02-43d0c918c326)

![sketchy effect 2](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FRqYvapqM599gSsMQflBh%2Fcustomeffect_sketchy2.jpg?alt=media\&token=00f003a8-5abc-44f5-9412-e4d35979bef4)

## Writing a shader

### Shader skeleton

The default vertex shader (_GLSL/simple130.glslv_) draws a full-screen quad and passes `texCoord` in the 0..1 range.

```glsl
#version 140

in vec2 texCoord;
out vec4 FragColor;

uniform sampler2D inputSampler;   // system: the effect input image
uniform vec2      texelSize;      // system: 1 / render target size
uniform float     iTime;          // system: seconds

uniform float     strength_slider; // user property: a 0..100 slider

void main(void)
{
    vec4 color = texture(inputSampler, texCoord);
    FragColor = vec4(color.rgb * (0.01 * strength_slider), color.a);
}
```

Optional text insertions are lines that the loader replaces with a snippet from the _GLSL_ folder. The whole line is replaced, so anything after the keyword on that line is dropped.

* `// INSERT: HEADER` inserts _insert\_header.glslf_: `#version 140`, `texCoord`, `fragColor`, `inputSampler`, `maskSampler`, `useMasking`, `upperClip`, `lowerClip`.
* `// INSERT: APPLY_IMAGE_CROP` inserts _insert\_image\_crop.glslf_: it returns the input color outside the clip range.
* `// INSERT: APPLY_MASKING` inserts _insert\_masking.glslf_: it mixes the result back to the input by the mask.

## Uniforms

A GLSL shader contains **uniforms** that define inputs provided either by the user or by the system (via predefined uniform names). These uniforms are parsed into a shader scheme, and the corresponding object properties are created automatically.

### System uniforms

A **system uniform** is a uniform with one of the reserved names below. The plugin fills it automatically for every pass, and it does **not** become a property in the UI.

* Declare it with **exactly** the name and type from the tables. If the type doesn't match, the uniform is skipped: it keeps the shader's default value, and the effect Status shows a warning. A `bool` is accepted where a `float` is expected.
* A system uniform that the shader declares but never uses is stripped by the compiler. That's fine: the plugin only fills active uniforms.
* Matrices are OpenGL column-major, so use them like `projection * modelView * vec4(p, 1.0)`.

#### Input textures (`sampler2D`)

| name               | slot | description                                                                                                                                   |
| ------------------ | ---- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| inputSampler       | 0    | the effect input image: the result of the previous effect in the chain, or the scene                                                          |
| iChannel0          | 0    | same as inputSampler (ShaderToy naming)                                                                                                       |
| depthSampler       | 1    | scene depth buffer, non-linear (window-space depth, 0..1). Bound only when an effect in the chain uses it                                     |
| linearDepthSampler | 2    | linear depth: positive camera-space distance in scene units (`.r`). Produced only when an effect in the chain uses it; can be half resolution |
| normalSampler      | 3    | world-space normals. **Not produced yet**, don't rely on it                                                                                   |
| maskSampler        | 4    | the mask channel selected for the effect, the mask value is in `.r`. Use it together with `useMasking`                                        |

#### Masking and clipping (`float`)

| name       | range  | description                                                                                                              |
| ---------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| useMasking | 0 or 1 | 1 when the effect **Use Masking** is on, or when the post processing settings have **Enable Masking For All Effects** on |
| upperClip  | 0..1   | lower edge of the processed band, in `texCoord.y` space                                                                  |
| lowerClip  | 0..1   | upper edge of the processed band, in `texCoord.y` space                                                                  |

User effects have no clip properties of their own, so they get `upperClip = 0` and `lowerClip = 1`, which means no clipping.

```glsl
if (texCoord.y < upperClip || texCoord.y > lowerClip)
{
    FragColor = texture(inputSampler, texCoord); // outside the band: pass the input through
    return;
}
...
if (useMasking > 0.0)
{
    float mask = texture(maskSampler, texCoord).r;
    FragColor.rgb = mix(FragColor.rgb, texture(inputSampler, texCoord).rgb, mask * useMasking);
}
```

#### Resolution (`vec2`)

| name           | description                                            |
| -------------- | ------------------------------------------------------ |
| gResolution    | render target size in pixels, for example (1920, 1080) |
| iResolution    | same as gResolution (ShaderToy naming, `vec2`)         |
| uInvResolution | 1.0 / resolution                                       |
| texelSize      | 1.0 / resolution                                       |

These describe the **render target** of the effect. It's smaller than the viewport when the effect runs downscaled (the **Resolution** property set to 1/2 or 1/4). For sampling neighbouring pixels of the input, use `texelSize`.

#### Time

| name  | type  | description                                                                                                                                                                           |
| ----- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| iTime | float | system time in seconds. In the effects chain it counts from the moment the post processing context started. It's **not** the timeline time: it keeps running when playback is stopped |
| iDate | vec4  | local date and time: (year, month 1..12, day 1..31, seconds since midnight)                                                                                                           |

#### Camera

The values are for the camera of the processed view, evaluated for the current frame. In a file render, every frame is evaluated right before it is rendered.

| name                | type  | description                                                                                                                                                                                           |
| ------------------- | ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cameraPosition      | vec3  | world-space camera position                                                                                                                                                                           |
| modelView           | mat4  | camera view matrix (world → camera space, the camera looks down −Z)                                                                                                                                   |
| projection          | mat4  | camera projection matrix                                                                                                                                                                              |
| modelViewProj       | mat4  | `projection * modelView`                                                                                                                                                                              |
| invModelViewProj    | mat4  | inverse of modelViewProj (clip space → world space)                                                                                                                                                   |
| prevModelViewProj   | mat4  | modelViewProj of the previous frame (motion vectors, motion blur)                                                                                                                                     |
| zNear               | float | camera near plane distance, in scene units                                                                                                                                                            |
| zFar                | float | camera far plane distance, in scene units                                                                                                                                                             |
| cameraIsOrtho       | float | 1 for an orthogonal camera, 0 for a perspective one                                                                                                                                                   |
| cameraFocusDistance | float | focus distance in scene units, resolved from the camera **Focus Distance Source** (Camera Interest, Focus Model or Focus Specific Distance). The same value DOF uses with _Use Camera DOF Properties_ |
| cameraFocusAngle    | float | camera **Focus Angle**, in degrees                                                                                                                                                                    |
| cameraFocalLength   | float | camera **Focal Length**, in mm                                                                                                                                                                        |
| cameraFov           | float | camera **Field Of View**, in degrees. Whether it is horizontal or vertical follows the camera aperture mode                                                                                           |
| cameraFocalLengthPx | float | focal length in pixels of the viewport height (`0.5 * viewHeight * projection[1][1]`), 0 for an orthogonal camera                                                                                     |

Examples of using the depth and camera uniforms:

```glsl
uniform sampler2D depthSampler;
uniform sampler2D linearDepthSampler;
uniform mat4  invModelViewProj;
uniform float zNear, zFar;
uniform float cameraFocusDistance;

// world position of a pixel from the depth buffer
vec3 WorldPosition(vec2 uv)
{
    float d = texture(depthSampler, uv).r;
    vec4 clip = vec4(uv * 2.0 - 1.0, d * 2.0 - 1.0, 1.0);
    vec4 world = invModelViewProj * clip;
    return world.xyz / world.w;
}

// 0 at the focus distance, growing with the distance from it
float DefocusAmount(vec2 uv)
{
    return abs(texture(linearDepthSampler, uv).r - cameraFocusDistance) / max(cameraFocusDistance, 0.0001);
}
```

### User properties

Any other active uniform becomes a property of the user effect object.

* **Supported types:** `float`, `int`, `bool`, `vec2`, `vec3`, `vec4`, `sampler2D`. Other types (`samplerCube`, `ivec*`, `mat3`, `mat4`, …) and arrays are skipped with a warning in Status.
* **`sampler2D`** is an object property, exposed as a list. There are several ways to define the input texture:
  * **Leave it empty** – the texture from the previously processed effect in the post processing chain is used (for this purpose you can also use `inputSampler` or `iChannel0`).
  * **Attach a scene texture** – the video/image from that texture is bound as the input to the shader.
  * **Attach another effect object** – the result of that effect's processing is used as the input texture.
* **A uniform that disappears** (removed, or stripped by the compiler because it's unused) keeps its property, together with its value and animation. Status shows the number of unused properties, and **Remove Unused Properties** deletes them.
* **Names are reserved:** a uniform can't have the name of one of the effect object's own properties (for example `Active` or `Resolution`). Such a uniform is skipped with a warning.

#### Uniform naming rules

During compilation the shader effect picks the system uniforms and user uniforms. For user uniforms it makes object properties, so that a user can customize the input values for the shader render. A special postfix of a uniform name customizes the UI element. The postfix is removed from the property name.

| Uniform type  | Postfix  | Description                                                                                                        |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------ |
| float         | \_slider | a number property with a clamped range \[0; 100]. The value is passed to the shader as is                          |
| float or bool | \_flag   | a checkbox, the on/off flag is passed as 0.0 or 1.0. Any `bool` uniform is a checkbox, with or without the postfix |
| vec2          | \_wstoss | a world-space vector3 property, converted into a screen-space vec2 uniform                                         |
| vec3          | \_color  | a color RGB picker, output as vec3                                                                                 |
| vec4          | \_color  | a color RGBA picker, output as vec4                                                                                |

### Object slots

An object slot lets a shader follow a scene object, such as a light, a null or a mesh, the way the lens flare follows its **Flare Light**.

```glsl
uniform vec3  sun_object;            // world position of the connected object -> creates the "sun" slot
uniform vec3  sun_object_color;      // light colour, white for a non-light
uniform float sun_object_intensity;  // light intensity, 1.0 for MotionBuilder's default 100
uniform vec3  sun_object_screen;     // (u, v, view depth) of the object on screen
uniform float sun_object_connected;  // 1 when an object is connected, 0 otherwise
```

* **The slot.** `uniform vec3 <name>_object;` creates one property, **\<name>**, on the user effect. It's an object slot that takes a single model: drag a light, a null or any other model onto it from the Navigator or the Schematic view.
* **Companion uniforms.** Uniforms named `<name>_object_<field>` don't create properties of their own; they are filled from the slot **\<name>**. Declare only the ones the shader needs. The slot is created even when the shader uses only companions.

| uniform                    | type          | description                                                                                                              |
| -------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------ |
| \<name>\_object            | vec3          | world position of the object                                                                                             |
| \<name>\_object\_color     | vec3          | the light **Diffuse Color**, white for a non-light                                                                       |
| \<name>\_object\_intensity | float         | the light **Intensity** / 100, so the default light gives 1.0; 1.0 for a non-light                                       |
| \<name>\_object\_screen    | vec3          | screen position (u, v) in 0..1 and view depth in scene units. The depth is negative when the object is behind the camera |
| \<name>\_object\_connected | float or bool | 1 when an object is connected, 0 otherwise                                                                               |

* **Nothing connected:** position, screen and connected are 0, while colour is white and intensity is 1. Multiply by `<name>_object_connected` to fade the effect out.
* **Every frame:** the values are evaluated for the current frame, so an animated object is followed without delay, including in a file render.
* **Wrong type:** a companion declared with the wrong type (for example `vec4 <name>_object_color`) is skipped with a warning in Status.
* **Unknown fields:** an unknown field after `_object_` (for example `float my_object_scale`) is an ordinary property.
* **Several objects:** use one slot per object (`sun_object`, `moon_object`, …); arrays aren't supported.
* **Texture User Effect:** there is no camera, so `<name>_object_screen` is always 0. The other values work.

Example (a glow at the object on screen, _MB\_Scenes/GLSL/object\_slot\_glow.glslf_):

```glsl
#version 140

in vec2 texCoord;
out vec4 FragColor;

uniform sampler2D inputSampler;
uniform vec2  gResolution;
uniform vec3  sun_object_screen;
uniform vec3  sun_object_color;
uniform float sun_object_intensity;
uniform float sun_object_connected;

uniform float radius_slider;  // glow radius in percents of the screen height, 0 is the default 20 percents

void main(void)
{
    vec4 color = texture(inputSampler, texCoord);

    // aspect corrected distance to the object on screen
    vec2 delta = (texCoord - sun_object_screen.xy) * vec2(gResolution.x / gResolution.y, 1.0);
    float radius = (radius_slider > 0.0) ? 0.01 * radius_slider : 0.2;
    float glow = 1.0 - smoothstep(0.0, radius, length(delta));

    // no glow when nothing is connected or the object is behind the camera
    float visible = sun_object_connected * step(0.0, sun_object_screen.z);

    color.rgb += sun_object_color * sun_object_intensity * glow * visible;
    FragColor = color;
}
```

## Shadertoy shaders and adaptation for a custom effect

### ShaderToy compatibility mode

![](https://21658545-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FYKfPCkQKpHW2adXsZwun%2Fuploads%2FzbMBmtQcDSQcKOliKyEt%2F%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0_20260111_132017.png?alt=media\&token=9b6e1391-f4f8-40a3-9065-1de0e873846c)

Turn on the **ShaderToy Compatibility** property in your user effect to wrap the shader with a header and a footer that support code from a shadertoy effect:

* **Before your code:** the header declares `#version 140`, `uniform vec2 iResolution`, `uniform float iTime`, `uniform sampler2D iChannel0`, `in vec2 texCoord` and `out vec4 FragColor`.
* **After your code:** `main()` calls `mainImage(FragColor, texCoord * iResolution)`.

So you write only `void mainImage(out vec4 fragColor, in vec2 fragCoord)` and use `iResolution`, `iTime` and `iChannel0` as in shadertoy. Note that `iResolution` is a `vec2` here, not a `vec3` as in shadertoy.

### Steps to manually adjust your shader

First of all, we have to add a header with the shader version and the input and output values. The input value is `vec2 texCoord`, which represents a processed texel position in uv space \[0; 1], and the output is `vec4 FragColor`, which is our computed texel color.

```glsl
#version 140

in vec2 texCoord;
out vec4 FragColor;
```

The main entry function has to be renamed from

```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
```

into

```glsl
void main(void)
```

The output variable to store a texel color is called `FragColor` with a capital letter, while a shadertoy shader has `fragColor` with a lower case.

The input `texCoord` is in uv space, while a shadertoy shader operates with `fragCoord` in image space. In order to convert from uv space into image space, multiply the tex coord by the image resolution.

```glsl
vec2 fragCoord = texCoord * iResolution.xy;
```

where `iResolution` is a system uniform that has to be declared in the top of the shader

```glsl
uniform vec2 iResolution;
```

For shaders that require buffers, you need to create each buffer as a separate GLSL fragment shader and use them to compose an effect shader. Then, connect these shaders together in the execution chain. When you have a `sampler2D` uniform, it will be exposed as a list object property in MotionBuilder, allowing another shader or texture to be used to compute that input.
