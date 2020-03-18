
# Retro Shader Pack by DaveFace

This is an Unreal Engine 4 material / post-process pack for all your retro / PS1-esque game needs. I may add more stuff over time but I think this provides the most common effects you'd want for this kind of project.

Some functions are based on other people's work online, but as most of this work was done some months ago for a private project I can't remember all possible contributions. If I've forgotten to give someone credit, my apologies, it isn't intentional.

Happy to accept contributions if anyone has improvements / tweaks / new shaders to add.

## Getting Started

Download a copy of the repository, and put 'RetroShaders' in your project's Content directory.

The 'ExampleContent' folder is optional, none of the core materials / material functions should depend on it.

### Unreal Engine Version

This has been made / tested in Unreal Engine version 4.24. It should work in previous and subsequent versions, though, as the functionality is pretty basic.

### Recommended Settings

Here's a few things you'll probably want to change in your project settings.

#### Rendering

- Auto Exposure *OFF*
- Motion Bluer *OFF*
- Anti-Aliasing Method *NONE* or *FXAA*

#### Device Profiles
You can find this under Window > Developer Tools > Device Profiles

 1. Click the tool icon under 'CVars' on your platform e.g. Windows (note - WindowsNoEditor etc. all inherit from Windows, so no need to edit each one)
 2. In Console Variables, in the Rendering tab, add `r.upscale.quality` with a value of `0` - this means that when we drop the render resolution, we get nice crunchy pixels
 3. In Console Variables, in the Scalability tab, add `sg.resolutionquality` with a value of `100`
 4. In Texture LOD Settings, for the following texture types, set the `MinMagFilter` to `point`: World, WorldNormalMap. This makes those texture groups render with nearest neighbor filtering by default, so you don't have to change it for each texture
 5. Optionally, you can do the same thing for other texture groups including `UI` and `Lightmap` so you get nice pixellation on those, too

## Documentation

I'll give the basics of each shader and it's use here.

### Retro Material
The retro material has the following functions:

 - Diffuse (Texture / Colour) and Normal Map support
 - Posterise texture (reduces colours of diffuse texture to mimic small colour pallete textures)
 - Opacity Mask / Transparancy Support with a screen space dithering effect
 - Affine texture warping (mimics PS1 hardware)
 - Vertex Wobble (mimics PS1 hardware / some early PC games e.g. Quake and Unreal)

### Post Process
The 'RetroLook' post process material does several things:


#### Posterise
Posterising reduces the amount of colours on the screen, creating banding effects. Older consoles / PCs used 8 and 16 bit colour palettes which caused this.

You may want to tweak this value per scene (or at least, have a few instances with different values) as more vibrant scenes will want more colours than desaturated ones. A desaturated scene with a higher amount of colours will look fairly normal, while a vibrant scene with a low amount of colours will look visually awful.

I found that posterising individual textures (using the posterise texture material function) helped balance this out a bit.

#### Dither
Adds a fake dithering effect to the screen. Dithering was common on the PS1 as it hid the lower colour bitdepth (which we fake with the posterise effect, above). This looks best when it matches the scale of your screen pixellation, which is (sort of) the case in the 'advanced resolution' blueprint.

The Dithering method here isn't ideal, it's the shader I'd most like to improve in future.

#### Pixellate
Pixellates the screen UV's. Usually, you're better off adjusting the render resolution, but this is included for a good reason (see advanced resolution, below)

### Advanced Resolution

This is a little technique I came up with to get the performance benefits of running the game at a lower resolution, while still having a small amount of 'super sampling' to increase visual quality. Just drop BP_AdvResolution in your level and hit play.

Essentially, the code makes the engine render at a standard base resolution (by default, 720px vertical), and then uses the post process pixellate effect to drop it further to the final resolution (by default, 350px vertical). I chose to tie this to the vertical resolution so it scales well for wide/ultrawide monitors.

If you drop a sharpen effect between the two *(i.e. add a sharpen effect before the retrolook post process material)* it enhances the effect further.
