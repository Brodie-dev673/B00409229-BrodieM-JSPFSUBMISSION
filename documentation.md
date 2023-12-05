
# Element 5 Documentation 
Element 5 has a mix of several things found throughout the other elements such as 
1. A height map 
2. A controllable player charater 
3. objects with physics components 


# Code

```
  let initializedHavok;

  HavokPhysics().then((havok) => {
    initializedHavok = havok;
  });

  const havokInstance = await HavokPhysics();
  const havokPlugin = new HavokPlugin(true, havokInstance);
  
  globalThis.HK = await HavokPhysics();
```
the code above intializes the physics engine used by babylon js and gives objects a physical prescence within the scene which makes them react to collision from the player and other objects.



``````
let keyDownMap: any[] = [];
  
  function importPlayerMesh(scene: Scene, collider: Mesh, x: number, y: number) {
    let tempItem = { flag: false }
    let item: any = SceneLoader.ImportMesh("", "./models/", "dummy3.babylon", scene, function(newMeshes, particleSystems, skeletons) {
        let mesh = newMeshes[0];
        let skeleton = skeletons[0];
        skeleton.animationPropertiesOverride = new AnimationPropertiesOverride();
        skeleton.animationPropertiesOverride.enableBlending = true;
        skeleton.animationPropertiesOverride.blendingSpeed = 0.05;
        skeleton.animationPropertiesOverride.loopMode = 1;

        let walkRange: any = skeleton.getAnimationRange("YBot_Walk");
        // let runRange: any = skeleton.getAnimationRange("YBot_Run");
        // let leftRange: any = skeleton.getAnimationRange("YBot_LeftStrafeWalk");
        // let rightRange: any = skeleton.getAnimationRange("YBot_RightStrafeWalk");
        // let idleRange: any = skeleton.getAnimationRange("YBot_Idle");

        let animating: boolean = false;

        let speed: number = 2;

        scene.onBeforeRenderObservable.add(()=> {
            let keydown: boolean = false;
            if (keyDownMap["w"] || keyDownMap["ArrowUp"]) {
                mesh.position.z += 0.09;
                mesh.rotation.y = 0;
                keydown = true;
            }
            if (keyDownMap["a"] || keyDownMap["ArrowLeft"]) {
                mesh.position.x -= 0.09;
                mesh.rotation.y = 3 * Math.PI / 2;
                keydown = true;
            }
            if (keyDownMap["s"] || keyDownMap["ArrowDown"]) {
                mesh.position.z -= 0.09;
                mesh.rotation.y = 2 * Math.PI / 2;
                keydown = true;
            }
            if (keyDownMap["d"] || keyDownMap["ArrowRight"]) {
                mesh.position.x += 0.09;
                mesh.rotation.y = Math.PI / 2;
                keydown = true;
            }
            if (keydown) {
              if (!animating) {
                  animating = true;
                  scene.beginAnimation(skeleton, walkRange.from, walkRange.to, true);
              }
            } else {
              animating = false;
              scene.stopAnimation(skeleton);
            }

            if (mesh.intersectsMesh(collider)) {
              console.log("COLLIDED");
              
            }
        });
        
        item = mesh;
        let playerAggregate = new PhysicsAggregate(item, PhysicsShapeType.CAPSULE, { mass: 0 }, scene);
        //playerAggregate.body.setCollisionCallbackEnabled(true);
        playerAggregate.body.disablePreStep = false;
    });
    item.mesh;
    return item;
  }  
``````
this code creates a player models, creates animations from the model and gives it a series of inputs to let the character walk around the environment and interact with physics objects. 



```
function createTerrain(scene: Scene) {
    //Create large ground for environment
    const largeGround = MeshBuilder.CreateGroundFromHeightMap("largeGround", 
   "https://assets.babylonjs.com/environments/villageheightmap.png", {width:150, 
   height:150, subdivisions: 20, minHeight:0, maxHeight: 10});
   largeGround.position.y = 0.01;
    //Add texture to ground
    const largeGroundMat = new StandardMaterial("largeGroundMat");
    largeGroundMat.diffuseTexture = new
   Texture("https://assets.babylonjs.com/environments/valleygrass.png");
    largeGround.material = largeGroundMat; 
    
    return largeGround; 
    } 
`````
this creates a terrain from a picture and creates a height map to add variety to the environment stop it being a flat plane. 

``````
    const hkPlugin = new HavokPlugin(true, HavokPlugin);
    that.scene.enablePhysics(new Vector3(0, -9.8, 0), havokPlugin);

    that.box = createBox(that.scene, 0, 2, 4);
    that.sphere = CreateSphere(that.scene, 2,4,2, );
    that.box = createBox(that.scene, 0, 2, -4);
    that.sphere = CreateSphere(that.scene, -2, 4, -2, );
    that.box = creatSecondBox(that.scene,5,2,0);
    that.box = creatSecondBox(that.scene,-5,2,0);
    that.ground = createGround(that.scene);
    that.terrain = createTerrain(that.scene);
    that.importMesh = importPlayerMesh(that.scene, that.box, 0, 0);
    that.actionManager = actionManager(that.scene);

    that.camera = createArcRotateCamera(that.scene);
    that.skybox = createSky(that.scene);
    that.hemisphericLight = createHemiLight(that.scene);

    return that;
  }
``````
the code above renders all the functions created into the scene so they can be  viewed by the camera and and controlled by the player. 

``````
 function createSceneButton(scene: Scene, name: string, index: string, x: string, y: string, advtex/*, val: number*/){
    let button = GUI.Button.CreateSimpleButton(name, index);
        button.left = x;
        button.top  =  y;
        button.width = "160px"
        button.height = "60px";
        button.color = "white";
        button.cornerRadius = 20;
        button.background = "purple";
        const buttonClick = new Sound("MenuClickSFX", "./audio/menu-click.wav", scene, null, {
          loop: false,
          autoplay: false,
        });
        button.onPointerUpObservable.add(function() {
            buttonClick.play();
            setSceneIndex(1);
        });
        advtex.addControl(button); 
        return button;
  }
``````
the code above creates a button in a menu that lets the player switch from a menu scene to the game scene that has a playable character. 

``````
let scene;
let scenes: any[] = [];

let eng = new Engine(canvas, true, undefined, true); // originally null instead of undefined

scenes[0] = MenuScene(eng);
scenes[1] = GameScene(eng);
scene = scenes[0].scene;

//call default scene
setSceneIndex(0);

//let startScene = scenes[];
``````
this code handle the scenes and make sure the player is directed towards the correct scene from the buttons in the menu.

``````
  function createSky(scene: Scene) {
    const skybox = MeshBuilder.CreateBox("skyBox", {size:150}, scene);
	  const skyboxMaterial = new StandardMaterial("skyBox", scene);
	  skyboxMaterial.backFaceCulling = false;
	  skyboxMaterial.reflectionTexture = new CubeTexture("textures/skybox", scene);
	  skyboxMaterial.reflectionTexture.coordinatesMode = Texture.SKYBOX_MODE;
	  skyboxMaterial.diffuseColor = new Color3(0, 0, 0);
	  skyboxMaterial.specularColor = new Color3(0, 0, 0);
	  skybox.material = skyboxMaterial;

    return skybox;
  }
``````
this code creates a cube around the envrionement that is textured to look like clouds to stop the scene being dull.

``````
import "@babylonjs/core/Debug/debugLayer";
import "@babylonjs/inspector";
import {
    Scene,
    ArcRotateCamera,
    Vector3,
    Vector4,
    HemisphericLight,
    SpotLight,
    MeshBuilder,
    Mesh,
    Light,
    Camera,
    Engine,
    StandardMaterial,
    Texture,
    Color3,
    Space,
    ShadowGenerator,
    PointLight,
    DirectionalLight,
    CubeTexture,
    Sprite,
    SpriteManager,
    SceneLoader,
    ActionManager,
    ExecuteCodeAction,
    Action,
    AnimationPropertiesOverride,
    AnimationRange,
    PhysicsAggregate,
    PhysicsShapeType,
    Quaternion,
    TransformNode,
    PhysicsImpostor,
    Sound,
    Collider,
    
  } from "@babylonjs/core";
  import * as GUI from "@babylonjs/gui";
  import HavokPhysics from "@babylonjs/havok";
  import { HavokPlugin } from "@babylonjs/core";
  import { PhysicsShapeBox, PhysicsShapeCapsule, PhysicsShapeSphere, PhysicsShapeContainer } from "@babylonjs/core/Physics/v2/physicsShape";
  import { PhysicsBody } from "@babylonjs/core/Physics/v2/physicsBody";
  import { PhysicsMotionType } from "@babylonjs/core/Physics/v2/IPhysicsEnginePlugin";
``````
this imports various plugins from Babylon js such as the scene index which lets you sitch between various scenes, the Havok plugin which is the physics engine used by babylon and the GUI used to create the menus. 