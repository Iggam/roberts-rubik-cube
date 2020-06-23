<template>
	<div class="app">
		<div id="rubik">
			<Scene @complete="complete"> 
				<Camera v-model="myCamera" type="arcRotate" :radius="10" :alpha="Math.PI / 4" :beta="Math.PI / 3"></Camera>
				<HemisphericLight diffuse="#FFF"></HemisphericLight>
				<Sphere :position="[0, 0, 0]" :scaling="[3, 3, 3]">
					<Material diffuse="#000000"></Material>
				</Sphere>
				<Box v-for="cube in cubes" v-model="cube.model" :position="cube.position" :key="cube.id" :options="cube.boxOptions"/>
			</Scene>
		</div>
		<div class="container">
			<div class="buttons buttons-red">
				<b-button class="red" size="is-large" @click="rotate('x', Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="red" size="is-large" @click="rotate('x', -Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>
				</b-button>
			</div>
			<div class="buttons buttons-white">
				<b-button class="white" size="is-large" @click="rotate('y', Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="white" size="is-large" @click="rotate('y', -Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>
				</b-button>
			</div>
			<div class="buttons buttons-blue">
				<b-button class="blue" size="is-large" @click="rotate('z', Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="blue" size="is-large" @click="rotate('z', -Math.PI / 2, 'plus')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>
				</b-button>
			</div>
			<div class="buttons buttons-orange">
				<b-button class="orange" size="is-large" @click="rotate('x', -Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="orange" size="is-large" @click="rotate('x', Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>
				</b-button>
			</div>
			<div class="buttons buttons-yellow">
				<b-button class="yellow" size="is-large" @click="rotate('y', -Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="yellow" size="is-large" @click="rotate('y', Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>					
				</b-button>
			</div>
			<div class="buttons buttons-green">
				<b-button class="green" size="is-large" @click="rotate('z', -Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="redo-alt">
					</b-icon>
				</b-button>
				<b-button class="green" size="is-large" @click="rotate('z', Math.PI / 2, 'min')">
					<b-icon
						pack="fas"
						icon="undo-alt">
					</b-icon>
				</b-button>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'Rubik3x3Model',
		data () {
			return {
				myScene: null,
				myCamera: null,
				myEntity: null,
				myAnimation: null,
				rotating: false,
				boxOptions: {
					faceColors: null
				},
				cubes: {
					rgw: {id: 'rgw', internal_faces: [0, 3, 5], position: [1.01, 1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					rwb: {id: 'rwb', internal_faces: [1, 3, 5], position: [1.01, 1.01, 1.01], model: null, transform_node: null, boxOptions: null},
					rby: {id: 'rby', internal_faces: [1, 3, 4], position: [1.01, -1.01, 1.01], model: null, transform_node: null, boxOptions: null},
					ryg: {id: 'ryg', internal_faces: [0, 3, 4], position: [1.01, -1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					rw: {id: 'rw', internal_faces: [0, 1, 3, 5], position: [1.01, 1.01, 0], model: null, transform_node: null, boxOptions: null},
					rb: {id: 'rb', internal_faces: [1, 3, 4, 5], position: [1.01, 0, 1.01], model: null, transform_node: null, boxOptions: null},
					ry: {id: 'ry', internal_faces: [0, 1, 3, 4], position: [1.01, -1.01, 0], model: null, transform_node: null, boxOptions: null},
					rg: {id: 'rg', internal_faces: [0, 3, 4, 5], position: [1.01, 0, -1.01], model: null, transform_node: null, boxOptions: null},
					r: {id: 'r', internal_faces: [0, 1, 3, 4, 5], position: [1.01, 0, 0], model: null, transform_node: null, boxOptions: null},
					obw: {id: 'obw', internal_faces: [1, 2, 5], position: [-1.01, 1.01, 1.01], model: null, transform_node: null, boxOptions: null},
					owg: {id: 'owg', internal_faces: [0, 2, 5], position: [-1.01 , 1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					ogy: {id: 'ogy', internal_faces: [0, 2, 4], position: [-1.01, -1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					oyb: {id: 'oyb', internal_faces: [1, 2,4], position: [-1.01, -1.01, 1.01], model: null, transform_node: null, boxOptions: null},
					ow: {id: 'ow', internal_faces: [0, 1, 2, 5], position: [-1.01, 1.01, 0], model: null, transform_node: null, boxOptions: null},
					og: {id: 'og', internal_faces: [0, 2, 4, 5], position: [-1.01 , 0, -1.01], model: null, transform_node: null, boxOptions: null},
					oy: {id: 'oy', internal_faces: [0, 1, 2, 4], position: [-1.01, -1.01, 0], model: null, transform_node: null, boxOptions: null},
					ob: {id: 'ob', internal_faces: [1, 2, 4, 5], position: [-1.01, 0, 1.01], model: null, transform_node: null, boxOptions: null},
					o: {id: 'o', internal_faces: [0, 1, 2, 4, 5], position: [-1.01, 0, 0], model: null, transform_node: null, boxOptions: null},
					bw: {id: 'bw', internal_faces: [1, 2, 3, 5], position: [0, 1.01, 1.01], model: null, transform_node: null, boxOptions: null},
					by: {id: 'by', internal_faces: [1, 2, 3, 4], position: [0, -1.01,  1.01], model: null, transform_node: null, boxOptions: null},
					b: {id: 'b', internal_faces: [1, 2, 3, 4, 5], position: [0, 0, 1.01, ], model: null, transform_node: null, boxOptions: null},
					gw: {id: 'gw', internal_faces: [0, 2, 3, 5], position: [0, 1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					gy: {id: 'gy', internal_faces: [0, 2, 3, 4], position: [0, -1.01, -1.01], model: null, transform_node: null, boxOptions: null},
					g: {id: 'g', internal_faces: [0, 2, 3, 4, 5], position: [0, 0, -1.01, ], model: null, transform_node: null, boxOptions: null},
					w: {id: 'w', internal_faces: [0, 1, 2, 3, 5], position: [0, 1.01, 0], model: null, transform_node: null, boxOptions: null},
					y: {id: 'y', internal_faces: [0, 1, 2, 3, 4], position: [0, -1.01, 0], model: null, transform_node: null, boxOptions: null}

				}
			}
		},
		watch: {
			myScene() {
				console.log(this.myScene)
				this.myScene.clearColor = this.BABYLON.Color4(0, 0, 0, 0)
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

				let anim = this.BABYLON.Animation.CreateAndStartAnimation("rotation", mesh, "rotationQuaternion", 120, 30, start, end, this.BABYLON.Animation.ANIMATIONLOOPMODE_RELATIVE)
				anim.onAnimationEnd = () => {this.rotating = false}
			},

			rotate(axisName, rotation, side) {
				if(!this.rotating) {
					this.rotating = true
					if (side == 'plus') {
						for (let key in this.cubes) {
							if (this.cubes[key].model.absolutePosition[axisName] > 0.5) {
								this.rotationFunc(axisName, this.cubes[key].transform_node, rotation)
							}
						}
					} else if (side == 'min') {
						for (let key in this.cubes) {
							if (this.cubes[key].model.absolutePosition[axisName] < -0.5) {
								this.rotationFunc(axisName, this.cubes[key].transform_node, rotation)
							}
						}
					}
				}
			}
		}
	}
</script>

<style>
.all {
	height: 100vh;
	background-color: blue;
}
#rubik {
	height: 50vh;
}
.buttons-white {
	grid-area: buttons_white;
	justify-self: center;
}
.buttons-red {
	grid-area: buttons_red;
	justify-self: center;
}
.buttons-blue {
	grid-area: buttons_blue;
	justify-self: center;
}
.buttons-green {
	grid-area: buttons_green;
	justify-self: center;
}
.buttons-orange {
	grid-area: buttons_orange;
	justify-self: center;
}
.buttons-yellow {
	grid-area: buttons_yellow;
	justify-self: center;
}
.container {
	display: grid;
	grid-template-columns: auto;
	grid-template-rows: auto;
	grid-template-areas: 
		". buttons_white ."
		"buttons_red . buttons_blue"
		"buttons_green . buttons_orange"
		". buttons_yellow .";
}

.button.red {
	background-color: #FF0000;
	border-color: #4a4a4a;
}
.button.white {
	background-color: #FFFFFF;
	border-color: #363636;
}
.button.blue {
	background-color: #0000FF;
	border-color: #363636;
}
.button.yellow {
	background-color: #FFFF00;
	border-color: #363636;
}
.button.green {
	background-color: #008000;
	border-color: #363636;
}
.button.orange {
	background-color: #FFA500;
	border-color: #363636;
}
</style>