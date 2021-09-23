<template>
<div>
  <div>
    <input type="color" v-model="setcolor">
    <button @click="changebutton">Update</button>
  </div>
  
  <div id="container"></div>
</div>
  
</template>
<style scoped>
#container {
  width: 100%;
  height: 90vh;
  background-color: blue;
}
</style>
<script>
import * as Three from 'three'
// import { STLLoader } from 'three/examples/jsm/loaders/STLLoader.js';
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader.js'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  data() {
    return {
      loader: null,
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      cameraTarget: null,
      controls: null,
      setcolor: null,
    }
  },
  methods: {
    init: function() {
        let self = this;
        let container = document.getElementById('container');

        this.camera = new Three.PerspectiveCamera(45, container.clientWidth/container.clientHeight, 1, 3000);
        // this.camera.position.z = 1;
        this.camera.position.set( 10, 10, 10 );

				// this.cameraTarget = new Three.Vector3( 0, - 0.25, 0 );

        this.scene = new Three.Scene();

        const ambientLight = new Three.AmbientLight( 0xcccccc, 0.4 );
				this.scene.add( ambientLight );

				const pointLight = new Three.PointLight( 0xffffff, 0.8 );
				this.camera.add( pointLight );
				// scene.add( camera );
        this.scene.background = new Three.Color(0xa0a0a0);
        // this.scene.fog = new Three.Fog(0xa0a0a0, 200, 1000);
        // const texture = new Three.TextureLoader().load("textire/grid_25.jpg");
        // const material = new Three.MeshPhongMaterial( { color: 0xff0033, specular: 0x111111, shininess: 200 } );
      
        // let materialColors;
        new RGBELoader()
					.load( 'royal_esplanade_1k.hdr', function ( texture ) {

						texture.mapping = Three.EquirectangularReflectionMapping;

						self.scene.background = texture;
						self.scene.environment = texture;

						self.render();

						// model

						const loader = new GLTFLoader().setPath('products/');
						loader.load( 'New.gltf', function ( gltf ) {
              gltf.scene.position.set(0, -2, 0 );
							gltf.scene.scale.set( 20.0, 20.0, 20.0 );
              gltf.scene.rotation.set( - Math.PI / 2, Math.PI / 2, Math.PI / 2 );
							self.scene.add( gltf.scene );

              self.mesh = gltf.scene;
            } );

					} );

        
        // this.scene.add( new Three.HemisphereLight( 0x443333, 0x111122 ) );

        // let geometry = new Three.BoxGeometry(0.2, 0.2, 0.2);
        // let material = new Three.MeshNormalMaterial();

        // this.mesh = new Three.Mesh(geometry, material);
        // this.scene.add(this.mesh);
        // this.addShadowedLight( 1, 1, 1, 0xffffff, 1.35 );
        // this.addShadowedLight( -1, -1, 1, 0xffaa00, 1 );
        // this.addShadowedLight( -1, -1, -1, 0xffaa00, 1 );
        // this.addShadowedLight( -1, 1, -1, 0xffaa00, 1 );
        // this.addShadowedLight( 1, -1, -1, 0xffaa00, 1 );
				// this.addShadowedLight( 0.5, 1, - 1, 0xffaa00, 1 );
        // this.addShadowedLight( 0.5, -1, 1, 0xffaa00, 1 );
        // this.addShadowedLight( -1, -1, -1, 0xffaa00, 1 );


        this.renderer = new Three.WebGLRenderer({antialias: true});
        this.renderer.setSize(container.clientWidth, container.clientHeight);
        console.log('widthxheight =>',  container.clientWidth +'+'+ container.clientHeight)
        container.appendChild(this.renderer.domElement);

        this.controls = new OrbitControls( this.camera, this.renderer.domElement );
				this.controls.target.set( 0, 0, 0 );
				this.controls.update();


    },
    changebutton() {
      console.log(this.setcolor.toString(16));
      const len = this.mesh.children[0].children[0].children[0].children.length;
      console.log(this.mesh.children[0].children[0].children);
      for(let i=0; i < len; i++){
        // console.log('checker+'+i, this.mesh.children[0].children[0].children[0].children[i].material.color);
        // console.log(this.mesh.children[0].children[0].children[0].children[i]);
        if(this.mesh.children[0].children[0].children[0].children[i].name == "Part1_2" || this.mesh.children[0].children[0].children[0].children[i].name == "Part1_5" || this.mesh.children[0].children[0].children[0].children[i].name == "Part1_7"){
          this.mesh.children[0].children[0].children[0].children[i].material.color.setStyle(this.setcolor);
        }
      }
    },
    render() {
      this.renderer.render(this.scene, this.camera);
    },

    addShadowedLight: function( x, y, z, color, intensity ) {

				const directionalLight = new Three.DirectionalLight( color, intensity );
				directionalLight.position.set( x, y, z );
				this.scene.add( directionalLight );

				directionalLight.castShadow = true;

				const d = 1;
				directionalLight.shadow.camera.left = - d;
				directionalLight.shadow.camera.right = d;
				directionalLight.shadow.camera.top = d;
				directionalLight.shadow.camera.bottom = - d;

				directionalLight.shadow.camera.near = 1;
				directionalLight.shadow.camera.far = 4;

				directionalLight.shadow.bias = - 0.002;

			},
    animate: function() {
        requestAnimationFrame(this.animate);
        // const timer = Date.now() * 0.0005;

				// this.camera.position.x = Math.cos( timer ) * 3;
				// this.camera.position.z = Math.sin( timer ) * 3;
        // this.camera.lookAt( this.cameraTarget );
        this.renderer.render(this.scene, this.camera);
    }
  },
  mounted() {
      this.init();
      this.animate();
  }
}
</script>



