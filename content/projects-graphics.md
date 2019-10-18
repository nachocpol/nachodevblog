---
title: "Projects Graphics"
---

### Demo reel 2016-2017
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


### Serious Engine & The R Island
<p style="text-align: justify;">
During my three years at ESAT I worked in Serious Engine, a 3D rendering engine made using C++ and OpenGl. Using this engine, I developed a demo scene: "The R Island", on it, you can see water rendering using planar reflections and refractions, procedural mesh placing, instancing, screen space light scatering (god rays), procedural texturing etc.
</p>

[Source(GitHub)](https://github.com/piluve/TheRIsland)

{{< youtube id="myyoXAXFSsQ" >}}