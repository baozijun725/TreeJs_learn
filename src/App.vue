<script setup>
// 导入threejs
import * as THREE from "three";
// 导入轨道控制器
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
// 导入lil.gui
import { GUI } from "three/examples/jsm/libs/lil-gui.module.min.js";
// 导入hdr加载器
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader.js";

// 创建场景
const scene = new THREE.Scene();

// 创建相机
const camera = new THREE.PerspectiveCamera(
  45, // 视角
  window.innerWidth / window.innerHeight, // 宽高比
  0.1, // 近平面
  1000 // 远平面
);

// 创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 设置相机位置
camera.position.z = 5;
camera.position.y = 2;
camera.position.x = 2;
camera.lookAt(0, 0, 0);

// 添加世界坐标辅助器
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);

// 添加轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
// 设置带阻尼的惯性
controls.enableDamping = true;
// 设置阻尼系数
controls.dampingFactor = 0.05;
// 设置旋转速度
// controls.autoRotate = true;

// 渲染函数
function animate() {
  controls.update();
  requestAnimationFrame(animate);
  // 渲染
  renderer.render(scene, camera);
}
animate();

// 监听窗口变化
window.addEventListener("resize", () => {
  // 重置渲染器宽高比
  renderer.setSize(window.innerWidth, window.innerHeight);
  // 重置相机宽高比
  camera.aspect = window.innerWidth / window.innerHeight;
  // 更新相机投影矩阵
  camera.updateProjectionMatrix();
});
let params = {};
//创建gui
const gui = new GUI();
//创建纹理添加器
let textureLoader = new THREE.TextureLoader();
//加载纹理
let texture = textureLoader.load(
  "./texture/watercover/CityNewYork002_COL_VAR1_1K.png"
);

//添加纹理的颜色空间
texture.colorSpace = THREE.SRGBColorSpace;
// texture.colorSpace = THREE.LinearSRGBColorSpace;
// texture.colorSpace = THREE.NoColorSpace;

//加载ao贴图(让边缘更亮一些)
let aoMap = textureLoader.load("./texture/watercover/CityNewYork002_AO_1K.jpg");

//设置透明度贴图
let alphaMap = textureLoader.load("./texture/door/height.jpg");

//设置光照贴图
let lightMap = textureLoader.load("./texture/colors.png");

// 高光贴图
let specularMap = textureLoader.load(
  "./texture/watercover/CityNewYork002_GLOSS_1K.jpg"
);

//rgbeLoader加载
let rgbeLoader = new RGBELoader();
rgbeLoader.load("./texture/Alex_Hart-Nature_Lab_Bones_2k.hdr", (envMap) => {
  //设置球形贴图
  envMap.mapping = THREE.EquirectangularReflectionMapping;
  //设置环境贴图
  scene.background = envMap;
  //场景设置环境贴图
  scene.environment = envMap;
  //给平面的材质设置环境贴图
  planeMaterial.environment = envMap;
});

let planeGeomoetry = new THREE.PlaneGeometry(1, 1);
let planeMaterial = new THREE.MeshBasicMaterial({
  // color: 0xffff00,
  map: texture,
  transparent: true, //设置透明度
  aoMap: aoMap,
  aoMapIntensity: 1, //设置透明度额强度，默认是1
  // alphaMap: alphaMap, //透明度贴图
  // lightMap:lightMap,//光照贴图
  reflectivity: 0.5,
  specularMap: specularMap, //高光贴图（比较亮的地方）
});
let plane = new THREE.Mesh(planeGeomoetry, planeMaterial);
scene.add(plane);
// gui.add(planeMaterial,'transparent','透明')
gui.add(planeMaterial, "aoMapIntensity").min(0).max(1).name("ao贴图");

//更改颜色空间
gui
  .add(texture, "colocSpace", {
    sRGB: THREE.SRGBColorSpace,
    Linear: THREE.LinearSRGBColorSpace,
  })
  .onChange(() => {
    texture.needsUpdate = true;
  });
</script>

<template>
  <div></div>
</template>

<style scoped></style>
