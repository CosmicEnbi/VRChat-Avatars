# VR Chat Avatar Development Notes

## Getting Started

To create custom VRChat avatars you'll need a modeling program such as the popular Blender or an application like VRoid Studio, we'll be using Blender. To configure and upload avatars for VRChat you'll need Unity game engine. We'll also need some plugins/add-ons for both programs.

### Setting Up Unity

1. Download and install [Unity Hub](https://unity.com/download)
2. Get the right version of unity. VRChat only supports a very specific unity version at a time. A link to the currently supported version can be found on the [VRChat Guides](https://docs.vrchat.com/docs/current-unity-version)
3. Activate a free Unity license in Unity Hub `Settings > License Management > Activate New License`
4. Create an open a new unity project in Unity Hub `Projects > New Project`
5. Download the latest VRChat avatar SDK from the [VRChat website](https://vrchat.com/home/download)
6. Import the SDK into your Unity Project via `Assets > Import Package > Custom Package`

### Setting up Blender

1. Download and install the latest version of [Blender](https://www.blender.org/)
2. Create new general blender project
3. Download the [CATS](https://github.com/absolute-quantum/cats-blender-plugin) add-on for blender as a zip file. This add-on is an absolute life saver in helping to prepare avatars for uploading to VRChat. Pretty much every step of this process will use some function from CATs.
4. Install the plugin by going to `Blender > Edit > Preferences > Addons > Install` and selecting the zip file
5. Refresh your add-ons list and check the `Cats Blender Plugin` item to enable it for this project
6. In the `Layout` view a new tabbed item `CATs` should appear in the toolbar along the right side of the viewport. Try pressing `N` if you don't see this toolbar.

### Obtain or Create a Custom Avatar

- Its a shit ton of work to build a full custom avatar
- I'd recommend [Aggrotard](https://www.youtube.com/c/Aggrotard) and [Rainhet Chaneru](https://www.youtube.com/c/RainhetChaneru) on YouTube for instructional videos on avatar modeling
- [PHIA](https://www.youtube.com/c/TheVirtualRealityShow) of The Virtual Reality Show also has some good resources on working with VRoid models
- For completed avatar models, items, and avatar parts try searching [VRCModes](https://vrcmods.com/) and [Deviant Art](https://www.deviantart.com/)
- If you're looking to start with just a body and add custom clothes/hair, the search terms `MMD base` and `TDA base` may be helpful
- Models seems to commonly distributed as either full Unity packages or FBX models
  - Unity packages can be directly imported into Unity `Assets > Import Package > Custom Package`
    - From here you can upload to VRChat or find the avatar file in the unity project folder and export it for modeling in Blender
  - FBX files contain the meshes and materials for an avatar and can be imported right to Blender for modeling
    - In Blender `File > Import > FBX file`

## Blender Items

### There's Holes in my Avatar

- If the mesh faces have face normals pointing inwards they may be culled
- Try recalculating the face normals
  - Select Mesh
  - Go to modeling view
  - Select faces
  - `Mesh > Normals Recalculate`
- If this doesn't fix the problem just turn off back face culling in the material options

### Exporting model

- Use CATs tool to merge all meshes into one (Is one the right number?)
  - Save a copy of your blender file
  - Run `CATs > Model Options > Join Meshes > All`
  - If you want to undo in the future you can run `CATs > Model Options > Separate By`
- Use CATs tool to atlas your materials into one `png` file
  - Run `CATs > Optimization > Atlas`
  - You may need to install the additional [material combiner](https://github.com/Grim-es/material-combiner-addon) addon
  - Save this atlas to a file and remember its location
- Run CATS `Fix Model`
- Run CATs `Export Model` to save `.fbx` file
  - If CATs complains about excessive polygons see [Model Size and Decimation](#model-size-and-decimation)
  - For a final export `CATs > Bake` is a more advanced tool and may be more appropriate
    - I have had some trouble getting baking to work correctly. It seems that if some portion of your model does not have a UV map CATs throws and error
    - A workaround is to set `Overlap Fix` to `Reproject` and have CATs regenerate the UV maps at the cost of reducing quality
    - For me this resulted in an unacceptable level of mesh problems so I have been manually decimating my meshes
- After exporting your model I highly recommend exiting Blender WITHOUT SAVING
- If you save your Blender file at this point you will loose easy access to the separate meshes and materials since they have all been merged and atlased, this can make it difficult to make further changes later.
- As such I would make merging and atlasing the last steps before exporting and follow the steps `Decimate > Save > Mesh Merge > Atlas > Export > Exit w/o Saving`

### Model Size and Decimation

If either CATs export or VRChat's upload tools complain about your avatar having too many polygons, you have two options

- Option A: Ignore it and export/upload anyway, your model may challenge people's framerates or be hidden by some users safety settings
- Option B: Reduce your polygon count
  - The easy options are deleting unused meshes, including meshes like skin which appear under clothes
  - If your polygon count is still to high you can reduce the resolution of your model
    - A quick way to do this is to run `CATs > Decimation`
- For more granular control you add perform decimation on each individual mesh. This lets you preserve details on some mashes (with the hands and face being particularly import) and drop the detail on other meshes (such as clothing items which are often provided with tens of thousands of faces)
  - To decimate a mesh, select it then in the bottom right click `Modifier Properties > Add Modifier > Decimate`
  - Use `collapse` to merge vertices of organic shapes, `un-ubdivide` for textures made of neatly divided polygons, and `planar` for reducing the number of polygons on planar surfaces
  - The `ratio` will set what fraction of the polygons to keep (computed based on the number of triangles so may be imprecise for other face shapes)
  - Toggling the computer icon will show/hide the effects of decimation in the editor and can be useful to check for damage such as holes that may arise from excessive decimation
  - At present VRChat requires < 70,000 polygons for Poor or better performance on desktop and < 32,000 polygons for Excellent The latest performance thresholds and numbers for the Quest can be found on the [VRChat Docs](https://docs.vrchat.com/docs/avatar-performance-ranking-system)

## Unity Items

### Importing Model

- After setting up project
- Import `fbx` model by  dragging into the assets panel
- Place the model in the world

### Getting shaders

- Download the zip file for the [Poiyomi Toon Shaders](https://github.com/poiyomi/PoiyomiToonShader)
- Import the Unity package via `Assets > Import Package > Custom Package`

### My Textures Are All White

- This is normal, don't panic. Or do, sometimes its fun.
- Extract the model materials
  - Select the model
  - Go to the materials tab in inspector view
  - Click `Extract Materials` and save to a new folder named `Materials`
- Configure the extracted materials
  - Create a `Textures` folder and drop in the atlased texture `.png`
  - Find and select the extracted material file
  - Set the shader to the Poiyomi shaders
  - Click the dot next to the `Main Texture` and select the atlased texture png

### My Textures Have Holes

- If the mesh faces have face normals pointing inwards they may be culled
- By default Unity likely culls back faces while Blender does not
- You can try correcting the mesh normals in Blender
- If this doesn't work adjust the culling setting in the shader
- Select the material with holes, set `Rendering Options > Cull` to `Off`

### Rig Your Avatar

- Select the model
- Under the inspector select `Rig` and set the animation type to `Humanoid`
- For `Avatar Definition` select `Create From this Model` and click apply
- You should now see a `Configure` button under the `Avatar Definition` dropdown
- Click configure to open the rigging interface
- In the rigging interface, under the `Inspector` view
  - Check that the major avatar bones are correctly mapped
    - In particular under the `Body` view everything except `Upper Chest`, and `Toes` should be mapped to a bone
    - If your avatars major bones are misconfigured go back to Blender, make corrections and rerun CATs tools
  - In the `Head` view ensure the eyes are correctly mapped and that the `Jaw` is unset (this can be done by selection None)
- If you avatar is not in a T-pose
  - Scroll to the bottom of the `Inspector View`
  - Click `Pose > Enforce T-Pose`
- Save the rig and exit, `Apply > Done`

### Add a VRChat Avatar Descriptor

- Select the model in the scene hierarchy
- Click `Add Component > VRChat Avatar Descriptor`
- Configure the avatar view point
  - Under `View > View Position` click `Edit`
  - These settings determine where the player characters camera is located in game
  - Position the small white ball directly between the avatars eyes with half the sphere clipped into the face
- Setup lip sync
  - These settings allow the avatars mouth to animate to match your speaking
  - Under `LipSync` click `Auto Detect!` and hope it works
  - You should a list of viseme syllables and associated blend shapes
  - If it doesn't work try going back to Blender and running `CATs > Visemes > Create Visemes` again
- Enable eye look
  - These settings control naturalistic eye animations to give your avatar life
  - Select `Eye Look > Enable`
  - Select the left and right eye bones, usually named `LeftEye` and `RightEye` by CATs
    - Configure the eye look ranges
      - Under `Eyes > Rotation States > Looking Up` click `Preview`
      - Rotation handles will appear on each eye bone, use them to set the maximum upwards looking eye angle
      - If the handles don't control the eyes, try switching the selected eye bones
      - When done press `Return`. Repeat for each eye direction.
    - Configure the eye look intensities
      - The Calm/Excited slider controls how often your character blinks
      - The Shy/Confident slider controls how often you look at other players and for how long
      - For blinking to work you need to configure an eyelid bone or associated viseme
      - I will update with instructions once I learn how to do this
- For `Playable Layers` click `Customize` use the defaults, I'm not sure how this works yet
- Under `Expressions` click `Customize`
  - If your avatar came with custom expressions and parameters files select them from the list
  - If not select the default files
  - I haven't tested this and can't guarantee it will work correctly

### Test Your Avatar

- In the top bar select `VRChat SDK > Show Control Panel`
- Sign in with your VRChat account
  - Must be `New User` trust level or above to upload content
  - Steam and Oculus accounts cannot upload content, try creating and merging a VRChat account
- Under `Builder` see what your warning and errors are
  - Many can be auto fixed directly from this menu
  - Errors
    - "This avatar does not contain an animator..."
      - You forgot to rig your model! See steps above
    - "This avatar has mipmapped textures without..."
      - Use auto fix
      - If this doesn't work, find and select the offending texture
      - Under `Inspector` check the box under `Advanced > Streaming Mipmaps`
  - Performance warnings
    - Too many materials? Atlas your materials
      - In Blender run `CATs > Optimization > Atlas`
      - You may need to install the additional [material combiner](https://github.com/Grim-es/material-combiner-addon) addon
    - Too many polygons? Back to Blender and run `CATs > Decimation`
- Once successfully built your avatar will appear in VRChat the `Other` section of your avatars menu
- The test avatar will be visible only to you, other players will see your last used avatar
- You can manage test avatars under the `Content Manager` tab
  - Make sure to run `Fetch` to update the listing

### Debugging Your Avatar

So you're testing your avatar but things are off, try the steps below

- My avatar is floating or its feet are clipping through the floor
  - "Try respawning that's a neat trick!"
  - Still weird? Double check that you've set your real world height correctly
    - In VRChat open the main menu
    - Got to `Settings > User Real Height` and check the value is correct
  - If your still having problems you may have set the location of avatar viewpoint incorrectly
    - This step is done back in unity when you add the VRChat avatar descriptor
    - See step three of the associated section above
- Part of my models texture's aren't appearing in game
  - Assuming the associated textures actually exist they may be being unwantedly culled because the shader thinks they're internal faces
  - See the [blender holes](#theres-holes-in-my-avatar) and [unity holes](#my-textures-have-holes) sections
- My mouths not moving correctly
  - Check you have the right mic set
  - If so you're visemes might be incorrectly configured
  - Check the "lip sync" item under the [avatar descriptor](#add-a-vrchat-avatar-descriptor)
  - Check that the syllables are correctly matching
  - If not try regenerating your visemes `Blender > CATs > Visemes`

### Publish Your Avatar

- Assuming your avatar has been tested and functions to your liking
- In the top bar select `VRChat SDK > Show Control Panel` then `Build and Publish for Windows`
- This will launch a game window and start running a debug version of your unity scene
  - A new camera named `VRCCam` will be added to your unity scene
- Set you avatar name, description, content warnings, and sharing settings
- To change the display image of your avatar
  - Return to your scene and change the location of the VRCCam element
  - You may need to click `Upload Image` to update the picture
- Upload your avatar!
- Once synced to the servers your avatar will appear in VRChat under a `Your Creations` tab in the avatars menu
