<template>
  <canvas ref="canvasRef"></canvas>
</template>

<script setup>
import { ref, onMounted, onUnmounted} from "vue"
import { OrbitControls} from "three/examples/jsm/controls/OrbitControls"
import * as THREE from "three"

var controls


let canvasRef = ref();

const sizes = {
  width: 800,
  height: 600
}

let scene = new THREE.Scene()

let renderer

// BOX
const boxGeometry = new THREE.BoxGeometry(1,1,1)
const boxMaterial = new THREE.MeshBasicMaterial({color: 0xff0000}) 
const box = new THREE.Mesh(boxGeometry,boxMaterial)


box.position.set(0 , 0, 0)

scene.add(box)


/* ORTOGRAPHIC CAMERA:
const aspectRatio = sizes.width/sizes.height // utilizziamo questo parametro , moltiplicandolo per i valor idel campo di vista orizzontale
                                            // cosi da mantenere le proporzioni degli oggetti e non deformarli
const camera = new THREE.OrthographicCamera(
  -1 * aspectRatio, //Left 
  1 * aspectRatio, //Right
  1,              //Top
  -1,             //Bottom
  0.1,            //Near Plane
  100)            //Far Plane
camera.position.set(2,2,2)
camera.lookAt(box.position)
scene.add(camera)
*/

// PERSPECTIVE CAMERA: viene usata per renderizzare la scena, con prospettiva.
let camera = new THREE.PerspectiveCamera(
  75, 
  sizes.width / sizes.height, 
  0.1,
  100  
);

camera.position.set(0,0,3)
camera.lookAt(box.position)
scene.add(camera);


const clock = new THREE.Clock()

const animate = () =>{
  const elapsedTime = clock.getElapsedTime()

  //rotazione completa usando seno e coseno
  //camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2
  //camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2
  //camera.position.y = cursor.y * 5

  //diciamo alla camera si guardare alla posizione del cubo ad ogni frame
  //camera.lookAt(box.position)

  controls.update()
  
  renderer.render(scene, camera)
}

onMounted(() => {
  renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value
  });
 
  renderer.setSize(sizes.width, sizes.height)
  renderer.setPixelRatio(window.devicePixelRatio)
  renderer.render(scene, camera)
  renderer.setAnimationLoop(animate)

  // CONTROLS
  // Ci sono diversi tipi di controls in threejs
  
  //1) DEVICE ORIENTATION CONTROLS
  //questa è usata soprattutto per smartphone e altri device che hanno l'orientazione, ti permette quindi di controllare la camera
  //anche se la rotazione dello schermo cambia, e crea delle esperienze immersive (USATO ANCHE PER VR) (ios ha smesso di supportare il device orientation)

  //2) FLY CONTROL
  // Permette di muovere la videocamera , come se fossi si una navetta spaziale. puoi ruotare tutti e 3 gli asse, andare avanti e indietro

  //3) FIRST PERSON CONTROL
  // simile al fly control, ma con asse Y fisso, non è il controller per i giochi in FPS

  //4) POINTER LOCK CONTROLS
  // usa il pointer lock javascript API
  // difficile da usare e in pratica fa si che possiamo ruotare la visuale della telecamera col mouse, pur non vedendo il puntatore del mouse a schemo

  //5) ORBIT CONTROLS
  // orbit controls ci permette di avere un controllo simile a quello che abbiamo implementato noi nella lezione precedente, ma migliore e con piu funzioni

  //6) TRACKBALL CONTROLS
  // simile a orbit control, solo che non abbiamo limiti a quanto possiamo ruotare l'angolo verticale

  //7) TRANSFORM CONTROLS
  // questo controllers non ha nulla a che fare con la camera. è piu simile al creare un editor per muovere oggetti ed altro

  //8) DRAG CONTROLS
  // anche questo non ha nulla a che fare con la camera, serve per muovere gli oggetti su un "piano di fronte" la camera

  //Usiamo l'orbit controls
  //devi passare due argomenti alla classe, la tua camera(perche orbit control la aggiornerà), ed un elemento nella pagina che servira come riferimento in cui posizionare gli event listner , in questo caso useremo il canvas con il ref canvasRef 
  controls = new OrbitControls(camera, canvasRef.value)
  controls.enableDamping = true; // permette uno effetto smooth una volta che rilasciamo l'elemento, rallenta fino a fermarsi

  //possiamo anche cambiare la posizione della telecamera rispetto al canvas
  //controls.target.y = 1
  //controls.update() // Dobbiamo pero ricordarci di aggiornare il controls dopo aver cambiato la posizione
});

onUnmounted(() => {
  renderer.setAnimationLoop(null)
});

</script>

<style>
canvas {
  width: 100%;
  height: 100%;
  display: block;
}
</style>