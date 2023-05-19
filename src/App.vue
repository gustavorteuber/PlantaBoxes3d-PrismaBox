<template>
  <div ref="container"></div>
</template>

<script>
import * as THREE from "three";
import DragControls from 'drag-controls'
DragControls.install({THREE: THREE})

export default {
  
  mounted() {
    this.scene = new THREE.Scene();    
    this.scene.background = new THREE.Color(0xf0f0f0);

    var objects = [];

    const geometry = new THREE.BoxGeometry(10, 1.0, 1.0);
    var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
    const mesh = new THREE.Mesh(geometry, material);
    mesh.position.x = 1;
    mesh.position.y = 3;
    mesh.position.z = 1;
    mesh.castShadow = true;
    mesh.receiveShadow = true;
    this.scene.add(mesh);
    objects.push(mesh);
    
    //LIGHT
      const skyColor = 0xB1E1FF;  // light blue
      const groundColor = 0xB97A20;  // brownish orange
      const intensity = 1.2;
      const light = new THREE.HemisphereLight(skyColor, groundColor, intensity);
      this.scene.add(light);

    this.camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    this.camera.position.z = 10;

    this.renderer = new THREE.WebGLRenderer({ antialias: true });
    this.renderer.setSize(window.innerWidth, window.innerHeight);

    this.$refs.container.appendChild(this.renderer.domElement);

    this.createAxes();

    this.createGrid();

    this.addMouseListeners();

    this.addMouseWheelListener();

    this.animate();
    
    //Controles
    var controls = new DragControls(objects, this.camera, this.renderer.domElement);

    // Variável para controlar se a tecla Shift está pressionada
    this.isShiftPressed = false;

    // Evento de pressionar e soltar a tecla Shift
    document.addEventListener('keydown', (event) => {
      if (event.key === 'Shift') {
        this.isShiftPressed = true;
      }
    });
    document.addEventListener('keyup', (event) => {
      if (event.key === 'Shift') {
        this.isShiftPressed = false;
      }
    });
  
  },
  
  methods: {
    createAxes() {
      const axesHelper = new THREE.AxesHelper(5);
      this.scene.add(axesHelper);
    },
    createGrid() {
      const gridSize = 40;
      const divisions = 40;

      const gridMaterial = new THREE.LineBasicMaterial({ color: 0x888888 });

      const gridHelper = new THREE.GridHelper(
        gridSize,
        divisions,
        gridMaterial,
        gridMaterial
      );
      this.scene.add(gridHelper);
    },
    addMouseListeners() {
      this.mouse = new THREE.Vector2();
      this.previousMouse = new THREE.Vector2();
      this.isMouseDown = false;

      document.addEventListener("mousedown", this.onMouseDown);
      document.addEventListener("mousemove", this.onMouseMove);
      document.addEventListener("mouseup", this.onMouseUp);
    },
    onMouseDown(event) {
      event.preventDefault();
      this.isMouseDown = true;
      this.previousMouse.set(event.clientX, event.clientY);
    },
    onMouseMove(event) {
      event.preventDefault();
      if (this.isMouseDown) {
        this.mouse.set(event.clientX, event.clientY);
        const delta = this.mouse.clone().sub(this.previousMouse);
        if (this.isShiftPressed) {
          // Mover a câmera
          this.scene.rotation.x += delta.y * 0.01;
          this.scene.rotation.y += delta.x * 0.01;
        } else {
          
        }
        this.previousMouse.set(event.clientX, event.clientY);
      }
    },
    onMouseUp(event) {
      event.preventDefault();
      this.isMouseDown = false;
    },
    addMouseWheelListener() {
      this.$refs.container.addEventListener("wheel", this.onMouseWheel);
    },
    onMouseWheel(event) {
      event.preventDefault();
      const delta = event.deltaY * 0.1;
      this.camera.position.z += delta;
      this.camera.position.z = Math.max(
        2,
        Math.min(50, this.camera.position.z)
      );
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style>
div {
  width: 100%;
  height: 100%;
}
</style>
