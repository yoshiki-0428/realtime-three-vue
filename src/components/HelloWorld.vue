<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div id="stage">
    </div>
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
      mixer: {},
      lastTime: 0,
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

    const tracks = [];
    // const x = this.mesh.position.x;
    tracks.push({ name: `${this.mesh.uuid}.rotation[x]`,
      type: 'vector',
      times: [...Array(100).keys()],
      values: [...Array(100).keys()]
    });
    tracks.push({ name: `${this.mesh.uuid}.rotation[y]`,
      type: 'vector',
      times: [...Array(100).keys()],
      values: [...Array(100).keys()]
    });

        // new THREE.KeyframeTrack(`${this.mesh.uuid}.rotation[x]`, [...Array(10).keys()],
        // [...Array(10).keys()]));
    // tracks.push(new THREE.KeyframeTrack(`${this.mesh.uuid}.rotation[y]`, [...Array(10).keys()],
    //     [...Array(10).keys()]));

    // === sceneにmodel,light, cameraを追加 ===
    this.scene.add(this.camera);
    this.scene.add(this.light);
    this.scene.add(this.mesh);

    // animation
    const clip = new THREE.AnimationClip.parse({ name: "aaa", duration: 10000, tracks: tracks });
    console.log('clip', clip);
    this.mixer = new THREE.AnimationMixer(this.scene);
    const action = this.mixer.clipAction(clip);
    action.timeScale = 1;
    action.time = 0;
    action.setLoop(THREE.LoopRepeat, 10);
    action.play();
    this.animate();
  },
  mounted() {
    const stage = document.getElementById("stage");
    stage.appendChild(this.renderer.domElement)
  },
  methods: {
    animate () {
      // this.mesh.rotation.x += 0.01;
      // this.mesh.rotation.z += 0.01;
      this.mixer.update(0.2);

      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    }
  }
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
