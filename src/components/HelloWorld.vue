<template>
  <div class="hello">
    <div id="stage">
    </div>
    <button @click="interver(move)">↓</button>
    <button @click="interver(moveRight)">→</button>
  </div>
</template>

<script>
import * as THREE from 'three';

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      scene: this.scene,
      renderer: this.renderer,
      camera: this.camera,
      light: this.light,
      geometry: this.geometry,
      material: this.material,
      mesh: this.mesh,
      mixer: undefined,
      action: {},
      lastTime: 0,
      keyFlame: {
        x: [0],
        y: [0]
      }
    }
  },
  created() {
    // === scene ===
    this.scene = new THREE.Scene();
    // === renderer ===
    this.renderer = new THREE.WebGLRenderer({alpha: true});
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    // === camera ===
    this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / (window.innerHeight), 1, 1000);
    this.camera.position.set(0, 0, 15);
    this.camera.lookAt(new THREE.Vector3(0, 0, 0));
    // === light ===
    this.light = new THREE.DirectionalLight(0xffffff);
    this.light.position.set(0, 0, 10);
    // === model ===
    const cubeSize = 2.5;
    this.geometry = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
    this.material = new THREE.MeshNormalMaterial();
    this.mesh = new THREE.Mesh(new THREE.CubeGeometry(5, 5, 5), this.material);

    // === sceneにmodel,light, cameraを追加 ===
    this.scene.add(this.camera);
    this.scene.add(this.light);
    this.scene.add(this.mesh);
    this.mixer = new THREE.AnimationMixer(this.scene);
    this.animate();
  },
  mounted() {
    const stage = document.getElementById("stage");
    stage.appendChild(this.renderer.domElement)
  },
  methods: {
    animate () {
      if (this.mixer) {
        this.mixer.update(0.1);
      }
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },
    move() {
      const tracks = [];
      this.keyFlame.x.push(Math.max(...this.keyFlame.x) + 1);
      tracks.push({ name: `${this.mesh.uuid}.rotation[x]`,
        type: 'vector',
        times: [...Array(this.keyFlame.x.length).keys()],
        values: this.keyFlame.x
      });
      const clip = new THREE.AnimationClip.parse({ name: "hoge", duration: 5000, tracks: tracks });
      this.action = this.mixer.clipAction(clip);
      this.action.time = this.mixer.time;
      console.log(this.mixer.time)
      this.action.setLoop(THREE.LoopOnce, 1);
      this.action.play();
    },
    moveRight() {
      const tracks = [];
      this.keyFlame.y.push(Math.max(...this.keyFlame.y) + 1);
      tracks.push({ name: `${this.mesh.uuid}.rotation[y]`,
        type: 'vector',
        times: [...Array(this.keyFlame.y.length).keys()],
        values: this.keyFlame.y
      });
      const clip = new THREE.AnimationClip.parse({ name: "hoge right", duration: 5000, tracks: tracks });
      this.action = this.mixer.clipAction(clip);
      this.action.time = this.mixer.time;
      console.log(this.mixer.time)
      this.action.setLoop(THREE.LoopOnce, 1);
      this.action.play();
    },
    interver(func) {
      setInterval(func, 500);
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
