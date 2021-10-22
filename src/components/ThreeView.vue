<template>
<div class="viewer">
  <!-- <div>
    <input type="color" v-model="setcolor">
    <button @click="changebutton">Update</button>
  </div> -->
  <div>
  </div>
  <div class="Previewer">
    <div id="container"></div>
  </div>
  <div class="products">
    <div v-for="(item, index) in products" :key="index" class="itemImg">
      <img  :src='item.img' @click="onChangeImage(index)" class="productImg" alt="">
      <!-- <span>{{item.name}}</span> -->
    </div>
  </div>
</div>
  
</template>
<style scoped>
.viewer{
  display: flex;
}
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
  display: grid;
  grid-template-columns: auto auto auto auto auto auto;
  grid-gap: 10px;
  overflow-wrap: anywhere;
  height: 100%;
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
// import Autocomplete from 'v-autocomplete'
// import 'v-autocomplete/dist/v-autocomplete.css'

import Itemtemplate from './Itemtemplate.vue'

export default {
  // components:{
  //   Autocomplete,
  // },
  data() {
    return {
      templatedev: Itemtemplate,
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      cameraTarget: null,
      controls: null,
      setcolor: null,
      loader: false,
      selected_prod: '',
      products: [
        {name: 'Alcatel_Joy_Tab_2',  img: require('../assets/img/Alcatel_Joy_Tab_2.png')},
        {name: 'Amazon_fire_10', img: require('../assets/img/Amazon_fire_10.png')},
        {name: 'Amazon_fire_10_plus', img: require('../assets/img/Amazon_fire_10_plus.png')},
        {name: 'New', img: require('../assets/img/New.png')},
        {name: 'switch_7', img: require('../assets/img/switch_7.png')},
        {name: 'Asus_Transformer_Book_Flip', img: require('../assets/img/Asus_Transformer_Book_Flip.png')},
        {name: 'Asus_Transformer_Mini_10', img: require('../assets/img/Asus_Transformer_Mini_10.png')},
        {name: 'Amazon_Fire_HD_8', img: require('../assets/img/Amazon_Fire_HD_8.png')},
        {name: 'Asus_Zenpad_3s_10', img: require('../assets/img/Asus_Zenpad_3s_10.png')},
        {name: 'Chuwi_Hi10_Pro', img: require('../assets/img/Chuwi_Hi10_Pro.png')},
        {name: 'Chuwi_Hi13', img: require('../assets/img/Chuwi_Hi13.png')},
        {name: 'Chuwi_HiBook_Pro', img: require('../assets/img/Chuwi_HiBook_Pro.png')},
        {name: 'Huawei_Matepad_Pro', img: require('../assets/img/Huawei_Matepad_Pro.png')},
        {name: 'Huawei_MediaPad_T3_10', img: require('../assets/img/Huawei_MediaPad_T3_10.png')},
        {name: 'Lenovo_Tab_P11_Pro', img: require('../assets/img/Lenovo_Tab_P11_Pro.png')},
        {name: 'Lenovo_IdeaPad_Miix_520', img: require('../assets/img/Lenovo_IdeaPad_Miix_520.png')},
        {name: 'Lenovo_Miix_630', img: require('../assets/img/Lenovo_Miix_630.png')},
        {name: 'Lenovo_Tab_4_8', img: require('../assets/img/Lenovo_Tab_4_8.png')},
        {name: 'Lenovo_ThinkPad_X1', img: require('../assets/img/Lenovo_ThinkPad_X1.png')},
        {name: 'Lenovo_Yoga_Smart_Tab', img: require('../assets/img/Lenovo_Yoga_Smart_Tab.png')},
        {name: 'Microsoft_Surface_Go', img: require('../assets/img/Microsoft_Surface_Go.png')},
        {name: 'Microsoft_Surface_Pro_4', img: require('../assets/img/Microsoft_Surface_Pro_4.png')},
        {name: 'Microsoft_Surface_Pro_8', img: require('../assets/img/Microsoft_Surface_Pro_8.png')},
        {name: 'Onn_8_inch_tablet', img: require('../assets/img/Onn_8_inch_tablet.png')},
        {name: 'Onyx_Boox_Max_Lumi', img: require('../assets/img/Onyx_Boox_Max_Lumi.png')},
        {name: 'Samsung_Galaxy_Tab_A_8', img: require('../assets/img/Samsung_Galaxy_Tab_A_8.png')},
        {name: 'Samsung_Galaxy_Tab_S2', img: require('../assets/img/Samsung_Galaxy_Tab_S2.png')},
        {name: 'Samsung_Galaxy_Tab_S3', img: require('../assets/img/Samsung_Galaxy_Tab_S3.png')},
        {name: 'Apple_iPad_2nd_generation', img: require('../assets/img/Apple_iPad_2nd_generation.png')},
        {name: 'Lenovo_Tab_M10_Plus', img: require('../assets/img/Lenovo_Tab_M10_Plus.png')},
        {name: 'Samsung_Galaxy_Tab_A7_10', img: require('../assets/img/Samsung_Galaxy_Tab_A7_10.png')},
        {name: 'Samsung_Galaxy_Tab_A7_Lite_8', img: require('../assets/img/Samsung_Galaxy_Tab_A7_Lite_8.png')},
        {name: 'Samsung_Galaxy_Tab_Active_3_8', img: require('../assets/img/Samsung_Galaxy_Tab_Active_3_8.png')},
        {name: 'Samsung_Galaxy_Tab_S4', img: require('../assets/img/Samsung_Galaxy_Tab_S4.png')},
        {name: 'Samsung_Galaxy_Tab_S5E', img: require('../assets/img/Samsung_Galaxy_Tab_S5E.png')},
        {name: 'Samsung_Galaxy_Tab_S6', img: require('../assets/img/Samsung_Galaxy_Tab_S6.png')},
        {name: 'Samsung_Galaxy_Tab_S6_Lite', img: require('../assets/img/Samsung_Galaxy_Tab_S6_Lite.png')},
        {name: 'Samsung_Galaxy_Tab_S7_Plus', img: require('../assets/img/Samsung_Galaxy_Tab_S7_Plus.png')},
        {name: 'Apple_iPad_3rd_generation', img: require('../assets/img/Apple_iPad_3rd_generation.png')},
        {name: 'Apple_iPad_5th_generation', img: require('../assets/img/Apple_iPad_5th_generation.png')},
        {name: 'Apple_iPad_7th_generation', img: require('../assets/img/Apple_iPad_7th_generation.png')},
        {name: 'Apple_iPad_Air_1st_generation', img: require('../assets/img/Apple_iPad_Air_1st_generation.png')},
        {name: 'Apple_iPad_Air_2nd_generation', img: require('../assets/img/Apple_iPad_Air_2nd_generation.png')},
        {name: 'Apple_iPad_Air_4th_generation', img: require('../assets/img/Apple_iPad_Air_4th_generation.png')},
        {name: 'Apple_iPad_mini_1st_generation', img: require('../assets/img/Apple_iPad_mini_1st_generation.png')},
        {name: 'Apple_iPad_mini_2nd_generation', img: require('../assets/img/Apple_iPad_mini_2nd_generation.png')},
        {name: 'Apple_iPad_mini_4th_generation', img: require('../assets/img/Apple_iPad_mini_4th_generation.png')},
        {name: 'Apple_iPad_mini_6th_generation', img: require('../assets/img/Apple_iPad_mini_6th_generation.png')},
      ],
    }
  },
  computed: {
    selectItems(){
      return this.products.map((item) => {
        return item.name;
      })
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

        // const ambientLight = new Three.AmbientLight( 0xffffff, 0.8 );
				// this.scene.add( ambientLight );

				const pointLight = new Three.PointLight( 0xffffff, 0.8 );
				this.camera.add( pointLight );
				// scene.add( camera );
        this.scene.background = new Three.Color(0xa0a0a0);
        new RGBELoader()
					.load( 'royal_esplanade_1k.hdr', function ( texture ) {

						texture.mapping = Three.EquirectangularReflectionMapping;

						// self.scene.background = texture;
						self.scene.environment = texture;

						self.render();

						// model

						// const loader = new GLTFLoader().setPath('products/');
            const loader = new GLTFLoader().setPath('products/');
						loader.load( self.products[0].name+'.gltf', function ( gltf ) {
            // loader.load('Lenovo_Tab_4_8.gltf', function ( gltf ) {
              gltf.scene.position.set(0, 0, 0 );
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
    getLabel(item){
      return item.name;
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



