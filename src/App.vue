<template>
  <div ref="container"></div>
</template>

<script>
export default {
  mounted() {
    // Cria a cena
    this.scene = new THREE.Scene();

    // Cria a câmera
    this.camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    this.camera.position.z = 10;

    // Cria o renderizador
    this.renderer = new THREE.WebGLRenderer({ antialias: true });
    this.renderer.setSize(window.innerWidth, window.innerHeight);

    // Adiciona o renderizador ao elemento do Vue
    this.$refs.container.appendChild(this.renderer.domElement);

    // Cria os eixos
    this.createAxes();

    // Cria o chão quadriculado
    this.createGrid();

    // Adiciona eventos de interação do mouse
    this.addMouseListeners();

    // Renderiza a cena
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

      // Cria o material do chão quadriculado
      const gridMaterial = new THREE.LineBasicMaterial({ color: 0x888888 });

      // Cria o chão quadriculado
      const gridHelper = new THREE.GridHelper(
        gridSize,
        divisions,
        gridMaterial,
        gridMaterial
      );
      this.scene.add(gridHelper);
    },
    addMouseListeners() {
      // Captura a posição inicial do mouse
      this.mouse = new THREE.Vector2();
      this.previousMouse = new THREE.Vector2();
      this.isMouseDown = false;

      // Adiciona os eventos de interação do mouse
      document.addEventListener("mousedown", this.onMouseDown);
      document.addEventListener("mousemove", this.onMouseMove);
      document.addEventListener("mouseup", this.onMouseUp);
    },
    onMouseDown(event) {
      event.preventDefault();
      this.isMouseDown = true;

      // Atualiza a posição inicial do mouse
      this.previousMouse.set(event.clientX, event.clientY);
    },
    onMouseMove(event) {
      event.preventDefault();
      if (this.isMouseDown) {
        // Calcula o movimento do mouse
        this.mouse.set(event.clientX, event.clientY);
        const delta = this.mouse.clone().sub(this.previousMouse);

        // Atualiza a rotação da cena com base no movimento do mouse
        this.scene.rotation.x += delta.y * 0.01;
        this.scene.rotation.y += delta.x * 0.01;

        // Atualiza a posição anterior do mouse
        this.previousMouse.set(event.clientX, event.clientY);
      }
    },
    onMouseUp(event) {
      event.preventDefault();
      this.isMouseDown = false;
    },
    animate() {
      requestAnimationFrame(this.animate);

      // Renderiza a cena com a câmera
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
