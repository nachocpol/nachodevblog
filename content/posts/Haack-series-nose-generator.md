+++ 
draft = false
date = 2019-10-26T20:10:57+01:00
title = "Haack Series nose generator"
description = ""
slug = "" 
tags = ["rocketry"]
categories = []
externalLink = ""
series = []
+++

{{%format/text-align-justify%}}
As a rocket designer, there are many nose cone types to choose from. For low to mid power rocketry, the choice can be based more on looks than anything else. Rockets reaching transonic and supersonic speeds should definetly make a fact based decission. For my next build I want to use a Haack nose cone, because I will be 3D printing it, I need a way of generating a 3D mesh. In this first post, I will explore how to generate a Haack profile. 
{{%format/text-align-justify%}}
### Haack Series
As oposed to other nose cones, Haack series do not represent a geometric figure, but they are created following a formula that tries to minimize drag. As input to this formula, one provides the nose radius, lenght and a *haack parameter*. In the visualization bellow, I am clamping this value in the range 0-0.66. This parameter values have special meanings:

*	**0:** LD-Haack (Von Kármán), minimun drag for the length and diameter given.
*	**0.33:** LV- Haack, minimum drag for length and volume.
*	**0.66:** Tangent

The formula used to compute the nose looks like this:

![](../../images/Haack/haack-formula.svg "Formula")
*Source: https://en.wikipedia.org/wiki/Nose_cone_design#Haack_series*


### Visualization
I created this visualization using threeJS. You can tweak the Haack parameter!
<div>
	<label>Haack Param: </label>
	<input type="range" min="0" max="0.66" value="0.33" step="0.01" id="haackParam" oninput="previewParam.value = haackParam.value;haackUpdate()">
	<output id="previewParam">0.33</output>
	</br>
</div>

<div id="threeCanvas" style ="background-color:#FFF; width:720; height:480px; margin:0 auto;">
<script src="/js/three.js"></script>
<script>
	var container = document.getElementById('threeCanvas');
	var width = container.offsetWidth;
	var height = container.offsetHeight;
	var renderer = new THREE.WebGLRenderer({ antialias: true });
	renderer.setSize( width, height );
	container.appendChild( renderer.domElement );

	var scene = new THREE.Scene();
	var camera = new THREE.PerspectiveCamera(75,width/height,0.1,1000);
	camera.position.z = 4;
	camera.position.x = -4;
	
	haackUpdate();
	render();

	//----------------------------

	function haackOmega(x,L)
	{
		return Math.acos(1.0 - ((2 * x) / L));
	}

	function haackRadius(R, omega, C)
	{
		var sinO3 = Math.sin(omega) * Math.sin(omega) * Math.sin(omega);
		return (R / (Math.sqrt(Math.PI))) * Math.sqrt(omega - (Math.sin(2*omega) * 0.5) + (C * sinO3));
	}

	function haackUpdate() 
	{
		// Read UI stuff
		var haackCSlider = document.getElementById("haackParam");

		var material = new THREE.LineBasicMaterial( { color: 0xffffffff } );
		var geometryUp = new THREE.Geometry();
		var geometryDown = new THREE.Geometry();

		var noseLength = 8;
		var noseRadius = 2;
		var C = haackCSlider.value;

		var iterations = 240;
		var xstep = noseLength / iterations;
		var curX = 0.0;

		for (var i = 0; i <= iterations; i++) 
		{			
			var x = xstep * i;
			var omega = haackOmega(noseLength - x, noseLength);
			var radius = haackRadius(noseRadius, omega, C);
			
			geometryUp.vertices.push(new THREE.Vector3(-x,  radius, 0));
			geometryDown.vertices.push(new THREE.Vector3(-x, -radius, 0));
		}

		// Deal with previous objects: 
		var oldLineUp = scene.getObjectByName("LineUp");
		if(oldLineUp)
		{
			scene.remove(oldLineUp);
		}
		var oldLineDown = scene.getObjectByName("LineDown");
		if(oldLineDown)
		{
			scene.remove(oldLineDown);
		}

		// Insert neew nose into the scene:
		var lineUp = new THREE.Line(geometryUp, material);
		lineUp.name = "LineUp";
		scene.add(lineUp);
		var lineDown = new THREE.Line(geometryDown, material);
		lineDown.name = "LineDown";
		scene.add(lineDown);
	}

	function render()
	{
		requestAnimationFrame(render);
		renderer.render( scene, camera );
	}
</script>
</div>


Hope this was useful, in the next post I want to create a tool that will allow me to create the nose cone in 3D after setting a few parameters and then exporting the results as a 3D mesh.