+++
date = "2016-05-07T16:12:31+03:00"
title = "Brief introduction to Yume Engine and Screenshots"
image = "1.jpg"
slug = "yume"
+++

**Yume Engine** is my playground for graphics experiments and developing my skills in graphics programming.
It took me a lot to finally come up with an idea which allows me to build a high level renderer that is simple but effective.![alt](../../images/2.png#img-yume1)
I wanted to have 2 things at first.One being able to create fast prototypes while other is being simple to read/comprehend.
Yume Engine is not a real game engine.I dont have to think about masses using this to create games so
 I used hacks here and there to get things going quickly,when it wouldnt be plaisuble in a real engine situation.
 In this article I'm going to talk about how Yume engine handles state changes,how it executes draw calls and hopefully
 provide source code at the end of the post so you,reader, maybe get a hold of how this engine works.

For a long time I didnt know how I can create a high level renderer,or to put it better,
I didnt know where to start.After several hundreds of hours inspecting,studying other engines
 I now have how I could go for building a renderer that is not so complicated and does what I want.
 Along the way I have learnt a lot,be it my coding proficiency or my graphics knowledge.
 To people out there who dont know where to start,it is a very good idea to examine open source engines like
 Ogre3D,Horde3D,Urho3D and many more! You can find many similarities in Yume by looking at those engines. <br />
 Although Yume doesn't have many features, there are several which I'm really happy to have. Such as Global Illumination!

# Deferred Rendering
Yume Engine uses full deferred pipeline.For most of the samples,the GBuffer has 4 render targets, **Diffuse Albedo**,**Specular Albedo**,**Linear Depth** and **Normals**.

![](../../images/gbuffer.png)

We then use these render targets in the next stages of rendering,such as Deferred Lights.For the moment, I didnt use hardware depth buffer because I also store the linear depth as

    float2 linearDepth = float2(z, z*z);

to use in *Variance Shadow Mapping*.

There is also **Reflective Shadow Map** support, which is basically rendering to the GBuffer but from the lights perspective.

# Deferred Lights

Since Yume is fully deferred,there has to be a way to implement lights.In a deferred pipeline,we must create a pass for each light,be it directional,
point or spot light.

For each point light,we draw a tesellated sphere,move it to lights world position and scale it to point lights range.
We then enable stencil test using the stencil buffer from the GBuffer pass. Comparing against stencil buffer yields the pixels that will be in the point lights range of influnce.
Then we shade the pixels by *reconstructing the depth* in the pixel shader using the Linear Depth from the GBuffer pass.

## Results

![](../../images/plight1.jpg)

![](../../images/plight2.jpg)

![](../../images/plight3.jpg)

# Horizon Based Ambient Occlusion

Adapting from the sample in NVidia SDK's DirectX 9 HBAO implementation, Yume Engine supports HBAO. Ambient Occlusion is not a simple concept to explain but in easiest words,for pixels in depth buffer,we assume a hemisphere,then shoot rays at random directions to see if they occlude something.If they do we add a occlusion factor.Note that this is the very basic idea of Ambient Occlusion and there are several implementations such as Scaleable Ambient Obscurance,ABAO,SSAO,SSDO,AAO.. For more information see [GPU GEMS 3](http://http.developer.nvidia.com/GPUGems3/gpugems3_ch12.html)
Here is a gif of the effect when HBAO is off,max,med and normal qualities.

![](http://i.imgur.com/w3S2uNA.gif)

Here is a side by side comparison

![alt](../../images/hbaosidebyside.png)
