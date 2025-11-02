# Unity Demos

A collection of tools and experimentations I've made through the years with Unity game engine.
Just like with a lot of people my introduction with Unity game engine started with an idea of making a simple game.
In the progress of making that game I began to develop tools and experiment with ideas. This eventually lead me to discard the game itself and focus on things that I really enjoyed about the process of making a game, specifically problem solving.
All examples/libraries listed below have no generative AI used in any step/part of their creation.

## Functional Animation

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/ef.gif">

* A high performance "DoBetween" interpolation engine
    - Uses Unitys Burst Compiler and Job System for high performance mass interpolation
* Main focus on custom animation graphs
* You can find the code and better documentation [here](https://github.com/NiklasKiuru/Functional-Animation)

## NPC AI Engine

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/tree_example.gif">

* Stateful behaviour tree desing
    - Instead of computing the entire tree from the root each tick the system can cache the state of the tree depending on the nodes process return flag
    - The tool works fine on simple behaviour modeling but I decided not to develop it further since using it requires quite deep understanding of the control flow visualized below

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/tree_flow.png">

* Tree editor UI
    - Slick IMGUI/UI Elements hybrid UI editor solution based on Unitys Graph Toolkit

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/tree_ui.gif">

## Voxel Framework

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/brick_wall.gif">

* Fully runtime interactable voxel object framework
    - All models are based on a subchunk structure
    - Each model has 16^3 possible subchunks which each of them holding a maximum of 16^3 voxels
    - Empty subchunks do not allocate memory
* Performant shape queries
    - Yes, I really like the burstcompiler
* Model fracturing based on voronoi style cells
    - Projectiles have a set impact radius
    - Upon collision performs a sphere query on the affected voxel object and generates a 3D voronoi lattice within the queried area
    - After lattice creation each voxel will be copied to a new voxel object based on its distance to the nearest lattice seed or placed into a particle que to be spawned as a single voxel
    - The original query is used to void original voxels within the impacted voxel object
    - The fragments are all preallocated and pooled voxel objects
    - All complex physics shapes are created on a separate thread
* Extremely low memory footprint on meshes
    - Since the framework uses Unitys mesh API the voxel data and mesh vertex data must be kept separate and while a voxel can be represented as a single byte the geometry would require way way more than that
    - The solution is to use a greedy meshing algorithm to reduce only visible geometry to 2D planes
    - The entire plane is represented as a 64-bit integer (2 x 32-bit since HLSL does not support them) and decoded by the shader in runtime
    - By comparison the traditional method of using basic vertex attributes would take minimum of 56 bytes per plane
    - The entire system can be made even more performant by removing the mesh API from the equation and issuing the draw command from a single buffer. This removes the necessity of using index buffers completely since all they do is waste memory on non-indexed buffer anyway
    - The main reason for memory optimizations is for one, to allow large number of microvoxels to be rendered and two, to minimize the amount of badwidth used between cpu anf gpu when modifying the mesh data dynamically

## Voxel Terrain Generation

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/terrain_gen.gif">

* Based on 3D simplex noise algorithm and my Voxel Framework
* Can generate worlds with hundreds of millions to billions of voxels
    - Each chunk is generated on the GPU incrimentally
* Custom camera culling and LODs
    - While the used framework is not based on octrees spesifically an implicit octree structure can be inferred from them
    - LODs are generated based on the most common material within a specific level on the octree
    - This is a fairly crude way to generate LODs but it works quite well on simple terrain

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/terrain_lod.gif">

## Ocean shader

<img src="https://github.com/NiklasKiuru/unityExamples/blob/main/Documentation/ocean.gif">

* Simple not so super realistic water shader tiled to create oceans made with shader graph