<template>
    <div
        id="viewer"
        style="height:100vh; position: absolute; top: 0; left:0; right: 0; bottom: 0;"
    ></div>
</template>

<script as="ts">

    import * as Three from "three";
    import {buildRoom, createCube} from "./ViewerService";

    var OrbitControls = require("three-orbit-controls")(Three);

    export default {
        name: "Viewer",
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                controls: null,
                mesh: null,
                cube: null,
                raycaster: null,
                mouse: null,
                objects: [],
                rollOver: null
            };
        },
        mounted() {
            this.init();
            this.animate();
        },
        methods: {
            init: function () {
                let container = document.getElementById("viewer");

                this.camera = new Three.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.01, 10000);
                this.camera.position.set(500, 800, 1300);
                this.camera.lookAt(0, 0, 0);

                this.scene = new Three.Scene();
                // this.scene.background = new Three.Color(0xf0f0f0);

                const rollOverGeo = new Three.BoxBufferGeometry(50, 50, 50);
                const rollOverMaterial = new Three.MeshBasicMaterial({
                    color: 0xff0000,
                    opacity: 0.5,
                    transparent: true
                });
                this.rollOver = new Three.Mesh(rollOverGeo, rollOverMaterial);
                this.scene.add(this.rollOver);

                this.cube = createCube();
                // this.scene.add(this.cube);
                // this.objects.push(this.cube);
                this.scene.add(buildRoom());

                this.raycaster = new Three.Raycaster();
                this.mouse = new Three.Vector2();

                const geometry = new Three.PlaneBufferGeometry(1000, 1000);
                geometry.rotateX(-Math.PI / 2);

                const plane = new Three.Mesh(geometry, new Three.MeshBasicMaterial({visible: false}));
                this.scene.add(plane);
                this.objects.push(plane);

                this.renderer = new Three.WebGLRenderer({antialias: true});
                this.renderer.setPixelRatio(window.devicePixelRatio);
                this.renderer.setSize(window.innerWidth, window.innerHeight);
                // this.renderer.setSize(container.clientWidth, container.clientHeight);

                this.controls = new OrbitControls(this.camera, this.renderer.domElement);
                this.controls.enableDamping = true;
                this.controls.dampingFactor = 0.25;
                this.controls.screenSpacePanning = false;
                this.controls.minDistance = 1;
                this.controls.maxDistance = 5;
                this.controls.maxPolarAngle = Math.PI / 2;

                container.appendChild(this.renderer.domElement);

                function onDocumentMouseMove(event) {
                    event.preventDefault();
                    this.mouse.set((event.clientX / window.innerWidth) * 2 - 1, -(event.clientY / window.innerHeight) * 2 + 1);
                    this.raycaster.setFromCamera(this.mouse, this.camera);

                    var intersects = this.raycaster.intersectObjects(this.objects);

                    if (intersects.length > 0) {
                        var intersect = intersects[0];
                        this.rollOver.position.copy(intersect.point).add(intersect.face.normal);
                        this.rollOver.position.divideScalar(50).floor().multiplyScalar(50).addScalar(25);
                    }

                    this.renderer.render(this.scene, this.camera);
                }

                document.addEventListener("mousemove", onDocumentMouseMove.bind(this), false);

                // document.addEventListener("mousedown", this.onDocumentMouseDown, false);

            },

            // onDocumentMouseDown: function (event) {
            //   event.preventDefault();
            //   this.mouse.set((event.clientX / window.innerWidth) * 2 - 1, -(event.clientY / window.innerHeight) * 2 + 1);
            //   this.raycaster.setFromCamera(this.mouse, this.camera);
            //
            //   var intersects = this.raycaster.intersectObjects(this.objects);
            //
            //   if (intersects.length > 0) {
            //     var intersect = intersects[0];
            //     // delete cube
            //     if (isShiftDown) {
            //       if (intersect.object !== plane) {
            //         this.scene.remove(intersect.object);
            //         this.objects.splice(this.objects.indexOf(intersect.object), 1);
            //       }
            //       // create cube
            //     } else {
            //       var voxel = new THREE.Mesh(cubeGeo, cubeMaterial);
            //       voxel.position.copy(intersect.point).add(intersect.face.normal);
            //       voxel.position.divideScalar(50).floor().multiplyScalar(50).addScalar(25);
            //       scene.add(voxel);
            //       objects.push(voxel);
            //     }
            //     render();
            //   }
            // }

            animate: function () {
                // requestAnimationFrame(this.animate);
                // this.controls.update();
                // move(this.cube);
                // this.renderer.render(this.scene, this.camera);
            },
        },
    };

</script>
