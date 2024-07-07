<!-- src/routes/CabinetDesigner.svelte -->
<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';

	let cabinetWidth = 10;
	let cabinetHeight = 10;
	let defaultDrawerSize = 5;
  let drawerWidth = defaultDrawerSize;
  let drawerHeight = defaultDrawerSize;
	let cabinet,
		scene,
		camera,
		renderer,
		drawers = [];

	const initThree = () => {
		scene = new THREE.Scene();
		camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
		renderer = new THREE.WebGLRenderer();
		renderer.setSize(window.innerWidth, window.innerHeight);
		document.body.appendChild(renderer.domElement);

		const light = new THREE.AmbientLight(0x404040);
		scene.add(light);

		const pointLight = new THREE.PointLight(0xffffff, 1, 100);
		pointLight.position.set(50, 50, 50);
		scene.add(pointLight);

		camera.position.z = 30;

		updateCabinet();
		animate();
	};

	const createCabinet = (width, height) => {
		const geometry = new THREE.BoxGeometry(width, height, 1);
		const material = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true });
		return new THREE.Mesh(geometry, material);
	};

	const createDrawer = (x, y, width, height) => {
		const geometry = new THREE.BoxGeometry(width, height, 1);
		const material = new THREE.MeshBasicMaterial({ color: 0x0000ff, wireframe: true });
		const drawer = new THREE.Mesh(geometry, material);
		drawer.position.set(x, y, 0);
		return drawer;
	};

	const updateCabinet = () => {
		if (cabinet) scene.remove(cabinet);
		drawers.forEach((drawer) => scene.remove(drawer));
		drawers = [];

		cabinet = createCabinet(cabinetWidth, cabinetHeight);
		scene.add(cabinet);

		const cols = Math.ceil(cabinetWidth / defaultDrawerSize);
		const rows = Math.ceil(cabinetHeight / defaultDrawerSize);

		drawerWidth = cabinetWidth / cols;
		drawerHeight = cabinetHeight / rows;

		for (let i = 0; i < cols; i++) {
			for (let j = 0; j < rows; j++) {
				const x = -cabinetWidth / 2 + drawerWidth / 2 + i * drawerWidth;
				const y = -cabinetHeight / 2 + drawerHeight / 2 + j * drawerHeight;
				const drawer = createDrawer(x, y, drawerWidth, drawerHeight);
				drawers.push(drawer);
				scene.add(drawer);
			}
		}
	};

	const animate = () => {
		requestAnimationFrame(animate);
		renderer.render(scene, camera);
	};

	const handleWidthChange = (event) => {
		cabinetWidth = parseInt(event.target.value);
		updateCabinet();
	};

	const handleHeightChange = (event) => {
		cabinetHeight = parseInt(event.target.value);
		updateCabinet();
	};

	onMount(initThree);
</script>

<div class="controls">
	<label for="width">Cabinet Width:</label>
	<input type="range" id="width" min="5" max="20" value="10" on:input={handleWidthChange} />
	<label for="height">Cabinet Height:</label>
	<input type="range" id="height" min="5" max="20" value="10" on:input={handleHeightChange} />
</div>

<style>
	canvas {
		display: block;
	}
	.controls {
		position: absolute;
		top: 10px;
		left: 10px;
		z-index: 10;
	}
</style>
