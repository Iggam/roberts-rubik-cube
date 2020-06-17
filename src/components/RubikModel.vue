<template>
	<div id="rubik">
		<Scene @complete="complete"> 
			<Camera v-model="myCamera" type="arcRotate" :radius="10" :alpha="Math.PI / 4" :beta="Math.PI / 3"></Camera>
			<HemisphericLight diffuse="#FFF"></HemisphericLight>
			<Box :options="boxOptions"></Box>
			<Sphere :position="[1, 0, 0]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#FF0000"></Material>
			</Sphere>
			<Sphere :position="[0, 1, 0]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#FFFFFF"></Material>
			</Sphere>
			<Sphere :position="[0, 0, 1]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#0000FF"></Material>
			</Sphere>
			<Sphere :position="[-1, 0, 0]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#FFA500"></Material>
			</Sphere>
			<Sphere :position="[0, -1, 0]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#FFFF00"></Material>
			</Sphere>
			<Sphere :position="[0, 0, -1]" :scaling="[0.25, 0.25, 0.25]">
				<Material diffuse="#008000"></Material>
			</Sphere>
			<Box v-for="cube in cubes" v-model="cube.model" :position="cube.position" :key="cube.id" :options="cube.boxOptions"/>
		</Scene>
		<div>
			<button @click="redCw">red CW</button>
			<button @click="redCcw">red CCW</button>
		</div>
		<div>
			<button @click="whiteCw">white CW</button>
			<button @click="whiteCcw">white CCW</button>
		</div>
		<div>
			<button @click="blueCw">blue CW</button>
			<button @click="blueCcw">blue CCW</button>
		</div>
		<div>
			<button @click="orangeCw">orange CW</button>
			<button @click="orangeCcw">orange CCW</button>
		</div>
		<div>
			<button @click="yellowCw">yellow CW</button>
			<button @click="yellowCcw">yellow CCW</button>
		</div>
		<div>
			<button @click="greenCw">green CW</button>
			<button @click="greenCcw">green CCW</button>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'RubikModel',
		data () {
			return {
				myScene: null,
				myCamera: null,
				myEntity: null,
				myAnimation: null,
				boxOptions: {
					faceColors: null
				},
				cubes: {
					rgw: {id: 'rgw', internal_faces: [0, 3, 5], position: [0.51, 0.51, -0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					rwb: {id: 'rwb', internal_faces: [1, 3, 5], position: [0.51, 0.51, 0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					rby: {id: 'rby', internal_faces: [1, 3, 4], position: [0.51, -0.51, 0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					ryg: {id: 'ryg', internal_faces: [0, 3, 4], position: [0.51, -0.51, -0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					obw: {id: 'obw', internal_faces: [1, 2, 5], position: [-0.51, 0.51, 0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					owg: {id: 'owg', internal_faces: [0, 2, 5], position: [-0.51 , 0.51, -0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					ogy: {id: 'ogy', internal_faces: [0, 2, 4], position: [-0.51, -0.51, -0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null},
					oyb: {id: 'oyb', internal_faces: [1, 2,4], position: [-0.51, -0.51, 0.51], model: null, transform_node: null, xRotation: 0, yRotation: 0, zRotation: 0, boxOptions: null}
				}
			}
		},
		watch: {
			myScene() {
				console.log(this.myScene)
				for (let key in this.cubes) {
					let x = this.BABYLON.MeshBuilder.CreateSphere("Sphere_"+this.cubes[key].id, {diameter:0.1}, this.myScene, true);
					this.cubes[key].transform_node = x
					this.cubes[key].model.parent = this.cubes[key].transform_node
				}
			},
			myCamera() {
				this.myCamera.lowerRadiusLimit = 10
				this.myCamera.upperRadiusLimit = 40
				console.log(this.myCamera)
			},
			myAnimation() {
				console.log(this.myAnimation)
			},
			myEntity() {
				console.log(this.myEntity)
			}
		},
		created() {
			const faceColors = [];
			faceColors[0] = this.BABYLON.Color3.FromHexString("#0000FF"); //blue
			faceColors[1] = this.BABYLON.Color3.FromHexString("#008000"); //green
			faceColors[2] = this.BABYLON.Color3.FromHexString("#FF0000"); //red
			faceColors[3] = this.BABYLON.Color3.FromHexString("#FFA500"); //orange
			faceColors[4] = this.BABYLON.Color3.FromHexString("#FFFFFF"); //white
			faceColors[5] = this.BABYLON.Color3.FromHexString("#FFFF00"); //yellow

			this.boxOptions.faceColors = faceColors

			for (let key in this.cubes) {
				this.cubes[key].boxOptions = {faceColors: [...faceColors]}

				this.cubes[key].internal_faces.forEach(value => {
					this.cubes[key].boxOptions.faceColors[value] = this.BABYLON.Color3.Black()
				})
			}
		},

		methods: {
			complete(event) {
				console.log('complete', event)
				this.myScene = event.scene
			},

			rotationFunc(axisName, mesh, rotation) {
				let axis = this.BABYLON.Axis[axisName.toUpperCase()];
		
				if (!mesh.rotationQuaternion) {
					mesh.rotationQuaternion = this.BABYLON.Quaternion.RotationYawPitchRoll(0, 0, 0);
				}
				
				let start = mesh.rotationQuaternion;
				
				let rotationQuaternion = this.BABYLON.Quaternion.RotationAxis(axis, rotation);
				let end = rotationQuaternion.multiply(mesh.rotationQuaternion);

				this.BABYLON.Animation.CreateAndStartAnimation("rotation", mesh, "rotationQuaternion", 90, 30, start, end, this.BABYLON.Animation.ANIMATIONLOOPMODE_RELATIVE)
			},

			rotate(axisName, rotation, side) {
				if (side == 'plus') {
					for (let key in this.cubes) {
						if (this.cubes[key].model.absolutePosition[axisName] > 0) {
							this.rotationFunc(axisName, this.cubes[key].transform_node, rotation)
						}
					}
				} else if (side == 'min') {
					for (let key in this.cubes) {
						if (this.cubes[key].model.absolutePosition[axisName] < 0) {
							this.rotationFunc(axisName, this.cubes[key].transform_node, rotation)
						}
					}
				}
			},

			redCw() {
				let rotation = Math.PI / 2
				this.rotate('x', rotation, 'plus')
			},

			redCcw() {
				let rotation = -Math.PI / 2
				this.rotate('x', rotation, 'plus')
			},

			whiteCw() {
				let rotation = Math.PI / 2
				this.rotate('y', rotation, 'plus')
			},

			whiteCcw() {
				let rotation = -Math.PI / 2
				this.rotate('y', rotation, 'plus')
			},

			blueCw() {
				let rotation = Math.PI / 2
				this.rotate('z', rotation, 'plus')
			},

			blueCcw() {
				let rotation = -Math.PI / 2
				this.rotate('z', rotation, 'plus')
			},
			orangeCw() {
				let rotation = Math.PI / 2
				this.rotate('x', rotation, 'min')
			},

			orangeCcw() {
				let rotation = -Math.PI / 2
				this.rotate('x', rotation, 'min')
			},

			yellowCw() {
				let rotation = Math.PI / 2
				this.rotate('y', rotation, 'min')
			},

			yellowCcw() {
				let rotation = -Math.PI / 2
				this.rotate('y', rotation, 'min')
			},

			greenCw() {
				let rotation = Math.PI / 2
				this.rotate('z', rotation, 'min')
			},

			greenCcw() {
				let rotation = -Math.PI / 2
				this.rotate('z', rotation, 'min')
			}
		}
	}
</script>

<style>
#rubik {
	height: 50vh;
}
</style>