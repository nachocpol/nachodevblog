+++ 
draft = false
date = 2020-05-16T23:06:15+01:00
title = "Hack Series nose cone generator and exporter"
description = "Generator that allows you to customize the nose cone settings and then export it as an OBJ"
slug = "" 
tags = ["rocketry"]
categories = []
externalLink = ""
series = []
+++

After the initial research generating the <a href="{{< relref "Haack-series-nose-generator.md" >}}" target="_blank">2D Haack nose cone</a>, I finished the full 3D generator. This tool allows you to tweak and export the nose cone as an OBJ. The generator is unitless! So just be consistent with your CAD program units.


### Settings

<div>
	<table>
	<tr>
		<td>
			<label>Haack Param </label>
		</td>
		<td>
			<input type="range" min="0.0" max="0.66" value="0.33" step="0.01" id="haackParamSlider" oninput="previewParam.value = haackParamSlider.value;">
			<output id="previewParam">0.33</output>
		</td>
	</tr>
	
	<tr>
		<td>
			<label>Nose Length</label>
		</td>
		<td>
			<input type="number" min="0.0" max="100.0" value="10.0" step="0.001" id="haackNoseLength">
		</td>
	</tr>

	<tr>
		<td>
			<label>Nose Radius</label>
		</td>
		<td>
			<input type="number" min="0" max="100.0" value="2.0" step="0.001" id="haackNoseRadius">
		</td>
	</tr>

	<tr>
		<td>
			<label>Number of Segments(vertical divisions) </label>
		</td>
		<td>
			<input type="range" min="2" max="256" value="256" step="1" id="haackNumerSegments" oninput="previewNumerSegments.value = haackNumerSegments.value;">
			<output id="previewNumerSegments">256</output>
		</td>
	</tr>

	<tr>
		<td>
			<label>Radii(horizontal divisions) </label>
		</td>
		<td>
			<input type="range" min="3" max="64" value="64" step="1" id="haackRadii" oninput="previewRadii.value = haackRadii.value;">
			<output id="previewRadii">64</output>
		</td>
	</tr>

	<tr>
		<td>
			Generate shoulder
		</td>
		<td>
			<input type="checkbox" id="haackGenShoulder">
		</td>
	</tr>

	<tr>
		<td>
			<label>Shoulder Length </label>
		</td>
		<td>
			<input type="number" min="0" max="100.0" value="1.0" step="0.001" id="haackShoulderLength">
		</td>
	</tr>

	<tr>
		<td>
			<label>Shoulder Radius </label>
		</td>
		<td>
			<input type="number" min="0" max="100.0" value="1.9" step="0.001" id="haackShoulderRadius">
		</td>
	</tr>
	
	<tr>
		<td>
			<input type="button" onclick="haackRefreshValues();haackUpdate();" value="Update">
			<input type="button" onclick="exportOBJ();" value="Export .OBJ">
		</td>
		<td>
			
		</td>
	</tr>
	</table>
	<br><br>
</div>

<div id="threeCanvas" style ="background-color:#FFF; width:720; height:480px; margin:0 auto;">
<script src="/js/three.js"></script>
<script src="/js/OrbitControls.js"></script>
<script>
	var container = document.getElementById('threeCanvas');
	var width = container.offsetWidth;
	var height = container.offsetHeight;
	var renderer = new THREE.WebGLRenderer({ antialias: true });
	renderer.setSize( width, height );
	container.appendChild( renderer.domElement );

	var scene = new THREE.Scene();
	var camera = new THREE.PerspectiveCamera( 90, width / height, 0.1, 100 );
	camera.position.set(0,5,5);
	var controls = new THREE.OrbitControls( camera, renderer.domElement);
	controls.update();
	controls.enablePan = false;
	controls.minDistance = 5.0;
	controls.maxDistance = 100.0;

	var axesHelper = new THREE.AxesHelper( 25 );
	axesHelper.position.y = 0.05;
	scene.add(axesHelper);

	var gridHelper = new THREE.GridHelper( 50, 10 );
	scene.add(gridHelper);

	var whiteMaterial = new THREE.MeshBasicMaterial({color : 0xffffff});
	var redWireMaterial = new THREE.MeshBasicMaterial({color : 0x808080,wireframe: true});

	// Initial values:
	var numSegments = 32; // Vertical segments
	var radii = 3;
	var noseLength = 15.0; //cm
	var noseRadius = 2.1;
	var haackParam = 0.33;
	var addShoulder = false;
	var shoulderRadius = 1.93;
	var shoulderLength = 1.0;

	haackRefreshValues();
	setup();
	render();

	function haackOmega(x,L)
	{
		return Math.acos(1.0 - ((2 * x) / L));
	}

	function haackRadius(R, omega, C)
	{
		var sinO3 = Math.sin(omega) * Math.sin(omega) * Math.sin(omega);
		return (R / (Math.sqrt(Math.PI))) * Math.sqrt(omega - (Math.sin(2*omega) * 0.5) + (C * sinO3));
	}

	function haackRefreshValues()
	{
		// The + thingy is so we  get the actual number (not a string)
		radii = +document.getElementById("haackRadii").value;
		numSegments =+document.getElementById("haackNumerSegments").value;
		noseLength = +document.getElementById("haackNoseLength").value;
		noseRadius = +document.getElementById("haackNoseRadius").value;
		haackParam = +document.getElementById("haackParamSlider").value;
		addShoulder = document.getElementById("haackGenShoulder").checked;
		shoulderRadius = +document.getElementById("haackShoulderRadius").value;
		shoulderLength = +document.getElementById("haackShoulderLength").value;

		controls.target = new THREE.Vector3(0,noseLength * 0.5,0);

		console.log("Current settings:");
		console.log(
			numSegments + ", " + radii + ", " + noseLength + ", " + noseRadius + ", " + haackParam + ", "
			+ addShoulder + ", " + shoulderRadius + ", " + shoulderLength 
		);
	}

	function haackUpdate()
	{
		var oldMesh = scene.getObjectByName("Nose");
		if(oldMesh)
		{
			scene.remove(oldMesh);
		}
		var oldWire = scene.getObjectByName("NoseWire");
		if(oldWire)
		{
			scene.remove(oldWire);
		}

		var sphereGeometry = new THREE.SphereGeometry(0.15,32,32);
		var noseGeometry = new THREE.Geometry();

		var addBottomCap = true;
		var capVertexId = 0;

		var yStep = noseLength / numSegments;
		var curYStepIndex = 0;
		var y = 0.0;

		var angleStep = (Math.PI * 2.0) / radii;
		var angle = 0.0;

		// Build vertex data:
		for(var yIndex = 0.0; yIndex <= numSegments; ++yIndex) // <= because we want to reach top!
		{
			var omega = haackOmega(noseLength - y, noseLength);
			var radius = haackRadius(noseRadius, omega, haackParam);
			for(var angleIndex = 0; angleIndex < radii; ++angleIndex)
			{
				var x = Math.cos(angle) * radius;
				var z = Math.sin(angle) * radius;
				noseGeometry.vertices.push(new THREE.Vector3(x,y,z));

				angle += angleStep;
			}
			y += yStep;
		}
		
		if(addBottomCap)
		{
			capVertexId = noseGeometry.vertices.length;
			noseGeometry.vertices.push(new THREE.Vector3(0,0,0));
		}

		// Build faces
		for(var segment = 0; segment < numSegments; ++segment) // <= because we want to reach top!
		{
			var off = segment * radii;
			for(var rad = 0; rad < radii; ++rad)
			{
				// Face0
				{
					var idx0 = ((rad + 0)  % radii) + off;
					var idx1 = ((rad + 1)  % radii) + off + radii;
					var idx2 = ((rad + 1)  % radii) + off;
					noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
				}
				// Face1
				{
					var idx0 = ((rad + 0)  % radii) + off;
					var idx1 = ((rad + 0)  % radii) + off + radii;
					var idx2 = ((rad + 1)  % radii) + off + radii;
					noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
				}
			}
		}

		// Bottom cap:
		if(addBottomCap)
		{
			for(var rad = 0; rad < radii; ++rad)
			{
				var idx0 = rad;
				var idx1 = (rad + 1) % radii;
				var idx2 = capVertexId;
				noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
			}
		}

		// Shoulder
		var numShoulderRadii = 128;
		if(addShoulder)
		{
			var startVId = noseGeometry.vertices.length;
			var shoulderStep = (Math.PI * 2.0) / numShoulderRadii;
			for(var curCap = 0; curCap != 2; ++curCap)
			{
				var curShoulderAngle = 0;
				var y = (curCap == 0) ? 0.0 : -shoulderLength;
				for(var rad = 0; rad != numShoulderRadii; ++rad)
				{
					var x = Math.cos(curShoulderAngle) * shoulderRadius;
					var z = Math.sin(curShoulderAngle) * shoulderRadius;
					noseGeometry.vertices.push(new THREE.Vector3(x,y,z));
					curShoulderAngle += shoulderStep;
				}
			}

			// Add central point
			var botVId = noseGeometry.vertices.length;
			noseGeometry.vertices.push(new THREE.Vector3(0,-shoulderLength,0));

			// Setup shoulder side faces
			for(var curIndex = 0; curIndex != numShoulderRadii; ++curIndex)
			{
				// Face0
				{
					var idx0 = ((curIndex + 0)  % numShoulderRadii) + startVId;
					var idx1 = ((curIndex + 1)  % numShoulderRadii) + startVId;
					var idx2 = ((curIndex + 1)  % numShoulderRadii) + startVId + numShoulderRadii;
					noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
				}
				// Face1
				{
					var idx0 = ((curIndex + 0)  % numShoulderRadii) + startVId;
					var idx1 = ((curIndex + 1)  % numShoulderRadii) + startVId + numShoulderRadii;
					var idx2 = ((curIndex + 0)  % numShoulderRadii) + startVId + numShoulderRadii;
					noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
				}
			}

			// Add shoulder bottom cap
			for(var curCapIdx = 0; curCapIdx != numShoulderRadii; ++curCapIdx)
			{
				var idx0 = ((curCapIdx + 0) % numShoulderRadii) + startVId + numShoulderRadii;
				var idx1 = ((curCapIdx + 1) % numShoulderRadii) + startVId + numShoulderRadii;
				var idx2 = botVId;
				noseGeometry.faces.push(new THREE.Face3(idx0, idx1, idx2));
			}
		}

		{
			var noseMesh = new THREE.Mesh(noseGeometry, whiteMaterial);
			noseMesh.name = "Nose";
			var noseMeshWire = new THREE.Mesh(noseGeometry, redWireMaterial);
			noseMeshWire.name = "NoseWire";
			scene.add(noseMeshWire);
			scene.add(noseMesh);
		}
	}

	function setup()
	{
		haackUpdate();
	}

	function exportOBJ()
	{
		var nose = scene.getObjectByName("Nose");

		// Parse mesh and export to obj:
		var objText = "# Generated by nachocpol@gmail.com \n"
		for(var v = 0; v != nose.geometry.vertices.length; ++v)
		{
			objText = objText + "v ";
			objText = objText + nose.geometry.vertices[v].x + " ";
			objText = objText + nose.geometry.vertices[v].y + " ";
			objText = objText + nose.geometry.vertices[v].z + "\n";
		}

		for(var f = 0; f != nose.geometry.faces.length; ++f)
		{
			objText = objText + "f ";
			objText = objText +  (nose.geometry.faces[f].a + 1) + " ";
			objText = objText +  (nose.geometry.faces[f].b + 1) + " ";
			objText = objText +  (nose.geometry.faces[f].c + 1) + "\n";
		}

		// Write the file to disk:
		var pom = document.createElement('a');
		pom.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(objText));
		pom.setAttribute('download', "haack-nose.obj");
		pom.style.display = 'none';
		document.body.appendChild(pom);
		pom.click();
		document.body.removeChild(pom);
	}

	function render() 
	{
		requestAnimationFrame(render);

		// Update camera
		controls.update();

		renderer.render(scene,camera);	
	};
</script>
</div>

#### Releases
**17 May 2020**

* Initial release


<br>

To test this, I 3D printed a nose cone using this tool:

![](../../images/Haack/3dprintresult.jpg "Results")


Let me know if you find any issues!
Thanks.