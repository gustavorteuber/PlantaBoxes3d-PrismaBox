<template>
  <div ref="container"></div>
</template>

<script>
export default {
  mounted() {
    this.scene = new THREE.Scene();

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
        this.scene.rotation.x += delta.y * 0.01;
        this.scene.rotation.y += delta.x * 0.01;
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
