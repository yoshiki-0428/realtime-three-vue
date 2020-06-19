<template>
  <div class="hello">
    <div id="stage">
    </div>
    <button @click="move({ value: Math.min(...keyFlame.x) - 1, part: 'rotation[x]' }, keyFlame.x)">↑</button>
    <button @click="move({ value: Math.max(...keyFlame.x) + 1, part: 'rotation[x]' }, keyFlame.x)">↓</button>
    <button @click="move({ value: Math.max(...keyFlame.y) + 1, part: 'rotation[y]' }, keyFlame.y)">→</button>
    <button @click="move({ value: Math.min(...keyFlame.y) - 1, part: 'rotation[y]' }, keyFlame.y)">←</button>
    <div>
      {{ this.keyFlame.x }}
      {{ this.keyFlame.y }}
    </div>
  </div>
</template>

<script>
import * as THREE from 'three';

export default {
  name: 'HelloWorld',
  data () {
    return {
      scene: this.scene,
      renderer: this.renderer,
      camera: this.camera,
      light: this.light,
      mesh: this.mesh,
      mixer: undefined,
      action: {},
      keyFlame: {
        x: [0],
        y: [0]
      }
    }
  },
  created() {
    this.setUp();
    this.animate();
  },
  mounted() {
    const stage = document.getElementById("stage");
    stage.appendChild(this.renderer.domElement)
  },
  methods: {
    setUp () {
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
      const light = new THREE.DirectionalLight(0xffffff);
      light.position.set(0, 0, 10);
      // === model ===
      const cubeSize = 2.5;
      const material = new THREE.MeshNormalMaterial();
      this.mesh = new THREE.Mesh(new THREE.CubeGeometry(cubeSize, cubeSize, cubeSize), material);

      // === sceneにmodel,light, cameraを追加 ===
      this.scene.add(this.camera);
      this.scene.add(light);
      this.scene.add(this.mesh);
      this.mixer = new THREE.AnimationMixer(this.scene);
    },
    animate () {
      if (this.mixer) {
        this.mixer.update(0.1);
      }
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },
    move(logData, keyFlame) {
      const tracks = [];
      keyFlame.push(logData.value);
      tracks.push({ name: `${this.mesh.uuid}.${logData.part}`,
        type: 'vector',
        times: [...Array(keyFlame.length).keys()],
        values: keyFlame
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
