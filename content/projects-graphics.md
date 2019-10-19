---
title: "Projects Graphics"
---

## 2019

### Software rasterizer
<p style="text-align: justify;">
	Rasterization is something that as graphics programmer I deal with daily. However, the implementation details are not so obvious and there are plenty optimization techniques.
	My version of a rasterizer implements: perspective correct interpolation, rendering meshes, programable shaders, multi threaded binning and depth buffer.
</p>

<a target="blank_" href="https://github.com/piluve/NRaster">Source code.</a>.

<img src="../images/raster.jpg"/>


## 2018

### Volumetric clouds rendering
<p style="text-align: justify;">
	Rendering clouds has always been hard due to the nature of algorithms used to render it: some type of ray-casting. In the past few years companies like Simul Software pushed the realm of volumetric clouds into the games industry. Other companies like Guerrilla Games also implemented real-time cloud rendering systems. I based my research in the knowledge gained working at Simul and also by reading papers from <a target="blank_" href="https://www.guerrilla-games.com/read/the-real-time-volumetric-cloudscapes-of-horizon-zero-dawn">Guerrilla</a> and <a target="blank_" href="http://bitsquid.blogspot.co.uk/2016/07/volumetric-clouds.html">Stingray</a>.
</p>
<p style="text-align: justify;">
	I implemented the first (brute force) implementation using Shadertoy, you can check it out <a target="blank_" href="https://www.shadertoy.com/view/MtfBD7">here</a>.
	After the initial implementation, I worked on a standalone version using DX12. I implemented several optimization techniques to run this smoothly at run time and keeping a great look.
</p>

+ **Noise precalculation:** implemented several noise algorithms in the CPU: perlin, simplex and worley. 

+ **Directional light shadow:** this involves marching additional rays for each main ray. Because the clouds tile, I decided to compute the shadow pass once per frame using a compute shader.

The source for the project is in <a target="blank_" href="https://github.com/piluve/AwesomeEngine">GitHub</a>.

{{< youtube id="PoWlGZ7w7jY" >}}

## 2017

### Demo reel
<p style="text-align: justify;">
	This video showcases the most important projects I worked on from 2016 to 2017.
</p>

{{< youtube id="_Njr7qLtJQQ" >}}


### Nature rendering
<p style="text-align: justify;">
	Nature scenes are an important part for many videogames. They are huge and detailed scenaries that have the power to inmerse the player in the game. It also comes with other kind of problems like rendering large terrains, realistic skies, undergrow generation and placement. All of this should be done at interactive framerates. For this project, I wanted to put together diferent elements and optimization techniques using Opengl (selfmade framework). 
</p>

[Source(GitHub)](https://github.com/piluve/Nature)

{{< youtube id="Vo7kEmEnR04" >}}


### Demo my Sponza
<p style="text-align: justify;">
	For this project, I had to take a basic framework provided by the university (with window creation and inner loop managment) and render the sponza scene. 
</p>

<ul style="text-align: justify;">
<li>
	<b>Physically based rendering:</b> implemented using Cook-Torrance and custom enviroment mapping.
</li>
<li>
	<b>Shadow mapping:</b> with smooth edges using multiple samples and poisson sampling.
</li>
<li>
	<b>Deferred:</b> a deferred rendering pipeline. As it was my first attemp at deferred, I made it with a full fat G-buffer.
</li>
<li>
	<b>Antialiasing:</b> using a deferred renderer is very powerful but aliasing is a problem. I implemented FXAA to solve this problem.
</li>
<li>
	<b>SSAO:</b> developed using John Chapman's article.
</li>
</ul>

[Source(GitHub)](https://github.com/piluve/DemoMySponza)

{{< youtube id="xDSvOYhCZ0A" >}}

## 2016

### Serious Engine & The R Island
<p style="text-align: justify;">
	During my three years at ESAT I worked in Serious Engine, a 3D rendering engine made using C++ and OpenGl. Using this engine, I developed a demo scene: "The R Island", on it, you can see water rendering using planar reflections and refractions, procedural mesh placing, instancing, screen space light scatering (god rays), procedural texturing etc.
</p>

[Source(GitHub)](https://github.com/piluve/TheRIsland)

{{< youtube id="myyoXAXFSsQ" >}}