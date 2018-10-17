# Making your own room for VRChat

A somewhat complete guide for creating your very own room for VRChat

- [Introduction](#introduction)
- [Modeling (Blender)](#modeling-blender)
  - [Blender workflow tips](#blender-workflow-tips)
  - [The basic room layout](#the-basic-room-layout)
    - [UV Mapping the room](#uv-mapping-the-room)
  - [Furniture](#furniture)
    - [Posters on walls (Atlasing & merging)](#posters-on-walls-atlasing--merging)
    - [Mirror buttons](#mirror-buttons)
- [Exporting](#exporting)
- [Setting up the world (Unity)](#setting-up-the-world-unity)
  - [Setting up materials](#setting-up-materials)
  - [Lighting](#lighting)
    - [Night lighting (Skybox)](#night-lighting-skybox)
    - [Light probes](#light-probes)
    - [Final touches to lighting](#final-touches-to-lighting)
    - [Reflection probe box projection](#reflection-probe-box-projection)
- [Readying the world for VRChat](#readying-the-world-for-vrchat)
  - [Scene descriptor & setting up layers](#scene-descriptor--setting-up-layers)
  - [Collisions](#collisions)
  - [Testing the world](#testing-the-world)
  - [Jumping](#jumping)
  - [Grabbable props](#grabbable-props)
  - [Mirror](#mirror)
    - [Mirror button triggers](#mirror-button-triggers)
    - [Testing multiple clients locally](#testing-multiple-clients-locally)
  - [(Grabbable) Chairs](#grabbable-chairs)
    - [Chair Prefabs](#chair-prefabs)
- [Uploading the world](#uploading-the-world)
- [A word on optimization](#a-word-on-optimization)
- [Replacing a mesh with an updated version](#replacing-a-mesh-with-an-updated-version)
- [Conclusion](#conclusion)

## Introduction

Welcome to my tutorial where we will model our very own room and have it uploaded and working in VRChat.
The result will look a little something like this:

![final result](https://i.imgur.com/MbDklAo.png)
![final result 2](https://i.imgur.com/g4rKZO6.png)

## Modeling (Blender)

Starting with the basics, we need some way of creating the room itself. One way is to arrange planes (or quads) together in Unity so they form walls, floor and a ceiling, but that is needlessly cumbersome and wasteful; Unity is not much of a modeling software. For this task we will use Blender, so go ahead and start it.

This tutorial assumes you know your way somewhat around blender, but I'll tell you what operations to perform and what keybinds they have when applicable. If you get stuck on a step, feel free to contact me on discord (Rikard#4777) and I should be able to help.

The default scene of blender contains a 2x2x2m cube, it is a good starting point for a room, but go ahead and move it up one meter so the grid is on the "floor"

![our first cube](https://i.imgur.com/zojxjnI.png)
<!-- <img src="https://i.imgur.com/zojxjnI.png" width=200px> -->

### Blender workflow tips

For more intuitive viewport navigation, i recommend these view manipulation settings

![view manipulation settings](https://i.imgur.com/jpWX79r.png)

Another very useful thing to speed up modeling is the use of pie-menus. With these add-ons you can easily switch between editor modes, shading types and pivot points quickly, instead of having to rely on the dropdown menus or being limited to `TAB` only toggling between edit and object mode.

![pie menus](https://i.imgur.com/c4qJ6O9.png) ![changing modes via TAB](https://i.imgur.com/mWVA4XM.png)

The third tip is to name your objects when you create them, so you don't end up with `Cube.001` `Cylinder` `Cube.002` and so on in your outliner.

![object names](https://i.imgur.com/SOgw4Fw.png)

For this tutorial we will be using Blender with the Cycles engine. We're not really going to do a lot of rendering with it, but the material system and baking in Cycles is simpler and more intuitive to use.

![cycles](https://i.imgur.com/vLiFfQu.png)

The same goes for materials; create or assign materials to new objects as you create them, so you don't have to go through all the objects later to do it. Also helps to give them a distinct color so you can easily tell them apart with material shading on (`Z (pie menu) -> Material`)

![materials](https://i.imgur.com/kXBeXhU.png)

The next tip is universal across blender; if you want to do an operation but are unsure about where the button/menu entry or what its hotkey is, you're likely to find it quickly by hitting `space` anywhere in blender, then starting to type out the name of the operation. For instance, the first step we will take on the cube is to flip its faces so the "front" of the faces face into the room:

![flipping normals](https://i.imgur.com/APzys5h.png)

When we're dealing with normals, it can be helpful to turn on Backface Culling and/or displaying normals, both of which can be found in the right sidepanel (N) of the 3D viewport. Backface culling shows how the model will render in-game with most shaders.

![normal displaying](https://i.imgur.com/TpOBCL1.png)

### The basic room layout

Starting with our cube, we can easily move one or more of the sides, add an edge loop and extrude only a part of it, creating a more interestingly shaped room.

![room basics](https://i.imgur.com/73nQYTq.png)

Since we're making the internals of the room, it can be easier to have a high FOV in the 3D view, we achieve this by changing the Lens angle under View of the right sidepanel (N). A smaller angle means higher FOV.

![fov](https://i.imgur.com/wTCUFu1.png)

Right now is a good time to start applying some materials to the room:
1. Enable face selection mode
2. Select all the faces you want to assign a material to (the floor in our case)
3. Select the material you want to assign
4. Hit "Assign"

![material applying](https://i.imgur.com/rk3X0jI.png)

If you now enable Material shading (`Z -> material`) you should see the materials displaying their diffuse colors. Don't worry too much about the exact colors or properties of the materials, we will set them all up again when we put the entire thing in unity!

![material preview](https://i.imgur.com/fB0e5w5.png)

Use edge loops to your advantage, and add static details to the room like windows and doorframes. Don't worry about adding too many polys to your model, we will simplify it in the next step.

![doors and windows](https://i.imgur.com/yf3TTyE.png)

When you've added some details you might end up with a lot of redundant edges. There's many ways of going about this, and one way is to split based on materials;
1. Select everything with a single material by first making sure nothing is selected, then going to the material tab, selecting a material then hitting the "Select" button
2. Hit Y to split the selected parts of the mesh from the rest of the mesh
3. You can optionally hide (H) the selected mesh if you want to work on different parts of the mesh (to unhide, use Alt + H)

![lots of edges](https://i.imgur.com/034cai8.png)

For instance, here we can dissolve the remnants of the edge loops we added earlier, now part of the wooden frames only (with the rest of the mesh hidden)

![dissolving](https://i.imgur.com/CwJOvAF.png)![result](https://i.imgur.com/BE3gjme.png)

For the ceiling and floor we can select all but the corner verts, then dissolve verts. This will give us a nice L-shaped hexagon, but I like to cut it up (K) into two quads just to be safe.

![ceiling dissolve](https://i.imgur.com/gB8kw8m.png)

Repeat this process for the floor too. For the walls, you might speed up the process by using Limited dissolve, then manually moving and merging verts (select two verts then do `Alt + M`, specifying which vert to merge into (usually the last, or the center)) and cutting up polygons into quads. A good rule of thumb is to make sure everything is a quad or a triangle. Quads for the most part triangulate easily, while things start getting spooky once pentagons and hexagons are involved.

Now would be a good idea to save the blender file, if you haven't already! Blender is not immune to crashes, and they usually happen at the worst times. If Blender does end up crashing, you can usually find the latest autosaved version in your `%temp%` folder (that is, run `%temp%` then sort by date modified to find it).

![quads](https://i.imgur.com/ixS8zmM.png)

#### UV Mapping the room

Since we will be using baked lighting (and also some textures) in the room, we have to create a UV map for the room. We do this by *unwrapping* the mesh into a 2D layout known as the UV Map (U and V are simply the axes for the 2D texture since XYZ are used for the 3D object). Since our floor and ceiling are completely flat separate pieces in the mesh (that is, they aren't connected to the rest of the room) they will UV map just fine without any issues.

To better know how our UVs will behave, we can create a rainbow grid image to display on the mesh while we're UV mapping. Make an image/UV-editor panel and hit the plus button:

![new image](https://i.imgur.com/eEZHGnE.png)

Then make a Node Editor panel and select one of the materials in the mesh (floor in our case). The node editor shows the shader setup, which currently consists of a Diffuse shader with a color. We will just add an image texture node without connecting it to anything. Set its image to the grid image we just created.

![image node](https://i.imgur.com/0UZ9iDc.pngA)![UVdemo](https://i.imgur.com/gvcMlUE.png)

If you now set shading mode to Texture (`Z -> Texture`) you should see the floor (or the material you chose) turn purple-pink-ish. This is because with no UV data, all the verts are just squished into the bottom left corner of the UV map. Go ahead and select the floor, hit U -> Unwrap. You should see the rainbow grid appear on the floor, as well as the L-shaped layout appear in the UV/image editor.

![floor grid](https://i.imgur.com/fbk6WaM.png)

Go ahead and add the same image node to every material by copying and pasting the node (Texture shading mode will show the selected image node in all materials), and then also Unwrap the ceiling just like the floor.

The walls require another step before we can unwrap them. As they are, they are more of a ribbon, and will give very weird results if you just unwrap them directly:

![weird uvs](https://i.imgur.com/5u1ND7E.png)

We have to add a *seam*, which is one or more edges that Blender will allow to be separated in the UV map. We're in luck, as our walls just need one seam, for instance in the corner next to the door. When you then unwrap it, the layout will be a lot better.

![seam](https://i.imgur.com/Rou2Qf4.png)![sensible wall UVs](https://i.imgur.com/rUJYB1G.png)

Now you might be wondering, aren't the cells in the grid pretty big on the walls compared to the floor? And yes, you would be right. By default when UV mapping, blender assumes you want all the verts completely contained within the UV map, and this makes a lot of sense for objects and avatars, where all faces should have a more or less unique position in the UV map. For rooms like this however, it would be really wasteful to try putting the entire room in one texture, as we would need a very high resolution texture to be able to get any fine details on the walls. what you can do is scale up the parts in the UV map so they extend outside the rainbow image. What actually happens in the gray area is that the texture simply repeats, and this can be shown more intuitively if you enable Display -> Coordinates -> Repeat in the right sidepanel (N) of the UV/Image editor.

![repeating UVs](https://i.imgur.com/W6Eah62.png)

Now you can add your favorite repeatable texture to the walls!

![repeating buns](https://i.imgur.com/wwNtOEV.jpg)

The wooden frames around the windows, door and the rest of the room have a more complex shape, having both vertical and horizontal faces. We can still use the same method as with the walls, placing the seams in the corners like this:

![window frame seams](https://i.imgur.com/77gPXXY.png)

If you now unwrap the window frame, you should get a clean and nice result like this

![frame uvs](https://i.imgur.com/9rur5gS.png)

If you get wonky stretching in the UVs, you can try the "Conformal" unwrap method instead, which works well when working with 90-degree angled geometry like this room:

![conformal](https://i.imgur.com/QKb8A9h.gif)

Now, you might notice that some parts of the frame are vertical while others are horizontal. We will want to use a repeating texture with the wood grains either horizontal or vertical, so you should probably rotate parts of the UVs so all of them are aligned the same way. Another word of advice is that the scale of the resulting UVs depend on how many things you are unwrapping at once. 

A good way to make sure everything has the same relative shape is to first mark all the seams, then select all faces with the same material (in this case, the wood material for the frames), then unwrap, rotate  and align pieces , then scale up for the repeating texture.

- you can paint a selection using circle select (C) then selecting the connected verts with Ctrl + L
- to rotate exactly 90 degrees, hit R and type 90 then hit enter
- we don't care much about overlap since we will be using a repeating texture, just make sure everything has the same relative scale

![all frame uvs](https://i.imgur.com/6poAWAu.gif)

As with the walls, you can find your favorite repeating texture and add them to the materials so you can preview and fine tune the scale of the UVs to make sure the details in the texture don't display too large or small on the actual model. For the ceiling and window materials, we don't need textures, as ceilings are usually fairly neutral, and there will be enough detail once we bake the light in Unity, and the window texture will be a simple transparent shader that reflects reflection probes we will put in the room.

![finished room layout](https://i.imgur.com/GuVvsow.jpg)

### Furniture

Now that we have the basic room layout, it's time to start populating it with furniture and other objects. Place the 3D cursor somewhere in the scene, and add a cube (`Shift + A -> Mesh -> Cube`). Name it something descriptive ("Desk" in our case) and assign it some materials (the same wood material as for the frames in our case). Enter edit mode and hit `Numpad /` to hide the rest of the scene while you're working on the cube (hit it again to revert). We will add some UV seams right off the bat, as we will be scaling and duplicating this cube to create a desk with some feet and shelves. Imagine you would make a cube out of paper - a cube has six sides, so you could start with a cross shape consisting of four cells one way, and three cells the other way. If you are good at spatial thinking, you can probably figure out what edges on the cube the seams will map to:

![cube uvs](https://i.imgur.com/GA9yD78.png)

Now just start duplicating (`Shift + D` after having something selected in edit mode), moving and scaling pieces until you have something resembling a desk.

- When moving or scaling you can lock to an axis by hitting the mouse wheel (will lock onto the axis the mouse is moving to), or hitting X, Y or Z
- Snapping modes are useful to align verts or faces with other verts or faces, when combined with axis locking

![desk](https://i.imgur.com/vfXqrYj.png)

Avoid having parts of faces (visibly) overlap with each other, as this will cause the infamous Z-fighting that manifests as flickering in-game. Having the bottom of the feet of the desk overlapping with the floor won't cause any visible issues, but it will take up space in the lightmap, so you should delete faces that would never be visible anyways:

![delete overlaps](https://i.imgur.com/7Kmru36.png)

While you're modeling, it can be very hepful to paste in an avatar to use as a reference for the scale of the room and furniture. (In my case, I'll use [Lexidoll's V3 Nanachi](https://1drv.ms/u/s!Ai6p7Z7l4NEOoQgPmGHubM1wyYKa)) If the room turns out too small, you can always scale it later in unity, or right here and now in blender. 

- To apply scaling that's done in object mode, use `Space -> Apply Object Transform -> check Scale`
- Other transforms can also be applied:
  - if you apply transform, the object will stay in place, but the origin (its local XYZ 0,0,0) will "reset" to the global origin
  - if you apply rotation, the object will keep its rotation, but the local axis rotation align itself to the global axes

![scale guide](https://i.imgur.com/zPthYCT.jpg)

#### Posters on walls (Atlasing & merging)

A lot of VRChat users like to put pictures of memories they've captured in VRChat up on their walls. This is fairly easy - you just have to add a cube, scale it until it's a picture frame, remove the back face, add a new material called Picture, then add a screenshot to the material. Duplicate the picture and create a new material with another screenshot, and so on until you have a wall full of pictures:

![picture wall](https://i.imgur.com/EXT7N4M.png)

The problem here is that we're adding a lot of new objects and materials for something so simple. It clutters up our outliner with a lot of Picture objects, as well as the material list with a bunch of Picture materials. Pictures on a wall don't really go anywhere, so unless you want the ability to move them around later in Unity, there's not much of a reason to keep them as separate objects. We'll join the objects together into one object containing all the meshes, and we'll also create an *atlas* out of all the actual pictures, essentially fitting all the four pictures into one single material and texture. This also means we'll have an easier time in Unity when we add the textures to the materials, as we'll have to add only one texture into one material, instead of *n* textures to *n* materials, where *n* is the number of pictures we had. 

If you have very many pictures on different places, you can split them out to separate meshes again after atlasing, as separate meshes can be used with occlusion culling. For the purpose of this tutorial, we will keep the number of meshes low for simplicity.

1. Create a new rainbow grid image, with resolution of either 2048x2048 or 4096x4096, name it something like "PictureAtlas"
2. Select all your picture objects, then join them together with `Ctrl + J`, rename the object to "all_pictures" so we know what it is.
3. Go to the Data tab (looks like ▽, to the left of the materials tab) under UV Maps, and hit the plus to add a new UV Map, name it something like "Atlas"
4. Select the newly created Atlas map, but make sure the camera icon stays on the original UVMap (the camera dictates what UVMap blender samples from when baking)
5. Enter edit mode, select all the faces with the different picture materials, then hit `U -> Unwrap`
    - if the resulting rectangles in the UV map turn out wider or taller than you would expect, uncheck "Correct Aspect" in the Unwrap tool pane. (This happens if you have a non-square picture assigned to a material, as blender will try matching the aspect ratio of that picture when unwrapping) ![correct aspect](https://i.imgur.com/8aJp9CF.png)
    - Up the margin a bit so the rectangles aren't completely squished together in the UV map, this prevents textures from bleeding into each other, which gets especially noticeable with lightmaps ![picture atlas](https://i.imgur.com/cHgRJqx.png)
6. Now that we have the correct UV layout in the "Atlas" map, we need to bake the images into (and overwrite) the rainbow grid texture we made earlier. To do this, we need to make sure that the rainbow grid image is the active image *in each picture material*. ![picture material](https://i.imgur.com/8LoqZ1M.png)
7. You can verify that all picture materials have the rainbow grid as their active texture by enabling texture shading (`Z -> Texture`) ![rainbow on all images](https://i.imgur.com/GZQri5J.png)
8. If we bake as it is right now, the wooden frame might interfere with the rest of the images, so we just separate out *only the image faces* by selecting them all and hitting `P -> Selection`. This creates a new object with only the image faces, and removes said faces from the original object. Go to object mode and select the new object.
9. Go to the render tab (camera icon) and find the Bake category. Set Bake Type to "Diffuse", and disable "Direct" and "Indirect", leaving only "Color" highlighted, then hit Bake ![bake ready](https://i.imgur.com/eE8yQUL.png)
10. You'll see a progress bar and blender will bake all the different image textures into the single picture atlas texture. When it is done, **make sure to save the image** somewhere. ![save](https://i.imgur.com/FYfJ3lO.png)
11. Join the "picture faces only" object back into the object containing the frames, then delete all but the first of the picture materials. Rename the remaining one to "allPictures" or something equally descriptive, then you can assign the newly created atlas texture as the diffuse color. ![material reduce](https://i.imgur.com/iz1U8bZ.png)
12. ***Delete the original UVMap from the object***, leaving "Atlas" as the only UV map in the object. Having more than UVMap in an object leads to confusion as Unity will use only the first one.

Phew! that was a lengthy process, and you can avoid it entirely if you create your atlas manually in your favorite image editor (since it's essentially just a collage of images) then align the UVs to the atlas. It's up to you how you want to do it, but the main goal is to have *only one* material with *only one* texture for all your posters.

- If you have a computer monitor in the room and you want it to light up, don't include it in the atlas - it will require its own material either way, and saving it as its own texture leads to higher detail, so we can get really close to the monitor in-game and see the pixels.

#### Mirror buttons

Mirrors in VRChat are a fun thing, everyone loves to check out their looks every now and then. Mirrors can be pretty taxing on the performance though, which is why the recommended way to deal with mirrors is to have buttons to enable and disable them, and this is done locally for each user. Some worlds offer mirrors of varying quality - one that reflects everything, and one that reflects just players, and maybe even one that just reflects the local player.

Our mirror will be a simple wooden frame that just has a basic metal shader inside when the mirror is disabled. Next to the mirror is a control panel, with two buttons. We'll have the buttons be separate objects, so that each one will also be a separate object once we're in Unity. We'll make the control panel separate too, just in case we find out we placed it too low, or we want to move it later in Unity.

Now is as good time as any to learn about parents and children. An object can be a parent of children objects, and when you move, scale or rotate the parent, the children will follow along, but not vice versa. Parenting can be used to keep related stuff together (much like families), and makes the outliner less of a vertical mess. The parent-child hierarchy will carry along into Unity, but we're also free to reorganize it later in unity - every object in blender will be a GameObject in unity.

To make an object a child of another object, first select the objects you want to be children, then select the intended parent object last. Then do `Ctrl + P` to parent it, and if you want to keep their relative positions (which you want to 90% of the time), choose "Object (Keep Transform)".
You can also easily parent and unparent by dragging the orange icons in the outliner onto and out of each other, but sometimes the transforms do not carry along in this case.

![mirror](https://i.imgur.com/kWBzyQ9.png)

## Exporting

![blender done](https://i.imgur.com/6FdUno6.jpg)

With our room furnished and pretty, it is time to get it working it Unity. We will export it as FBX (`File -> Export -> FBX`) with the following settings.

- in case you have some things you don't want to export (for instance, remaining avatars you used as a scale reference), you can select all but those, and use the "Selected Objects" checkbox

![fbx settings](https://i.imgur.com/DXPPGUv.png)

## Setting up the world (Unity)

If you've ever uploaded your own avatar, that means you probably have the [right version of Unity already installed](https://beta.unity3d.com/download/9c92e827232b/Windows64EditorInstaller/UnitySetup64-5.6.3p1.exe). In case you haven't, you should probably do that now. To work with Unity, you need to create a new project in case you don't have one already, and then import the VRChat SDK which is fairly straightforward. Once you can see the viewport and the project explorer, you're ready to go.

1. Create a folder called "Assets" if you haven't already. In that folder, we can create "Models" folder, and inside that you can create a very descriptive folder like "my nice room"
2. Drag the exported .fbx straight into the "my nice room" folder
3. Select the .fbx file which now should be visible in said folder, then go to the inspector and check "Generate Lightmap UVs" and hit Apply ![lightmap uvs](https://i.imgur.com/JRUeCOD.png)
4. Create a folder next to "Materials" and call it "Textures", then drop all the textures you will use in it (wall textures, floor textures, wood texture, picture atlas texture, and so on..)
5. Drag the fbx file into the gray empty space in the Hierarchy, you should see the room appear in the scene, and our parent-child hierarchy is intact, sweet! ![room in unity](https://i.imgur.com/xtPg8Jg.png)
6. Save the scene and give it a name! As with blender, you'll want to save regularly.

### Setting up materials

Right now the room looks a bit bland, all materials being a dull gray. We'll go through all the materials, adding textures and changing colors and smoothness.

1. For materials that will have a texture, ***make sure to set the albedo color to completely white*** as this color acts as a multiplier on the texture, dimming it or tinting it when it is not completely white. ![albedo tint](https://i.imgur.com/55h2Y7y.png)
2. Tweak the smoothness slider until the material looks as glossy as it should. If it still looks too glossy, disable "Specular Highlights" under Forward Rendering Options. Keep "Reflections" on, as this will make the material conform to reflection probes.
3. For materials that will be self-lit (like monitors) you should check "Emission" and set a texture or color there
4. For the windows, set the Rendering Mode to Transparent, and set its color to a slight blue/gray-ish with a very low alpha, with maximum smoothness.

### Lighting

![bad lit room](https://i.imgur.com/pwkeD5f.jpg)

Alright, our materials are in place, but the room looks pretty awful - sun is shining through the walls, and everything looks really flat and boring. There are multiple ways to achieve neat lighting, but this tutorial focuses on *baked lighting*. The upsides of baked lighting is that we can very easily create atmospheric environments with realistic lighting and shading with good performance. The downsides are that we have to do some more work to get good lighting on dynamic objects (avatars, moving stuff), since these cannot have baked light. There is a distinct difference between **Static** and **non-static** (dynamic) objects. Static objects are, as you probably could guess, static in the sense that they (should) stay in place.

1. Select the parent object of your room (should be named the same as the .fbx file)
2. Check the "Static" box, and then hit "Yes, change children" so we get all its children set static too. Most of our room will be static objects, and we can always uncheck the static box for individual objects ![to static](https://i.imgur.com/7rwf5x5.png)
3. Unity should suddenly start doing something, and you might see parts of the room go dark, this is intentional.
4. Select the "Directional Light" in the hierarchy, and set its Mode to Baked. Unity will do its thing again (baking the light), and you should see that the room becomes dark, but that the sun shines through the windows

![first bake](https://i.imgur.com/u1rvLJx.png)

5. Open `Window -> Lighting -> Settings` and uncheck "Realtime Global Illumination". Put the Lighting tab somewhere close (e.g. next to the inspector) as we will be peeking in here a lot.
6. Go to Lightmapping Settings and check "Final Gather", unity will re-bake the light and you should see immediate improvements. (notice how the walls get lit up from the bright sky outside) 

![second bake](https://i.imgur.com/krnHPkk.png)

  - Final Gather leads to more accurate results but longer bake times.
  - Right now Unity is baking the light automatically each time something lighting-related (or the position of a static object) changes, which is fine for our small scene, but in larger worlds this can take several minutes. If you want to bake the lighting on your own volition, uncheck "Auto Generate" at the bottom of the lighting panel.

7. You'll notice that some materials "reflect" the skybox, or look overly matte. Since we don't have any reflection probes (often called cubemaps) in the room, they can only reflect the skybox. Right click the parent object (world_tutorial in our case) and add a `Light -> Reflection Probe`. Position it somewhat in the middle of the room, and you should see the materials soon turn a lot more realistic.

![reflection probe](https://i.imgur.com/KVBC9ao.png)

8. Our room is still pretty dark, even though it has four windows and the sky is bright outside. You can tweak the environment lighting in the Lighting panel with the Intensity Multiplier slider. Set it to 2 for instance, and observe that the room becomes a bit brighter.
9. You can also adjust the intensity of the sun (Directional Light) as well as its Indirect Multiplier. You can keep the intensity at 1 while sliding the indirect up, if you want more light to bounce into the room while keeping the light spots on the floor from going nuclear. Here's an example of the environment indirect set to 2 and the sun's indirect set to 10

![indirects](https://i.imgur.com/1uuPf8L.png)

#### Night lighting (Skybox)

The world already looks good for a daytime scene, but in this tutorial we will go for a night scene. We'll create a skybox from a spherical (equirectangular) panorama image, in this case `night_mountains.png` which (if you have SteamVR installed) can be found in `steamapps\common\SteamVR\resources\backgrounds`

1. Drag `night_mountains.png` into the Texture folder
2. Set the Texture Shape to "Cube" and hit Apply
3. Create a new material in the Material folder, name it something like "night sky"
4. Drag the night_mountains.png into the Cubemap slot
5. Drag the skybox material into "Skybox Material" in the Lighting panel, Unity will rebake the light
6. Adjust the parameters of the Directional light so it fits with a night sky
   - rotate the light itself to control where the light shines into the room
   - simulate moonlight by setting the color to a light blue-purple-ish
   - reduce the indirect multiplier a bit in case the room gets too illuminated
   - up the Baked Shadow Angle to get smoother shadows

![night room](https://i.imgur.com/IzNBJej.png)

Nice! we have a night room, but it is a bit too dark, let's add some lamps!

1. Add a pointlight to the lamp (or anywhere if you don't have any lamp), position it nicely under the shade
2. Set shadow type to Soft Shadows, this will give you a preview of the shadows
3. Set the Mode to Baked
4. Adjust the Baked Shadow Radius for softer shadows (think of it as the radius of the lightbulb, e.g. 0.05)

![lamp room](https://i.imgur.com/E9gq8r2.png)

People looking at themselves in the mirror would like to be able to see themselves, so let's add an area light (effectively an invisible one-sided plane of light) somewhere near the mirror. Area lights have their intensity spread across the plane, so it helps to set the intensity a bit higher for them. Here's an area light with an intensity of 10

![area light](https://i.imgur.com/4yByPBW.png)

Our room is L-shaped, so we can have three reflection probes to up the accuracy of the reflections a bit.

![three probes](https://i.imgur.com/vZvNsxE.png)

#### Light probes

Our lighting looks pretty cozy already, but there is one more important step we need to do. Remember how baked lighting only affects static objects? We can place some spheres down to see how a dynamic object would look in this room.

![no probes](https://i.imgur.com/hzLIcwA.png)

Surprise surprise, they all look the same no matter where they are, and it looks as though they're basing their lighting off of the skybox. In fact, this is *exactly* what they are doing. Our scene does not have any *light probes* yet, but there is always a hidden ambient light probe based on the skybox. Let's add some light probes!

If you add a light probe group, you by default get eight probes positioned like a cube. You might assume that it could work to just make a grid of light probes throughout the room, and while you'd be correct, it is very wasteful. The main idea is to place light probes strategically, placing them closer to each other where the lighting changes a lot in a short distance, and fewer of them where the light is more uniform.

1. Place a light probe group, and move it somewhat to the center of the room
2. In the inspector, press "Edit Light Probes" to enter the edit mode
   - In the edit mode you can select individual probes or drag a box to select
   - When probes are selected you can move them with the gizmo
   - You can add, delete and duplicate probes from the buttons
3. Select the top four probes and delete them
   - I like to place probes near the floor first using the overhead view, then duplicating and moving upwards later
4. Move the bottom probes near the floor, and use the overhead view to move them around horizontally.
5. Check how your lighting looks every now and then by moving spheres around
   - Notice how an object always samples from four light probes arranged in a tetrahedron ![tetrahedron](https://i.imgur.com/R7JowhD.png)

![light probe result](https://i.imgur.com/GwDQfPi.png)
![actual probes](https://i.imgur.com/K5CdKZy.png)

Light probes can take a while to get right, but it is well worth it, as it prevents avatars from looking out of place (being either too bright or too dark or wrongly tinted)

- notice how more probes are placed near the lamps and especially under and over the desk 
- if there hadn't been probes under the desk, objects would look about as bright as they would on top of the desk
- there's less probes in the areas of the room further from the lamps, as the lighting doesn't change a lot there

#### Final touches to lighting

Our lighting is pretty much complete, but we can tweak settings to further improve the visual quality of the light. If you zoom in you might see that shadows might appear blurry on objects, or the lighting might be bleeding in from edges. There are a lot of parameters you can tweak when it comes to lightmapping, but the most dramatic one is the Lightmap Resolution, which is at 48 by default. You can for instance set it to 96:

![lightmap resolution](https://i.imgur.com/Ufa5KDF.gif)

Another way to fix light bleeding is to look in the actual lightmaps themselves, by going to "Object maps" in the lighting panel and selecting "Baked Intensity" from the dropdown. You can now select objects in the scene and have their meshes highlighted in the lightmap.

![light bleed](https://i.imgur.com/xjbMcX5.png)

In this case, Unity packed the meshes too tightly in the lightmap, which resulted in a bit of light bleeding somewhere it shouldn't have. You can increase the margin of this by selecting the .fbx from the project folder and then in the inspector, show the Advanced settings under "Generate Lightmap UVs". You can increase the Pack Margin (default is 4) to 16 for example.

![increase margin](https://i.imgur.com/ZdX2CpP.png)
![bleed gone](https://i.imgur.com/y6hjWzz.png)

#### Reflection probe box projection

You can make the reflections in the mirror more realistic even when the mirror isn't active, by enabling Box projection on individual reflection probes and then tweaking the box size and offset until it looks passable.

![box projection](https://thumbs.gfycat.com/UnpleasantAromaticFlies-size_restricted.gif)

## Readying the world for VRChat

So far we've made a nice little scene in Unity, but how do we actually get it into VRChat? There are two basic essentials needed; a scene descriptor (much like an avatar descriptor, but for worlds) and some collisions, so people don't just fall through the floor in an endless loop. I told you earlier to grab the VRChat SDK if you hadn't, and now we will put it to use.

### Scene descriptor & setting up layers

1. Search up "VRCWorld" in the project explorer and drag the prefab into the hierarchy
2. Position the VRCWorld object where you want players to spawn
   - By default, the VRCWorld has a scene descriptor with one spawn point: its own transform
3. Drag "Main Camera" into the Reference Camera slot in the scene descriptor inside VRCWorld
   - Filters or settings applied to this camera will be what players see in-game
4. Position the VRCWorld where you want players to spawn
   - Players will be facing towards the Z (blue) axis of the VRCWorld
5. In case you will have grabbable props in the world, you probably want them to respawn in case someone sticks their hand through a wall and drop a prop into the void. To achieve this, set "Object Behavior At Respawn Height" to **Respawn** instead of Destroy.
6. To set up layers for VRChat, go to `VRChat SDK -> Show Build Control Panel` and press the button to set up layers. We'll use layers to make some objects walk-through while still being solid for props.

### Collisions

Since we had the basic room layout as a separate object back in blender, we can simply apply a mesh collider to it, and everything will work as expected. You can see the wireframe of the collider if you temporarily uncheck the Mesh Renderer of the room

![collider mesh](https://i.imgur.com/wUfth4N.png)

- We can use the same method to get collisions for the other objects in the room, but for some objects it might be more sensible to add one or more Box Colliders instead of using the actual mesh.
- Think about how you would like your world to behave before you add colliders to furniture. Some worlds have collisions on furniture that *isn't* in the walkthrough layer, which makes people unable to lean over tables without their avatar either freezing in place or them suddenly popping up on the table.
- A method that works well in practice is to add colliders to furniture, but putting the furniture in the Walkthrough layer so that players are unaffected by the collisions, but we can still place props on the furniture.
- For more complex worlds, it might help to create (in Blender) a separate, simplified mesh of the walls, floor and ceiling to use as a mesh collider.

![walkthrough desk](https://i.imgur.com/l9zJLwD.png)

### Testing the world

Now that we have added the scene descriptor and some collisions, our world will already work in VRChat! Go to the build control panel and press "New Build" under Test. The world will build and VRChat will launch, loading you into a local test version of the world.

![test](https://i.imgur.com/w0NKn79.png)

### Jumping

You'll notice that you cannot jump in your room, this is because jumping is not on by default.

1. Add a `VRC_Player Mods` to VRCWorld, check `isRoomPlayerMods`, then hit Add Mods
2. Select jump from the dropdown (selected by default) then hit Add Mod
3. Adjust the jump power if needed

### Grabbable props

Say we want to make one of the chairs grabbable and have them react somewhat accurately to physics. We'll need to add colliders and some scripts to a chair.

1. Disable the "Static" checkbox for the chair, this will cause Unity to re-bake the light, and our chair will now be lit by our light probes.
2. For collisions, we could use a Mesh collider, but it would have to be set to **Convex** (non-convex mesh colliders can only be static), which is essentially a shrinkwrapping of the mesh. This would not be ideal for a chair, as we would be unable to place other physics props on the seat: ![convexhull](https://i.imgur.com/8IfHifu.png) A better option is multiple box colliders on the chair
3. Add four box colliders and edit them until they wrap the feet of the chair, add two more to the seat and backrest and repeat the process for them: ![boxcollider](https://i.imgur.com/NWzbxTb.png)
4. Add a `VRC_Pickup` script to the chair, and check "Allow Manipulation When Equipped" to make it more fun for desktop players with gamepads.
5. Add a `VRC_Object Sync` script to keep the prop synchronized between clients. If you don't do this, props will appear for you to just remain on the floor when other players pick them up.
6. Test the world and observe that you can stick the chair into places like you would expect.

![chaircollisions](https://i.imgur.com/gsjZ1N8.png)

### Mirror

Time to make the mirror into an actual mirror. We will make two different mirror versions; a regular mirror that reflects everything, as one would expect, and a second mirror that only reflects players. We will hook up the two buttons on the control panel to toggle on and off the mirrors.

1. Search up `VRCMirror` from the project explorer and drag the prefab into your mirror object
2. Position and rotate the mirror until it fits snugly inside your mirror frame, making sure it extends a little bit into the frame so the mirror doesn't appear pasted on top
3. Make a duplicate of the VRCMirror and call it VRCMirror_low, this will be the player-only mirror
4. Uncheck the "UI" and "UiMenu" layer in the regular mirror so the mirror won't reflect the UI. Set the layers for the low mirror to Nothing, then select only "Player" and "MirrorReflection" (Player is other players, MirrorReflection is the reflection of your own player)
5. Uncheck (disable) both mirrors, as we would like them to start disabled, and be opt-in when needed.

#### Mirror button triggers

1. Add a `VRC_Trigger` and a Box Collider to the mirror button.
2. In the trigger script, add a new `OnInteract` and change from `AlwaysBufferOne` to `Local` as we would like players to toggle the mirrors only for themselves.
3. Set the interaction text to "toggle mirror (local)" or something similar
4. Add a new `Basic Events -> SetGameObjectActive` action
5. Select that action, and add a receiver slot
6. Drag VRCMirror into the slot
7. Select the VRCMirror receiver and set the operation to Toggle
8. Repeat steps 4-7 for VRCMirror_low, but set the operation to False
   - the idea is that once we press the regular mirror button, we can always just disable the player-only mirror regardless if it was disabled already or not

![mirror triggers](https://i.imgur.com/7V9TRPp.png)

9. Repeat steps 1-8 for the second mirror button, but make sure the interaction text specifies that it is a player-only mirror, and make sure that it is the VRCMirror_low that gets toggled this time, and VRCMirror only gets set False

#### Testing multiple clients locally

It would be nice to double check that our mirrors are working as expected, without having to upload the world and have a friend come check it out. That's what the "Number of Clients" field is for in the Test category of the build panel. Set that to 2 and two vrchat windows will load up. Your clone will be displayed as a blue loading avatar (anything else would've caused a paradox)

![mirror demo](https://thumbs.gfycat.com/SecondhandSnoopyHarborseal-size_restricted.gif)

### (Grabbable) Chairs

We put some chairs in the room, but we can't actually sit down on them yet (unless you have the $300* DLC that is full-body tracking), we first have to add some *stations* to them.

<sub>*: also comes in cheaper variants</sub>

We can drag in the VRCChair prefab and study the contents to figure out what an interactive chair needs:

- **Enter Point**: a transform dictating the position of the seated player. Z (blue) arrow is the direction the player is facing and X (red) points horizontally to the player's right.
  - Note that when this position is at floor level, that means the avatar's feet will reach the floor, regardless of avatar size. This is the reason why small avatars will clip through regular chairs.
- **Use Text Point**: a transform where the tooltip text will be shown
- **Exit Point**: a transform dictating where the player will appear when exiting the chair. Z (blue) arrow is the direction the player will be facing and X (red) points horizontally to the player's right.
  - This transform is a bit above floor level on the default prefab
- **VRC_Station**: the script that configures how the chair should behave
  - For moving (e.g. grabbable) chairs we'd want to set Player Mobility to `Immobilize for vehicle`
- **VRC_Trigger**: An `OnInteract` `AlwaysBufferOne` trigger that sends a `UseStation` RPC to itself when pressed.

We can replicate the setup for our own chair, but first we should add a child object of the Chair and add a box collider to it. We'll add the station script and trigger to this child object, so as not to interfere with the physical chair object.

- We'd end up grabbing the chair as we sit down and flinging ourselves into the ether if they were on the same object
- Make the box collider a bit fat so it is easier to interact with trough the box colliders of the chair
- You can also put the station script on the chair itself, and have only the trigger and collider on the the child

![chairsettings](https://i.imgur.com/moCCUBl.png)
![chairs](https://i.imgur.com/e8Lnfwd.jpg)

#### Chair Prefabs

Now that we've made a grabbable chair, we can make a prefab out of it. This is as simple as just dragging the object from the hierarchy into a folder in the project explorer. Now we can create new grabbable chairs easily by just dragging that prefab into the viewport directly, and placing it on the floor somewhere.

- You might need to adjust the scale of the chair (or switch to a smaller/larger avatar) to avoid sitting in the air or inside the chair...

![whoops](https://i.imgur.com/z0HLoOt.jpg)

## Uploading the world

You're probably eager to invite all your friends to your cozy room, and to do that we'll have to upload the world to VRChat. In the build control panel you can find similar buttons under Publish as under Test. "Last Build" will upload the last build of the world (useful in case you just finished a testing session and want to upload right away), or uploading a new build.

1. Save the scene & project (Unity sometimes crashes when attempting to upload worlds)
2. Hit either Last or New build
3. Give the world a name
4. Set the player capacity
   - For a room this small, don't make the capacity too large or people will assume your room surely fits that many players and everyone will end up talking over each other
5. Give the world a description, this will be shown on the world details page as of the most recent VRChat version (2018.3.3), as well as count when searching up worlds.
6. Check any content warnings, if applicable
7. Check the last box to certify that your information is accurate
8. Hit Upload

## A word on optimization

Our world is fairly small, so we've mostly optimized what could be optimized so far. We've kept the number of meshes low, which isn't always a good thing when it comes to worlds. For larger worlds, like houses with many different rooms, we'll want to use occlusion culling to avoid having to render other rooms we cannot see. Occlusion culling works by not rendering meshes that are hidden, and thus it benefits from a scene that is divided into smaller meshes rather than the entire house being one single monolithic mesh. Our world is just one room, so we can safely keep the walls, floor and ceiling as one mesh.

## Replacing a mesh with an updated version

Say you're tired of the back wall being so bland, and you'd like to knock down a part of the wall to build a cozy place for a bed. For this, we'll head right back to Blender and open our world file there.

1. Save a copy of the blender file (e.g. my_room_v2.blend)
2. Start editing the room mesh to your liking

![cut wall](https://i.imgur.com/wAJmDWR.png)

3. Fix up any distorted UVs (you'll probably want to unwrap entire materials again so you get the relative sizes correct)

![fixed uvs](https://i.imgur.com/0MXMQWw.png)

4. Select only the changed mesh(es) and export selected as a new FBX
5. Drag this fbx into the same project folder in unity as our original FBX
6. Check "Generate Lightmap UVs" in the import settings
   - You should probably set the Pack Margin to the same as our original fbx, if you changed that
7. Find the object corresponding to the room mesh in the hierarchy. Inside it, you will see that the original "room" mesh is used in the **Mesh Filter** and the **Mesh Collider** component 

![meshes](https://i.imgur.com/aipitvz.png)

8. Click the little right arrow to expand the contents of our new FBX, and drag the new room mesh into the Mesh Filter and Mesh Collider 

![new mesh](https://i.imgur.com/pz0BhyS.png)

9. Wait for the new lightmap to bake, and hey presto! our room is extended.
10. Don't forget to add more light probes and maybe a new reflection probe to the new area.

![nice result](https://i.imgur.com/FGp01OA.jpg)

11. Add some spotlights (we don't care that the light source comes from thin air) and a bed for extra coziness. The back wall is boring no more!

![bed!](https://i.imgur.com/aYV6b78.jpg)

## Conclusion

That's that, then. You should now know enough to create at least a cozy little place for yourself and your friends. When you're ready for your world to go public, you can fill in the form (all relevant info about the public world process can be found on the [VRChat discord](https://discord.gg/vrchat)).

VRChat is a place for exploring, hanging out and having fun. Happy world building!
