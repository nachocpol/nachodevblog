+++ 
draft = true
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

As oposed to other nose cones, Haack series do not represent a geometric figure, but they are created following a formula that tries to minimize drag.

<div>
	<p>Generation parameter</p>
	<input type="range" min="0" max="0.66" value="0.33" step="0.01" id="haackParam">
</div>
<button>
Update!
</button>

<div id="threeCanvas" style ="background-color:#FFF; width:720; height:480px; margin:0 auto;">
<script src="/js/three.js"></script>
<script>
	var container = document.getElementById('threeCanvas');
	var width = container.offsetWidth;
	var height = container.offsetHeight;
	var renderer = new THREE.WebGLRenderer();
	renderer.setSize( width, height );
	container.appendChild( renderer.domElement );

	var scene = new THREE.Scene();
	var camera = new THREE.PerspectiveCamera(75,width/height,0.1,1000);
	camera.position.z = 5;
	camera.position.x = -3;
	
	setupHaack();
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

	function setupHaack() 
	{
		var material = new THREE.LineBasicMaterial( { color: 0xffffffff } );
		var geometryUp = new THREE.Geometry();
		var geometryDown = new THREE.Geometry();

		var noseLength = 8.0;
		var noseRadius = 2.0;
		var C = 0.0;

		var iterations = 120;
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

		var lineUp = new THREE.Line(geometryUp, material);
		scene.add(lineUp);
		var lineDown = new THREE.Line(geometryDown, material);
		scene.add(lineDown);
	}

	function render()
	{
		requestAnimationFrame(render);
		renderer.render( scene, camera );
	}
</script>
</div>


