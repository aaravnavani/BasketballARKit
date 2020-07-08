# Making a Basketball game using ARKit 

In this project, we make a basketball game using ARKit. 

## Adding the basketball and hoop

In order to add the hoop, we download the hoop.scn asset and put it in the art.scnassets. For the basketball, we download a texture which will later be added to a sphere. 

In order to add the backboard, we write this function: 

```swift
func addBackboard() {
    guard let backboardScene = SCNScene(named: "art.scnassets/hoop.scn") else {
        return
    }
    
    guard let backboardNode = backboardScene.rootNode.childNode(withName: "backboard", recursively: false) else {
        return
    }
    backboardNode.position = SCNVector3(x: 0, y: -1 , z:  -4)
    
    let physicsShape = SCNPhysicsShape(node: backboardNode, options: [SCNPhysicsShape.Option.type: SCNPhysicsShape.ShapeType.concavePolyhedron])
    let physicsBody = SCNPhysicsBody(type: .static, shape: physicsShape)
    
    backboardNode.physicsBody = physicsBody
    
    sceneView.scene.rootNode.addChildNode(backboardNode)
    
    
    currentNode = backboardNode
    
    
}
``` 

In the first bit, we load our hoop in from the hoop.scn file. We need a guard statement in case there is no such file "hoop.scn" in the art.scnassets folder.  We then create the backboard node, which will represent where the basketball will be placed: 

```swift
guard let backboardNode = backboardScene.rootNode.childNode(withName: "backboard", recursively: false) else {
    return
}
backboardNode.position = SCNVector3(x: 0, y: -1 , z:  -4)
```

Next, we create the physicsShape, which makes it so that the ball doesn't go through the backboard. We do this using the following two lines: 

```swift
let physicsShape = SCNPhysicsShape(node: backboardNode, options: [SCNPhysicsShape.Option.type: SCNPhysicsShape.ShapeType.concavePolyhedron])
let physicsBody = SCNPhysicsBody(type: .static, shape: physicsShape)

backboardNode.physicsBody = physicsBody
```

Finally, we add the backboardNode to the child node array. 

Now that the hoop is added, we have to add the basketball. We want to add the basketball at the center of the camera, so in order to do that, we write the following code: 

```swift
let cameraLocation = SCNVector3(x: cameraTransform.m41, y: cameraTransform.m42, z: cameraTransform.m43)
let cameraOrientation = SCNVector3(x: -cameraTransform.m31, y: -cameraTransform.m32, z: -cameraTransform.m33)

let cameraPosition = SCNVector3Make(cameraLocation.x + cameraOrientation.x, cameraLocation.y + cameraOrientation.y, cameraLocation.z + cameraOrientation.z)
```

In order for the basketball to look like an actual baskatball instead of just a white sphere, we have to add the material to our sphere: 

```swift
let ball = SCNSphere(radius: 0.15)
       let material = SCNMaterial()
       material.diffuse.contents = UIImage(named: "basketballSkin.png")
       ball.materials = [material]
       
       let ballNode = SCNNode(geometry: ball)
       ballNode.position = cameraPosition
```

Just like the backboard, we need to add the physicsBody:

```swift
let physicsShape = SCNPhysicsShape(node: ballNode, options: nil)
let physicsBody = SCNPhysicsBody(type: .dynamic, shape: physicsShape)

ballNode.physicsBody = physicsBody
```

In order for it to be an actual basketball game, we need to allow the user to interact with the basketball so that they can throw it at the hoop. We do this by adding a physics force: 

```swift
let forceVector:Float = 6

ballNode.physicsBody?.applyForce(SCNVector3(x: cameraPosition.x * forceVector, y: cameraPosition.y * forceVector, z: cameraPosition.z * forceVector), asImpulse: true)
```

Finally, we add it to the child node array: 

```swift
sceneView.scene.rootNode.addChildNode(ballNode)
```

## Making the hoop move 

In order to make the game a bit more challenging, we can make the hoop move around the user's screen. We can write a function called horizontalAction which moves the backboard left and right: 

```swift
func horizontalAction(node: SCNNode) {
    let leftAction = SCNAction.move(by: SCNVector3(x: -1, y: 0, z: 0), duration: 3)
    let rightAction = SCNAction.move(by: SCNVector3(x: 1, y: 0, z: 0), duration: 4)
    
    let actionSequence = SCNAction.sequence([leftAction, rightAction])
    
    node.runAction(SCNAction.repeat(actionSequence, count: 4))
    
}
```

The first two lines of the above function create the actions which tell the hoop to move left and right. The duration part tells us how many times the hoop will move left and right. We then add both of these actions to an action sequence and then finally, we run all the actions in the sequence. 

We can do the same thing for the vertical action (where the hoop moves in a circle), except there would be more actions: 

```swift
func roundAction(node: SCNNode) {
    let upLeft = SCNAction.move(by: SCNVector3(x: 1, y: 1, z: 0), duration: 2)
    let downRight = SCNAction.move(by: SCNVector3(x: 1, y: -1, z: 0), duration: 2)
    let downLeft = SCNAction.move(by: SCNVector3(x: -1, y: -1, z: 0), duration: 2)
    let upRight = SCNAction.move(by: SCNVector3(x: -1, y: 1, z: 0), duration: 2)
    
    let actionSequence = SCNAction.sequence([upLeft, downRight, downLeft, upRight])
    
    node.runAction(SCNAction.repeat(actionSequence, count: 4))
    
}
```

And that's it! 





