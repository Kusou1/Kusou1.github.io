---
layout: keynote
title: '使用React和Three.js制作3D landing page'
iframe: 'https://courageous-dragon-4ccc63.netlify.app'
date: 2022-05-28 20:26:00
author: 'zhangshaohong'
catalog: true
tags:
    - React
    - javascript
    - three.js
---


> Creating a cool 3D landing page using React and Three.js

### Intro

You've probably seen some amazing websites using 3D graphics. If you've ever only CSS, HTML and JS when creating a webpage achieving this might seem impossible. I certainly did. But using react-three-fiber you can create create amazing landing pages with 3D graphics using familiar declarative React syntax.

### What we'll be building

A pretty basic but cool interactive landing page with 3D graphics. I'll be using React together with three.js, react-three-fiber and Blender for creating some 3D models Here's a live preview: [courageous-dragon-4ccc63.netlify.app](https://courageous-dragon-4ccc63.netlify.app/)

### Dev environment

These days i pretty much always prefer to use Vite as a bundler and scaffolding tool together with pnpm as the package manager. Setting up a dev environment is as easy as running `pnpm create vite`. A couple packages are needed and can be added with `pnpm add @react-three/fiber @react-three/drei @react-three/postprocessing @types/three three` Remove all the extra "stuff" that is not needed like `App.css`, `icon.svg` and all the context in `index.css`. Replace the content in `App.jsx` with


```javascript
function App() {
  return <div>Hello world!</div>;
}

export default App;
```

### Time to code

To get things started we'll render a simple object to get a feel for how this all works. We'll need to import the `<Canvas>` component.`import { useFrame, useThree } from "@react-three/fiber "` It's inside this component that all the 3D magic happens.

Replace the current return statement with `<Canvas></Canvas>`. Anything we put as children inside the Canvas will be rendered to our scene. Alright lets start with a cube to get a feel for things. To do this we'll use the `<mesh>` tag and create our own component like so:


```javascript
const MyMesh = () => {
    return (
      <mesh>
        <boxGeometry />
        <meshBasicMaterial color="red" />
      </mesh>
  )
}
```

then place our newly created component inside the canvas and adjust our camera to better match what we're going for. Feel free to adjust these values as you see fit!


```javascript
  <Canvas camera={{ fov: 45 }} >
      <MyMesh />
   </Canvas>
```

You should now have a red cube! Pretty exciting stuff i know.

### Add functionality to our component

To add some life to our red cube well make it float pseudo-randomly from the bottom to the top in an infinite loop. We'll use the `useFrame` hook from react-three/fiber for this. This function takes a callback function as an argument which it will run 60 times a second. Lets import the function and add it to our component.

```
import { useFrame } from "@react-three/fiber "
```


```javascript
useFrame(() => {
    //I run 60 times a second :O!
});
```

### Making our cube move!

We need to create and store the initial location/state of our component. We'll use the useState hook for this.


```javascript
const [data] = useState({
    x: 0,
    y: 0,
})
```

To actually move our component we'll need to give it a ref that we can use inside the `useFrame` hook. Create a ref using the `useRef` hook and assign it to the mesh. Our component should look something like this now


```javascript
const MyMesh = () => {
  const ref = useRef();
  const [data] = useState({
    x: 0, // Initial X axis of our component  (-1 to 1 range)
    y: 0, // Initial Y axis of our component  (-1 to 1 range)
    z: 0, // Initial Y axis of our component  (-1 to 1 range)
  });

 useFrame(() => {
   //I run 60 times a second
});
    return (
      <mesh ref={ref}>
        <boxGeometry />
        <meshBasicMaterial color="red" />
      </mesh>
  )
}
```

Alright that looks pretty good. Inside the `useFrame` hook we can access the current object on the ref we've created and increment the x, y and z axis.


```javascript
  useFrame(() => {
      ref.current.position.set(
        0,
        (data.y += 0.05),
        0,
      );
  });
```

Cool. But now our component just leaves the screen and never comes back. That's not what we want. Luckily there is a very handy hook we can use called `useThree`. Lets update our import statement from `react-three/fiber`.
`import { useFrame, useThree } from "@react-three/fiber "`
Then we'll everything we need to position our elements correctly


```javascript
  const { viewport, camera } = useThree();
  const { width, height } = viewport.getCurrentViewport(camera, [0, 0, 0]);
```

then lets update our `useFrame` to this


```javascript
  useFrame(() => {
      ref.current.position.set(
        0,
        (data.y += 0.01),
        0
      );

    if (data.y > height + 1) data.y = -(height + 1);
});
```

### Adding more elements and creating some chaos

Alright time to make our canvas more interesting. We'll spawn inn a 100 elements and spread them out on the canvas. Instead of just adding a single `<MyMesh />` we can replace it with:


```javscript
  {Array.from({ length: 100 }, (_, i) => (
        <MyMesh key={i} index={i} z={(i / 100) * 10} />
      ))}
```

Now lets change our component to use these new props and add some more randomness to everything. To place elements randomly along the x and y axis we can use a helper function from three.js called `randFloatSpread(range)` which returns a random float from -range/2 to range/2.

To use helper functions import three as such
`import * as THREE from "three"`

Now we can reformat our component to something like this:


```javascript
const MyMesh = ({ index, z }) => {
  const [data] = useState({
    x: THREE.MathUtils.randFloatSpread(2), //Returns random float from -1 to 1
    y: THREE.MathUtils.randFloatSpread(height) * 2,
    z: -z,
  });

  useFrame((_, dt) => {
/*
dt is DeltaTime and the time since the last frame. 
This check makes it so that we dont update the position if the user in currently in another tab 
*/
    if (dt < 0.1) 
      ref.current.position.set(
        index === 0 ? 0 : data.x * width,
        (data.y += dt * 0.75),
        -z + Math.sin((data.z -= 0.01))
      );

    if (data.y > height * (index === 0 ? 4 : 1) + 5)
      data.y = -(height * (index === 0 ? 4 : 1) + 5);
  });

  return (
    <mesh ref={ref} scale={0.5}>
      <boxGeometry />
      <meshStandardMaterial
        color={colors[Math.floor(Math.random() * colors.length - 1)]}
      />
    </mesh>
  );

}
```

Alright now we're getting somewhere!

### Let's add some rotation

Changing the rotation of an object is very similar to how we changed the position of the element. First we'll extend or initial state by adding a x-rotation and spin speed like so:


```javascript
const [data] = useState({
    x: THREE.MathUtils.randFloatSpread(2),
    y: THREE.MathUtils.randFloatSpread(height) * 2,
    z: -z,
    rotationX: Math.random() * Math.PI,
    rotationZ: Math.random() * Math.PI,
    spinSpeed: THREE.MathUtils.randFloat(8, 12),
  });
```

We'll change the y-rotation directly inside `useFrame`. Let's now change the rotation like this


```javascript
  useFrame((state, dt) => {
    if (dt < 0.1)
      ref.current.position.set(
        index === 0 ? 0 : data.x * width,
        (data.y += dt * 0.75),
        -z + Math.sin((data.z -= 0.01))
      );

    ref.current.rotation.set(
      (data.rotationX += dt / data.spin),
      /*Feel free to experiment with different values to create an interesting pattern*/
      Math.sin(index * 1000 + state.clock.elapsedTime / 10) * Math.PI,
      (data.rotationZ += dt / data.spin)
    );

    if (data.y > height * (index === 0 ? 4 : 1) + 5)
      data.y = -(height * (index === 0 ? 4 : 1) + 5);
  });
```

### Time to ditch the cube

So far we've been using a pretty standard box. But let's be honest.. it's not very interesting. Luckily `react-three/fiber` has support for importing models from Blender. For basic shapes and text you need very minimal knowledge of the program. But if you want to start using more complex shapes and features you might need to optimize models before you use them. If this is the case i recommend watching some Blender tutorials beforehand.

I'll be using text converted to Blender meshes as i find it very easy to work with and it can still create som pretty cool effects. Here's a quick tutorial on converting text to meshes. Remember to export this as a .glTF file. I'll create a curly bracket, normal bracket and angle bracket mesh in Blender.

<iframe width="100%" height="100%" src="https://www.youtube.com/embed/qJOJ9_8UjsQ?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" class="webembed-iframe" style="box-sizing: border-box; border: 0px; --tw-ring-inset:var(--tw-empty, ); --tw-ring-offset-width:0px; --tw-ring-offset-color:#fff; --tw-ring-color:rgba(59, 130, 246, 0.5); --tw-ring-offset-shadow:0 0 #0000; --tw-ring-shadow:0 0 #0000; --tw-shadow:0 0 #0000; display: block; vertical-align: middle; max-width: 100%; position: absolute; top: 0px; left: 0px;"></iframe>

Export your chosen meshes from Blender and put them in a `public` folder at the root level of your project.

Now comes the fun part.

We'll convert our meshes from Blender into React components using a tool called [gltfjsx](https://github.com/pmndrs/gltfjsx)

Use it like this: `npx gltfjsx <mesh-to-transform>.glb --transform`

After it finishes you should have a new .js file generated from this command.

After i used this tool on my curly bracket mesh it generated the following component for me:


```javascript
/*
Auto-generated by: https://github.com/pmndrs/gltfjsx
*/

import React, { useRef } from 'react'
import { useGLTF } from '@react-three/drei'

export default function Model({ ...props }) {
  const group = useRef()
  const { nodes, materials } = useGLTF('/bracket-transformed.glb')
  return (
    <group ref={group} {...props} dispose={null}>
      <mesh geometry={nodes.Text.geometry} material={materials['Material.005']} />
    </group>
  )
}

useGLTF.preload('/bracket-transformed.glb')
```

We'll grab the `<mesh>` component and `const { nodes, materials } = useGLTF('/bracket-transformed.glb')` and insert it into our component. Remember to attach the ref and import useGLTF.

The return function in should look something like this now:


```javascript
  return (
    <mesh
      ref={ref}
      geometry={nodes.Text.geometry}
      material={materials["Material.005"]}
    >
      <meshBasicMaterial
        color="red"
      />
    </mesh>
  );
```

We should replace the `<meshBasicMaterial />` with `<meshStandardMaterial />`. `<meshBasicMaterial />` is self emissive and doesn't reflect light in any way. This makes it very easy to work with but in return it's harder to create a convincing 3D effect. This is why we'll change it to `<meshStandardMaterial />` which reflects light.

To see the effect of this we need to add a light source to our canvas. Inside `<Canvas>` we can add a light source like this


```javascript
  <Canvas camera={{ fov: 45 }}>
      {Array.from({ length: 100 }, (_, i) => (
        <MyMesh key={i} index={i} z={(i / 100) * 10} />
      ))}
      <spotLight position={[10, 10, 10]} penumbra={1} intensity={1} />
    </Canvas>
```

Now things are looking pretty good!

### Final touches

To add some more depth to our canvas we can use the `<EffectComposer>` and `<DepthOfField>` component from `react-three/postprocessing`. Add it inside the canvas like this:


```javascript
/*
You probably want to play around with these numbers a bit to
find what values works best for you.
*/
   <EffectComposer multisampling={0}>
        <DepthOfField
          target={[0, 10, 30]}
          focalLength={1.8}
          bokehScale={11}
          height={1000}
        />
     </EffectComposer>
```

Instead of every element being red we can randomize the colors by extracting all the color presets from three.js. Inside our component lets create a constant for this
`const colors = Object.keys(THREE.Color.NAMES)` and randomly assign our a color to our like this


```
<meshStandardMaterial
          color={colors[Math.floor(Math.random() * colors.length - 1)]}
        />
```

**Nice!** If you've followed along you should hopefully have a pretty cool 3D animated background. From here you can extend it with interactivity, different shapes or something entirely different. I'll post my final product underneath. Sky's the limit as they say.

### CodeSandbox

My final product

<iframe width="560" height="315" src="https://ks9g8c.sse.codesandbox.io/" frameborder="0" allowfullscreen></iframe>