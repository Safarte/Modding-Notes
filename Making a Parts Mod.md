# Preamble
This guide is based on the video: [https://www.youtube.com/watch?v=9fQg-oMqcH8](https://www.youtube.com/watch?v=9fQg-oMqcH8) and on other notes and guidance from the KSP2 Modding Society discord.
# Prerequisites

You will absolutely need the following things at a bare minimum:
- Unity Editor
- ThunderKit
- Unity KSP Tools
- Addressables Package (Install via Unity Window menu > Package Manager
	- Change Packages to Unity Registry
	- Search for “addressables”. Click on it and install.
- LuxShader

You will generally also need the following things:
- Blender (or other mesh modeling SW that can produce FBX files)
- Various blender addons such as TexTools, etc. (make a list of the free ones you’re using)

You probably need a few other things too:
- JSON exports of similar stock parts to show you what some things can or should be set to
# Process
This guide covers the part of the process that takes place in the Unity Editor resulting in an assembly you can load as a codeless mod in KSP2, and how to get that result into the game.
In Unity editor start with a fresh scene. This will hold your entire parts pack.
## Part Pack Prep
1. **Create Unity Project**: Create a new Unity project with an empty scene. You can use the sample scene.
2. **Install ThunderKit** in Unity. See other guides for details.
3. **Install Unity KSP Tools** in Unity. See other guides for details.
4.  Install LuxShader in Unity. Place a copy of “LuxShader” somewhere in your Unity project. I put mine in my Parts Pack folder.
5.  Configure Unity Project: Open the Addressables Window to configure some project properties by clicking the Unity menu for Window > Asset Management > Addressables > Group. Select Default Local Group in Addressables Groups window. In the Inspector window, configure the following properties for your parts pack mod.
	1. Build Path:
	2. Load Path:
6. **Create Parts Pack folder**: Under Assets, create a folder (L-Click Assets: Create > Folder) and name it the same as your parts pack mod. If your mod is called My Awesome Mod then this would be Assets\MyAwesomeMod.
7. **Create Materials folder**: Inside your parts pack folder create a folder called Materials. You’ll be storing the textures and materials you need there. E.g., Assets\MyAwesomeMod\Materials. This is just to aid in organization.
8. **Create Parts folder**: Inside your parts pack folder create a folder called Parts. You’ll be storing the part meshes and related things there. E.g., Assets\MyAwesomeMod\Parts. You can have whatever organization you like here, so if you want to group some parts you might create group folders within Parts (e.g., Methalox Engines, Nuclear Engines, Ion Engines, etc.). This is just to aid in organization and is optional.
9. **Create Plugin Folder and Content**: In your KSP2 install’s BepInEx\plugins folder create a folder for your mod. You can name this whatever you like, but it should be unique. This will be the “mod” players will install to have your parts pack and we’ll refer to it as the Base Plugin Folder in this guide.
a. **Create swinfo.json**: In the base plugin folder create a swinfo.json file with content like this:
```json
{
  "spec": "1.3",
  "mod_id": "com.github.schlosrat.SPARK",
  "author": "schlosrat",
  "name": "Stellar Plasma-Assisted Rocket Kinetics",
  "description": "High ISP engines for your low thrust needs",
  "source": "https://github.com/schlosrat/SPARK",
  "version": "0.0.1",
  "version_check": "https://raw.githubusercontent.com/schlosrat/SPARK/main/SPARK/swinfo.json",
  "ksp2_version": {
    "min": "0.1.3.2",
    "max": "*"
  },
  "dependencies": [
    {
      "id": "com.github.x606.spacewarp",
      "version": {
        "min": "1.4.0",
        "max": "*"
      }
    },
    {
      "id": "lfo",
      "version": {
        "min": "0.2.0",
        "max": "*"
      }
    }
  ]
}
```
b. **Create “addressables” folder**: In the base plugin folder, create an “addressables” sub-folder. This will be where you put the files created by Unity after a build (discussed below). Here’s an example:
```csv
Key,Type,Desc,English

Parts/Title/SPT100,Text,,SPT-100
Parts/Subtitle/SPT100,Text,,Hall Effect Thruster with Xenon Tank
Parts/Manufacturer/SPT100,Text,,"Stellar Plasma-Assisted Rocket Kinetics, inc."
Parts/Description/SPT100,Text,,"The SPT-100 is the pinnacle in tiny (0.625m-class) Ion engines, providing high Isp and low thrust with an integral toroidal xenon tank. Strap this little guy onto a probe core and be sure to bring plenty of electrons because you're in for a long slow ride to anywhere!"

Parts/Title/SPT100,Text,,X3
Parts/Subtitle/SPT100,Text,,Three-Channel Nested Hall Effect Thruster
Parts/Manufacturer/SPT100,Text,,"Stellar Plasma-Assisted Rocket Kinetics, inc."
Parts/Description/SPT100,Text,,"The SPARK X3 is the pinnacle in small (1.25m-class) Ion engines, providing high Isp and low thrust. Strap this bad boy onto your large probe or small capsule, and be sure to bring plenty of electrons because you're in for a long slow ride to anywhere!"
```
c. **Create “localizations” folder**: In the base plugin folder, create an “localizations” sub-folder (NOTE! This is currently required to be exactly this – localizations (plural), not localization (singular). This will be where you put the localization files (e.g., english.csv, spanish.csv, etc.). Localization files are CSV files following a particular format. These must have lines ending with LF not LF/NL, and there are some other restrictions for content, particularly that if you want a string that contains a “,” that string needs to be enclosed in quotes or the comma will mess with how the strings are parsed. These files are where the part’s **Title**, **Subtitle**, **Manufacturer**, and **Description** are configured.

## Part Production

1. **Create Root Part Object**: Create an empty game object under your scene (L-click Scene: Game Object > Create Empty). Name this object the same as your part.
	1. Recommended naming scheme: <mod_name>_<part_name>. If your mod is called “My Awesome Mod” and your part title is “My Part”, then your part name might be my_awesome_mod_my_part for example. Part names must be unique, though you can have any descriptive title you like (that is done later in Localization). A naming scheme like this helps to prevent naming collisions in case anyone else might make a part they want to call “My Part”, like yours.
2. **Create Model Object**: Create an empty game object under your root part object and name this one “model”.
3. **Create Part Folder**: Create a Part folder named for your part inside your Parts folder. E.g., Assets\MyAwesomeMod\Parts\MyPart, or Assets\MyAwesomeMod\Parts\ThisGroup\MyPart if you’re grouping parts.
4. **Bring in FBX**: Drag a copy of your part’s FBX file into the part folder.
	1. If you have baked textures for your part that go with the FBX (meaning, they’re based on the UV Unwrap specific to that FBX), then drag those into this same file with the FBX.
5. **Create Part Object**: Drag a copy of the part FBX from _the Part folder in Unity_ to the _model object created in step 2_. Don’t drag the FBX file from your computer’s files system, you need to use the copy you just placed in step 4. This will create a prefab for your part as a child of the model object.
6. **Unpack Prefab**: Left-Click part object: Prefab > Unpack.
7. **Remove Unnecessary Things**: Remove any parts that came in with the FBX that you don’t actually need in the game like lights, cameras, empty nodes, etc. If it’s not an actual part you want the game to render, then delete it.
8. **Orient Part**: Your model will appear in the Unity scene oriented as you built it in Blender, but this may not be the way you want it to be oriented in the game. If you need changes to the position, rotation, or scale of the part do those now using the Transform panel within the Inspector Window with your part object selected. For example, to flip a part over just give a rotation of 180 in Z, etc.
9. **Create Mesh Object**: L-Click part object: Create Empty. Name this object “mesh”.
10. **Create Collider Object**: L-Click mesh object: Create Empty. Name this object “col”.
11. **Create Collider**: In the Inspector window for the col object click “Add Component”. Search for Mesh Collider and pick it. This will create a Mesh Collider component in the col object. Click the arrowhead to the left of it to expand it and see its properties.
	1. **Select Mesh**: In the Mesh Collider properties select the Mesh you want to use. If your FBX is all one object you can pick that, or you can pick a suitable primitive like cube or cylinder, etc.
	2. **Position, Rotation, and Scale**: Set the position, rotation, and scale of the mesh to encompass the part. You should see a green mesh represented in the Scene window to help guide you to make sure you’ve got the right position, rotation, and scale.
	3. **Convex**: Check “Convex”
12. **Add Core Part Data**: Select your _Root Part Object_. In the Inspector Window click “Add Component”. Search for “Core Part Data” and pick that. Open it up and configure as follows:
	1. **Part Name**: The Part Name needs to be the same as what you’ve used for the Root Part object, i.e., my_awesome_mod_my_part or whatever you used.
	2. **Author**: Use what you like here, typically your KSP Forum screen name or whatever you go by as your modding author name.
	3. **Category**: Select an appropriate category for your part.
	4. **Family**: If you wish to identify a “Family” for your part, this needs to be a particular string. You can find examples in the game’s files, or part JSONS, or ask in the KSP2 Modding Society discord to get this information.
	5. **Co Lift, Co Mass, Co Pressure, etc.**: These parameters allow you to set the Center of Lift, Center of Mass, Center of Pressure, etc. Adjust these to get the markers in the Unity scene where they should be for your part. Typically, Co Pressure and Co Lift are in the same place.
	6. **Fuel Cross Feed**: Check if fuel should be able to transit through your part on the way to other parts. Typically set to true, but not always.
	7. **Mass**: Set this in metric tons, not Kg.
	8. **Attach Rules**: Check the types of attachment your part should allow. Checking “Stack” or “Srf Attach” will allow your part to attach in a stack or to a surface. Checking “Allow Stack”, “Allow Srf Attach”, etc. will allow _other_ parts to stack attach or surface attach respectively. Currently (?) Allow Collision, Allow Dock, Allow Rotate, and Allow Root have no effect in game (check this).
	9. **Attach Nodes**: If Stack is checked above, then you need a “top” and a “bottom” node, if Srf Attach is checked above, then you need a “srfAttach” node. Note, node names are case sensitive and having a node is not enough by itself, you do also need the corresponding Attach Rule set true or the node will have no effect. Under Attach Nodes click the + button to add a blank node and configure as needed.
		1. **Node ID**: “top”, “bottom”, “srfAttach”, etc. (case sensitive!)
		2. **Node Type**: Select as appropriate.
		3. **Attach Method**: Select Fixed_Joint.
		4. Is Multi Joint: In general set to True for stack attach to help prevent noodle rockets.
		5. **Multi Joint Max Joint**: Set to 3 if you set _Is Multi_ Joint to true?
		6. **Position**: Set as appropriate. Should be on the skin or outside of the part where you would expect to find it on your part in the VAB.
		7. **Orientation**: Set as appropriate. The Orientation vector should be a unit vector (length 1) pointing in the direction of the part that will attach to the node, so pointing away from your part.
		8. **Size**: Affects rigidity of your part. If your part is connected to another part with the same “size” node, then rigidity will be optimal, and otherwise it will be suboptimal.
		9. **Visual Size**: Set the same as Size.
		10. **Is Resource Crossfeed**: Set as needed for this node.
		11. **Is Rigid**: Set as needed for this node.
		12. **Rinse and Repeat**: Subsequent nodes created with the + button will inherit settings from the last node made, so this may accelerate the process as you just need to change the Node Id, Position and Orientation for new nodes that are similar to the previous created node.
13. **Add Module_Color**: You need this to be able to paint your part with base and accent colors. As above, click Add Component and search for Module Color.
14. **Add Module_Drag**: _All parts need this_. As above, click Add Component and search for Module Color.
15. **Add other modules as needed**. For example, if your part is an engine you’ll also need:
	1. **Throttle VFX Manager**: Configure as needed (?). No need to drag anywhere, it just need to be a component for the part.
	2. **Flameout VFX Data**: Drag this up to the Flameout VFX property in Module_Engine.
	3. **Module_Gimbal**: Configure details as needed (e.g., Gimbal Range and Gimbal Speed, etc.), then drag this up to the Gimbal property in Module_Engine.
	4. **Module_Generator**: Configure details as needed, then drag this up to the Alternator property in Module_Engine.
16. **Apply Options**: Select the root part and in the Inspector window near the top on the Prefab line, click the Overrides dropdown and pick “Apply All”. If this option is not available, then you’ve got nothing you need to do here. Move along, move along.
17. **Save Part JSON**: Click Save Part JSON button at the bottom of the Core Part Data module. This will put the resulting part JSON in the Assets folder for your Unity project. You need to do this any time you’ve edited the Core Part Data module (or also a module it depends on?).
18. **Make Prefab**: Grab the root part object and drag it to the Unity project Assets folder.
19. **Add Part Icon**: Create an icon for your part that the game will use in the parts picker. This needs to be a PNG file with specific dimensions. It should conform to the style used by other parts in the game. However you do this, you need to name the file <part_name>_icon.png, and you need to drag that file into the Assets folder in Unity.
20. **Make Root Part Addressable**: Select the root part’s prefab in the Assets folder and in the Inspector window check the box for Addressable.
21. **Make JSON Addressable**: Select the part’s JSON in the Assets folder and in the Inspector window check the box for Addressable.
22. **Make the Icon Addressable**: Select the root part’s icon in the Assets folder and in the Inspector window check the box for Addressable.
23. **Configure Addressable Properties**: In the Addressables Groups expand the Default Local Group and find your part.
	1. **Group Name \ Addressable Name**: Change the information in the Group Name \ Addressable Name from “Assets/<part_name>*” to be just “<part_name>*”. So “Assets/<part_name>.prefab” becomes “<part_name>.prefab”, and so forth. You can leave the “Assets/” part of the path definition alone for each of these, that’s as it should be. The value for the Addressable Name needs to be the same as the file name it’s associated with and must not include any path parts. All of these need to be based on the Part Name established in the Core Part Data module.
	2. **Labels**: For the JSON set this to parts_data. Leave it blank for the prefab. If parts_data is not an option in the dropdown for Labels, then click Manage Labels, click the + button to add a new label, and set the Label Name to “parts_data”. Click Save.
24. **Build Parts Pack Addressables**: In the Addressables Groups window, click the drop down for Build and pick New Build > Default Build Script. If you have any unsaved changes Unity will prompt you to save, and then it will build the addressables package for your mod.
25. **Copy Addressables to Plugin Folder**: If the Build step above was successful, the do the following.
	1. **Delete Old Stuff**: In Windows Explorer, navigate to your Base Plugin Folder’s addressables folder (<KSP2 Game Dir>\BepInEx\plugins\<your Mod Name>\addressables) and delete any content inside that folder. There may be four things: AddressablesLink (folder), StabaloneWindows64 (folder), catalog.json (file), and setting.json (file).
	2. **Copy New Stuff**: In Windows Explorer, navigate to your Unity project’s folder and from there to Library\com.unity.addressables\aa\Windows folder. If the build process above was successful, then there will be four things in this folder: AddressablesLink (folder), StabaloneWindows64 (folder), catalog.json (file), and setting.json (file). You will need to copy all of these to your Base Plugin Folder’s addressables folder.
26. **Launch Game and Test**!
27. **Rinse and Repeat for Additional Parts**