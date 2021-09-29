<template>
<div>
  <!-- <div>
    <input type="color" v-model="setcolor">
    <button @click="changebutton">Update</button>
  </div> -->
  <div class="products">
    <div v-for="(item, index) in products" :key="index" class="itemImg">
      <img  :src='item.img' @click="onChangeImage(index)" class="productImg" alt="">
    </div>
    
  </div>
  <div class="Previewer">
    <div id="container"></div>
  </div>
</div>
  
</template>
<style scoped>
#container {
  background-color: blue;
  width: 100%;
  height: 100%;
}
#loader{
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
}
.Previewer{
  width: 1200px;
  height: 800px;
}
.products{
  display: flex;
}
.itemImg{
  margin: 10px;
}
.productImg{
  width: 80px;
  height: 80px;
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
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      cameraTarget: null,
      controls: null,
      setcolor: null,
      loader: false,
      products: [
        {name: 'Alcatel_Joy_Tab_2',  img: require('../assets/img/Alcatel_Joy_Tab_2.png')},
        {name: 'Amazon_fire_10', img: require('../assets/img/Amazon_fire_10.png')},
        {name: 'Amazon_fire_10_plus', img: require('../assets/img/Amazon_fire_10_plus.png')},
        {name: 'New', img: require('../assets/img/New.png')},
        {name: 'switch_7', img: require('../assets/img/switch_7.png')},
      ],
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

        // const ambientLight = new Three.AmbientLight( 0xcccccc, 0.4 );
				// this.scene.add( ambientLight );

				// const pointLight = new Three.PointLight( 0xffffff, 0.8 );
				// this.camera.add( pointLight );
				// scene.add( camera );
        // this.scene.background = new Three.Color(0xa0a0a0);
        new RGBELoader()
					.load( 'royal_esplanade_1k.hdr', function ( texture ) {

						texture.mapping = Three.EquirectangularReflectionMapping;

						self.scene.background = texture;
						self.scene.environment = texture;

						self.render();

						// model

						const loader = new GLTFLoader().setPath('products/');
						// loader.load( self.products[0].name+'.gltf', function ( gltf ) {
            loader.load('Amazon_Fire_HD_8.gltf', function ( gltf ) {
              gltf.scene.position.set(0, -2, 0 );
							gltf.scene.scale.set( 20.0, 20.0, 20.0 );
              gltf.scene.rotation.set( - Math.PI / 2, Math.PI / 2, Math.PI / 2 );
							self.scene.add( gltf.scene );

              self.mesh = gltf.scene;
            } );

					} );

        this.renderer = new Three.WebGLRenderer({antialias: true});
        this.renderer.setSize(container.clientWidth, container.clientHeight);
        this.renderer.toneMapping = Three.ACESFilmicToneMapping;
        this.renderer.toneMappingExposure = 1;
        this.renderer.outputEncoding = Three.sRGBEncoding
        console.log('widthxheight =>',  container.clientWidth +'+'+ container.clientHeight)
        container.appendChild(this.renderer.domElement);

        this.controls = new OrbitControls( this.camera, this.renderer.domElement );
				this.controls.target.set( 0, 0, 0 );
				this.controls.update();


    },
    changebutton() {
      console.log(this.setcolor.toString(16));
      const len = this.mesh.children[0].children[0].children[0].children.length;
      console.log(this.mesh);
      for(let i=0; i < len; i++){
        // console.log('checker+'+i, this.mesh.children[0].children[0].children[0].children[i].material.color);
        // console.log(this.mesh.children[0].children[0].children[0].children[i]);
        if(this.mesh.children[0].children[0].children[0].children[i].name == "Part1_2" || this.mesh.children[0].children[0].children[0].children[i].name == "Part1_5"){
          this.mesh.children[0].children[0].children[0].children[i].material.color.setStyle(this.setcolor);
        }
        // if(this.mesh.children[0].children[0].children[0].children[i].name == "Part1_7"){
        //   console.log(this.mesh.children[0].children[0].children[0].children[i]);
        //   this.mesh.children[0].children[0].children[0].children[i].scale.set( 1, 1, 1.5 );
        // }
      }
    },
    render() {
      this.renderer.render(this.scene, this.camera);
    },
    onChangeImage(index) {
      let self = this;
      self.loader = true;
      self.scene.remove(self.mesh);
      const loader = new GLTFLoader().setPath('products/');
      loader.load( self.products[index].name+'.gltf', function ( gltf ) {
        gltf.scene.position.set(0, -2, 0 );
        gltf.scene.scale.set( 20.0, 20.0, 20.0 );
        gltf.scene.rotation.set( - Math.PI / 2, Math.PI / 2, Math.PI / 2 );
        self.scene.add( gltf.scene );
        self.loader = false;
        self.mesh = gltf.scene;
      } );
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



