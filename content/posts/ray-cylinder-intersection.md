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

![](../../images/RayVSCylinder/fig1.png "Figure 1")

{{< format/text-align-justify >}}
First, I will simplify the problem by flatening the ray (so effectively this becomes a 2D problem). You can understand this as trying to figure out the intersection point between the ray and a slice of the cylinder.
{{</ format/text-align-justify >}}

![](../../images/RayVSCylinder/fig2.png "Figure 2")

$C$ is the slice center: $(Cylinder_x, P_y, Cylinder_z)$

$R$ is the radius of the cylinder.

$I$ is the intersection with this slice.

$\alpha$ is the angle between $RD$ and $CP$.

$T$ is a special point that makes the right triangles $TCP$ and $TCI$.

{{< format/text-align-justify >}}
Finding the length of $PI$ will give us the point I ($I=P+\hat{RD}\cdot||PI||$). However, we don't know that distance yet! Note that $||IP|| = ||TP|| - ||TI||$, so if we can figure out this two lenghts, we can solve point $I$!
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

{{< format/text-align-justify >}}
Method A is quite simple to understand, we just use simple trig to find the angle and then resolve for $||TC||$. Method B takes advantage of vector projection to figure out $||TP||$ and then we solve for $||TC||$ with the pythagoras theorem. I will use Method B as we will re-use $||TP||$. Finally to get $||TI||$ we can do:
{{</ format/text-align-justify >}}

$R^2 = ||TC||^2 + ||TI||^2$

$||TI|| = \sqrt{R^2 - ||TC||^2}$


With this information, we can go back to the original representation of the problem:


![](../../images/RayVSCylinder/fig3.png "Figure 3")

We can represent the distance $||P'P||$ as:

$||IP|| = ||TP|| - ||TI||$

$\cos \beta == \hat{RD} \cdot \hat{CP}$

$\cos \beta = \frac{||IP||}{||P'P||}$

$||P'P|| = \frac{||IP||}{\cos \beta}$

$P' = P + \hat{RD} \cdot ||P'P||$

And there we have it! An intersection point... Not really. Before wrapping this up we need to check for rays missing the cylinder and also take into account the cylinder caps. The first thing to note, is that all of this must be done in cylinder local space (or some assumptions won't work). We can start by discardind rays when $||TC|| > R$. 

Before checking for the cylinder height, we are going to limit the ray direction y to be positve. 

We have two cases:

1) $P_y > \frac{H}{2}$ AND $\hat{RD}_y >= 0$ discard the ray.

2) $P_y > \frac{H}{2}$ AND $\hat{RD}_y < 0$ we can't discard the ray, as it may be intersecting the cylinder cap.


![](../../images/RayVSCylinder/fig4.png "Figure 4")

$ \cos \omega = \hat{RD} \cdot -Y$

$ \cos \omega = \frac{H}{D}$

$ D = \frac{H}{\cos \omega}$

$ P' = P + \hat{RD} \cdot D$


<div id="threeCanvas" style ="background-color:#FFF; width:720; height:480px; margin:0 auto;">
<script src="/js/three.js"></script>
<script src="/js/OrbitControls.js"></script>
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

		var controls = new THREE.OrbitControls( camera, renderer.domElement );

		// Helpers
		var axisHelper = new THREE.AxesHelper(2);
		scene.add(axisHelper);
		var gridHelper = new THREE.GridHelper(10,10);
		scene.add(gridHelper);

		// Materials
		var solidMaterial = new THREE.MeshBasicMaterial({color:0xffffff});
		var lambertMaterial = new THREE.MeshLambertMaterial({color : 0xffffff});

		// Cylinder
		var cylinderRadius = 1.0;
		var cylinderHeight = 3.0;
		var cylinderGeometry = new THREE.CylinderGeometry(cylinderRadius, cylinderRadius ,cylinderHeight, 32);
		var cylinder = new THREE.Mesh(cylinderGeometry, lambertMaterial);
		scene.add(cylinder);

		// Light
		var sun = new THREE.DirectionalLight(0xffffff,1);
		scene.add(sun);
		sun.position.x = 2;
		sun.position.z = 2;
	}

	function render()
	{
		requestAnimationFrame(render);
		renderer.render( scene, camera );
	}
</script>
</div>


