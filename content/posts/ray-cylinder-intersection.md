+++ 
draft = true
date = 2020-02-15T20:57:45Z
title = "Ray-Cylinder intersection"
description = ""
slug = "" 
tags = ["math","algebra"]
+++

{{< format/text-align-justify >}}
For a given cylinder with radius $R$ and height $H$, a ray with origin $P$ and direction $RD$, find the first intersection point $P'$ between the ray and the cylinder:
{{</ format/text-align-justify >}}

![](../../images/RayVSCylinder/fig1.png "Figure1")

{{< format/text-align-justify >}}
First, I will simplify the problem by flatening the ray (so effectively this becomes a 2D problem). You can understand this, as trying to figure out the intersection point between the ray and a slice of the cylinder.
{{</ format/text-align-justify >}}

![](../../images/RayVSCylinder/fig2.png "Figure2")

$C$ is the slice center: (Cylinder.X, P.Y, Cylinder.Z)

$R$ is the radius of the cylinder.

$I$ is the intersection with this slice.

$\alpha$ is the angle between RD and CP.

$T$ is a special point that makes the right triangle TCP.

{{< format/text-align-justify >}}
Finding the length of PI will give us the point I ($I=P+\hat{RD}\cdot||PI||$). However, we don't know that distance yet! Note that $||IP|| = ||TP|| - ||TI||$, so if we can figure out this two lenghts, we can solve point $I$!
First I want to find $||TC||$ as this is a common side of the two right triangles: $TCP$ and $TCI$. There are two methods of figuring it out:
{{</ format/text-align-justify >}}

*Method A:*

$c = \hat{RD} \cdot \hat{CP}$

$\alpha = \arccos c$

$\sin \alpha = \frac{||TC||}{||CP||}$

$||TC|| = \sin \alpha \cdot ||CP||$


*Method B:*

$||TP|| = CP \cdot \hat{RD}$

$||CP||^2 = ||TC||^2 + ||TP||^2$

$||TC|| = \sqrt{||CP||^2 - ||TP||^2}$


** Note: this is done in cylinder local space



<div id="threeCanvas" style ="background-color:#FFF; width:720; height:480px; margin:0 auto;">
<script src="/js/three.js"></script>
<script>
	var container = document.getElementById("threeCanvas");
	var width = container.offsetWidth;
	var height = container.offsetHeight;

	var renderer = new THREE.WebGLRenderer({ antialias: true });
	renderer.setSize( width, height );
	container.appendChild( renderer.domElement );

	var scene = null;
	var camera = null;

	init();
	render();

	//----------------------------
	
	function init()
	{
		scene = new THREE.Scene();

		// Camera setup
		camera = new THREE.PerspectiveCamera(70,width/height,0.1,1000);
		camera.position.z = 0;
		camera.position.y = 2;
		camera.position.x = 5;
		camera.lookAt(0,0,0);

		// Helpers
		var axisHelper = new THREE.AxesHelper(2);
		scene.add(axisHelper);
		var gridHelper = new THREE.GridHelper(10,10);
		scene.add(gridHelper);

		var solidMaterial = new THREE.MeshBasicMaterial({color:0xffffff});
	}

	function render()
	{
		requestAnimationFrame(render);
		renderer.render( scene, camera );
	}
</script>
</div>


