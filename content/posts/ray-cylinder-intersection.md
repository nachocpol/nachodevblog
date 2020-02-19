+++ 
draft = false
date = 2020-02-19T20:57:45Z
title = "Ray-Cylinder intersection"
description = ""
slug = "" 
tags = ["math","algebra"]
+++

{{< format/text-align-justify >}}
For a given cylinder with radius $R$ and height $H$, a ray with origin $P$ and direction $RD$, find the first intersection point $P'$ between the ray and the cylinder.
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
Finding the length of $PI$ will give us the point I ($I=P+\hat{RD}\cdot||PI||$). However, we don't know that distance yet! Note that $||IP|| = ||TP|| - ||TI||$, so if we can figure out this two lengths, we can solve point $I$!
First I want to find $||TC||$ as this is a common side of the two right triangles: $TCP$ and $TCI$. There are two methods of figuring it out:
{{</ format/text-align-justify >}}

*Method A:*

$RD_f = (RD_x,0,RD_z)$

$c = \hat{RD_f} \cdot \hat{CP}$

$\alpha = \arccos c$

$\sin \alpha = \frac{||TC||}{||CP||}$

$||TC|| = \sin \alpha \cdot ||CP||$


*Method B:*

$||TP|| = CP \cdot \hat{RD_f}$

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

And there we have it! An intersection point... Not really. Before wrapping this up we need to check for rays missing the cylinder and also take into account the cylinder caps. The first thing to note is that all of this must be done in cylinder local space (or some assumptions won't work). We can start by discarding rays when $||TC|| > R$. 

Before checking for the cylinder height, we are going to limit the ray direction y to be positve:

![](../../images/RayVSCylinder/fig4.png "Figure 4")

$|{P'}_y| > \frac{H}{2}$

$ A = |P_y| - \frac{H}{2}$

$Y_d = (0,-1,0)  RD_y	\leqslant 0$

$Y_d = (0,1,0)  RD_y	> 0$

$ \cos \omega = \hat{RD} \cdot Y_d$

$ \cos \omega = \frac{H}{D}$

$ D = \frac{H}{\cos \omega}$

$ P' = P + \hat{RD} \cdot D$

Finally, we need to check that $P'$ is within the cap:

$\sqrt{{P'}_x^2 + {P'}_z^2} > R \Rightarrow discard.$


And now we have an intersection point that takes into account the caps. I implemented the intersection code bellow using three.js:

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
		var camControls = new THREE.OrbitControls(camera, renderer.domElement);
		camControls.enablePan = false;

		// Helpers
		var axisHelper = new THREE.AxesHelper(5);
		axisHelper.position.y = 0.005;
		scene.add(axisHelper);
		var gridHelper = new THREE.GridHelper(10,16,0xf0f0f0,0x606060);
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
		var sun = new THREE.DirectionalLight(0xffffff,0.5);
		scene.add(sun);
		sun.position.x = 2;
		sun.position.z = 2;

		var light = new THREE.AmbientLight( 0x202020 );
		scene.add( light );

		
		drawIntersection(
			new THREE.Vector3(0.3,3,2),
			new THREE.Vector3(-0.3,-0.8,-1),
			cylinderRadius,
			cylinderHeight
		);

		drawIntersection(
			new THREE.Vector3(0.3,1.5,3),
			new THREE.Vector3(0.2,-0.8,-1),
			cylinderRadius,
			cylinderHeight
		);

		drawIntersection(
			new THREE.Vector3(0,-2,-3),
			new THREE.Vector3(0.2,0.8,1),
			cylinderRadius,
			cylinderHeight
		);

		drawIntersection(
			new THREE.Vector3(2,-2.5,0),
			new THREE.Vector3(-1,0.7,0),
			cylinderRadius,
			cylinderHeight
		);

		drawIntersection(
			new THREE.Vector3(-2.5,0.5,0.2),
			new THREE.Vector3(1,0.4,0),
			cylinderRadius,
			cylinderHeight
		);
	}

	function drawIntersection(P, RD, Radius, Height)
	{
		RD.normalize();
		var arrow = new THREE.ArrowHelper(RD,P,1,0x00ffff);
		scene.add(arrow);

		var RD2D = new THREE.Vector3();
		RD2D.copy(RD);
		RD2D.y = 0;
		RD2D.normalize();

		var C = new THREE.Vector3(0,P.y,0);
		var CP = new THREE.Vector3();
		CP.subVectors(C,P);

		var CPLen = CP.length();
		var TP = CP.dot(RD2D);
		var TC = Math.sqrt(CPLen*CPLen - TP*TP);

		// Discard ray.
		if(TC > Radius)
		{
			return;
		}

		var TI = Math.sqrt(Math.max(Radius*Radius - TC*TC, 0));
		var IP = TP - TI;

		// Debug 2D tests:
		if(false)
		{
			var debugRay = new THREE.ArrowHelper(RD2D,P,IP,0xff0000);
			scene.add(debugRay);

			var Ipo = new THREE.Vector3();
			Ipo.copy(P);
			Ipo.addScaledVector(RD2D,IP);
			console.log(Ipo);
			console.log(Math.sqrt(Ipo.x*Ipo.x + Ipo.z*Ipo.z));
		}

		var CPnorm = new THREE.Vector3();
		CPnorm.copy(CP);
		CPnorm.normalize();
		var cosB = RD.dot(CPnorm);
		var PpP = IP / cosB;

		var Pinter = new THREE.Vector3();
		Pinter.copy(P);
		Pinter.addScaledVector(RD,PpP);
		
		// Finally, we need to check for the cylinder height:
		Pinter.y = Math.abs(Pinter.y); // Flip point y

		var halfCylinderHeight = Height * 0.5;
		if(Pinter.y > halfCylinderHeight)
		{
			// Check if we hit the cylinder cap:
			var YDir = new THREE.Vector3(0,-1,0);
			if(RD.y > 0)
			{
				YDir = new THREE.Vector3(0,1,0);
			}
			var H = Math.abs(P.y) - halfCylinderHeight; // Flip y!
			var cosw = RD.dot(YDir);
			var D = H / cosw;

			var Pcap = new THREE.Vector3();
			Pcap.copy(P); // we now copy the original point
			Pcap.addScaledVector(RD,D);// Also use original dir
			var rad = Math.sqrt(Pcap.x*Pcap.x + Pcap.z*Pcap.z);
			if(rad > Radius)
			{
				// Discard, ray misses the cap
				return;
			}
				
			var interArrow = new THREE.ArrowHelper(RD,P,D,0x00ff00);
			scene.add(interArrow);
		}
		else
		{
			var interArrow = new THREE.ArrowHelper(RD,P,PpP,0x00ff00);
			scene.add(interArrow);
		}

		/*
		console.log(C);
		console.log(CP);
		console.log(TP);
		console.log(TC);
		console.log(TI);
		console.log(cosB);
		console.log(PpP);
		*/
	}

	function render()
	{
		requestAnimationFrame(render);
		renderer.render( scene, camera );
	}
</script>
</div>

## Appendix:
In this implementation I'm not taking into account rays starting inside the cylinder. Also, this could be extended to provide the second intersection point.


