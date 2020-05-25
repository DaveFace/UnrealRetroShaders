
# Retro Shader Pack by DaveFace
**Current Version: v2 (05/04/2020)**

This is an Unreal Engine 4 material & post-process pack for all your retro PS1-esque game needs. I may add more stuff over time but I think this provides the most common effects you'd want for this kind of project.

Some functions are based on other people's work online, I've tried to include comments linking to their work where I can remember. If I've forgotten to give someone credit, my apologies, it isn't intentional.

Happy to accept contributions if anyone has improvements / tweaks / new shaders to add.

![](Preview.jpg)

## Getting Started

For Version 2, I'm distributing this as a complete project. But really all you need is the 'RetroShaders' folder in the content directory. The 'ExampleContent' folder is optional, none of the core materials / material functions should depend on it.

### Unreal Engine Version

This has been made / tested in Unreal Engine version 4.24. It should work in previous and subsequent versions, though, as the functionality is pretty basic.

### Recommended Settings

Here's a few things you'll probably want to change in your project settings to get the 'retro look'

#### Rendering

- Auto Exposure *OFF*
- Motion Bluer *OFF*
- Anti-Aliasing Method *NONE*

#### Device Profiles
You can use devie profiles to set some global console variables and texture settings for your project; this is easier than manually setting each texture to use nearest neighbor (point) filtering, for example.

You can find this under Window > Developer Tools > Device Profiles

Click the tool / spanner icon under 'CVars' on your platform e.g. Windows (note - WindowsNoEditor etc. all inherit from Windows, so no need to edit each one)

* In Console Variables, in the Rendering tab, add `r.upscale.quality` with a value of `0` - this means that when we drop the render resolution, we get nice crunchy pixels
* In Console Variables, in the Rendering tab, add `r.shadowquality` with a value of `1` - this turns off filtering for dynamic shadows
* In Console Variables, in the Scalability tab, add `sg.resolutionquality` with a value of `100`
* In Texture LOD Settings, for World & WorldNormalMap, set the `MinMagFilter` to `point`. This makes those texture groups render with nearest neighbor filtering by default, so you don't have to change it for each texture
* Optionally, you can do the same thing for other texture groups including `UI` and `Lightmap` so you get nice pixellation on those, too

## Documentation

I'll give the basics of each shader and it's use here.

### Retro Material
The retro material has the following functions:

 - Diffuse (Texture / Colour) and Normal Map support
 - Posterise texture (reduces colours of diffuse texture to mimic small colour pallete textures)
 - Opacity Mask / Transparancy Support with a screen space dithering effect (this can produce some weird results with the post process pixellation, I'm not sure how to fix this yet)
 - Affine texture warping (mimics PS1 hardware)
 - Vertex Wobble (mimics PS1 hardware / some early PC games e.g. Quake and Unreal)

The amount of vertex wobble and the strength of the affine texture warp can be controlled in the material parameter collection `MPC_RetroShaders`

### Post Process
The `BP_RetroPostProcess` blueprint should handle most post process functions out the box, but you can use the post process materials directly if you really want to.

The material does three things:

1. Pixellate - Pixellates the screen, using a target vertical number of pixels.
2. Posterise - Reduces the amount of colours on screen, causing banding. Emulates older bitdepth graphics like the PS1. You will probably want toa adjust this per level, or use the diffuse posterisation on the model material to balance everything out.
3. Dithering - Adds a dithering effect to reduce the appearance of colour banding (above); I'm not really happy with the implementation here, I'd like to improve it.

The `BP_RetroPostProcess` blueprint gives you an easy way to set these paramaters per-level, but also does a little downsample trickery. In addition to the `VerticalResolution` value (which is the final vertical number of pixels on screen), you can set a `DownsampleResolution` which drops the engine renderscale to match that vertical resolution. I'd recommend having this value ~200px above your final pixellate resolution, which gives you a little bit of supersampling at a fairly negligible performance cost.
