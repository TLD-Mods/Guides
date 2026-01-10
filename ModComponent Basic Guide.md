# How to implement items in TLD using ModComponent
![IdeaToResult](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/IdeaToResult.png)

## Objective of this guide

This guide will teach the basics on how to implement your own items in The Long Dark using [ModComponent](https://github.com/dommrogers/ModComponent). This guide will not teach you how to make your own 3D models, textures or visual assets you might need.

If you wanted to create 3D models of your own, however, it's highly recommended to use [Blender](https://www.blender.org), as is free to download on Steam and has a ton of tutorials on the internet. 

If you want to borrow free 3D models, [Sketchfab](https://sketchfab.com/feed) is a good source, but remember to only use models marked as Creative Commons and always ask for permission first.

More resources at the end of this guide.

If you need help, tips or any kind of advice, you can ask us modders in the [TLD Modding Discord server](https://discord.com/invite/nb2jQez).

## First things first

Before even thinking about making your own modded items, you will need to install the software neccesary to make them work. 

If you're reading this guide, you probably have already played The Long Dark with mods, but in case you haven't, the instructions on how to install mods can be found on [TLD Mod List](https://xpazeman.com/tld-mod-list/install.html) page.

ModComponent and its dependencies are required for this guide, so be sure to have them in your mods folder. You can find those [here](https://xpazeman.com/tld-mod-list/index.html).

You will also need to install [Unity](unity.com), as it's the engine TLD uses. It's highly recommended to install it along Unity Hub.

For the JSON creation and editing required, any text editor is fine as long as it can export to JSON. In this guide, however, we will be using [Notepad++](https://notepad-plus-plus.org/downloads/).

As of version 2.17 of TLD (Frontier Comforts), you will need:
  * [MelonLoader 0.6.1](https://github.com/HerpDerpinstine/MelonLoader/releases/latest/download/MelonLoader.Installer.exe)
  * [Unity 2021.3.6](https://unity.com/releases/editor/archive)
> Bear in mind that mods are currently only compatible with legal Windows versions of the game, purchased through GOG, Steam or Epic Games Store (You'll need a compatibility plugin for the latter).

## 1. Concept

In this guide we will introduce a basic item, Plant Nutrients (borrowed from Indoors Greenery), that acts as a material for other crafting recipes. This kind of objects are labeled in-game as GEAR_*ItemName*, which means it's an item owned by the player. To achieve this, we will go through the following steps:
 * Adding model and textures to Unity.
 * Set GEAR_*ItemName*, material, icon and crafting menu icon.
 * Export as a bundle using Addressables (Unity Package).
 * Setting an Automap file and a Localizations file (two JSON files that contains how the item behaves and how it's called and what description have while we are playing).
 * Adding crafting recipes and spawn files.

## 2. Workflow

Half the work when making mods is keeping everything organized and somewhat usable. A good thing to do is keeping everything in separate folders. For the purpose of this guide, we are going to work with a folder, in which there's a hierarchy that looks like this:

![Hierarchy1](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Hierarchy1.png)

 * **Dummy_Mod**: This folder is where the ModComponent files are located. This should be called as whatever you want your mod to be named.
 * **Unity**: This folder is where the Unity project files are located. This can be called just Unity or, ideally, something related to your mod.
 * **Working**: In this folder will be located every visual asset you are working with.

The Unity folder will be automatically organized once you create the project, and the organization of the Working folder is yours to decide. The Dummy_Mod folder, however, **MUST** contain these inside: 

![Hierarchy2](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Hierarchy2.png)

 * **auto-mapped**: This folders contains the automap files, JSON files that defines how the items works and behaves in-game.
 * **blueprints**: Self explanatory, this folder contains the blueprints, also JSON files.
 * **bundle**: In this folder will be located the bundle files obtained from Unity.
 * **gear-spawns**: Also self explanatory, this folder contains txt files that determines where items should spawn.
 * **localizations**: Contains a JSON file with the name and description in-game of the items.
 * **BuildInfo (Optional)**: A JSON file containing your mod's info. Will be explained in this guide.

These folders/files **MUST** be named this way, or else your mod will not work.

## 3. Unity Editor

Once you have installed Unity Hub and the required version listed above, go on and start a new project (3D). Once the project is opened, there will be a dialog box. Click on "Skip New Version" right away.

### 3.1. Basic editor knowledge

If you have experience with Unity, you can skip this part. If you don't, however, when you open a project, you will see something like this:

![UnityEditorGuide](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/UnityEditorGuide.png)

* **1. Scene:** This is the scene, where you can drag and drop models, prefabs, etc, to see how it looks. 
* **2. Inspector:** This is the inspector, where you can see info, components and important data about the asset you have clicked on.
* **3. Project manager:** This is where you can see folders and assets currently in the project.
* **4. Hierarchy:** This shows the objects located in a scene, or inside a prefab, and how they relate to each other.

And, for the purpose of this guide, a brief explanation of terms we will be using while assembling our mod in Unity:
* **Asset:** Files inside the project we are working with. This can include 3D models, textures, scripts, etc.
* **Game Object:** Anything that is in the scene or prefab hierarchy. Composed by one or more assets. Cameras, lights or models with textures and collisions are examples of Game Objects.
* **Prefab:** It's a group of Game Objects, usually composed by several 3D models, that it's normally used to assemble in-game items, decorations, etc.
* **Mesh:** Wireframe of a 3D model, or the model without any material on it.
* **Component:** Add-on inside a Game Object that determines how it behaves, how it looks or what its position/rotation is.
* **Collider:** Component that determines the collision of a Game Object, or the area you can interact with.
* **Material:** Asset that contains the texture of a 3D model. You can edit it through variables and sliders to your liking.

### 3.2. Setting up Unity
Following our **"keep everything clean and organized"** approach, everything has to be in different folders. The *Assets* folder in your project should look like this:

![AssetFolder](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/AssetFolder.PNG)

These folders, although not always required depending on the mod you're making, should always be named this way. Here's a quick rundown on what they're for:
* **ClothingPaperDoll:** This folder contains images of what your clothing items (like a jacket) should look like on your character (clothing menu). These two subfolders, Male and Female, determines how it should look on Will or Astrid.
* **CraftingIcons:** This is where the blueprint icons that display in the crafting menu are located.
* **InventoryGridIcons:** This folder contains the icons of the items, the images that should display when you have items in the inventory,
* **Models:** You should put here the 3D models of your items. There's 2 subfolders inside, one for the textures and one for the materials of the items.
* **Prefabs:** The prefabs that contains the finished items, with its models, colliders and materials, should go here.

Once all of the folders are created, head to **Window > Package Manager**. This window will show up:

![PackageManager](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/PackageManager.PNG)

* **1:** Head to the *Packages* menu and click *Unity Registry*. This option by default is *In project*
* **2:** Search *Addressables*.
* **3:** Install.

Addressables is a package used inside The Long Dark to short resources by type, making everything convenient and usable, so we must adapt to this in orther to add new items.

## 4. Making GEAR_*ItemName*

When the assets have been imported into the project, it's time for us to start setting our item.

### 4.1. Prefab

Go to the **scene hierachy**, then **right click > Create Empty**.

![EmptyGameObject](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/EmptyGameObject.PNG)

This action will create an empty Game Object that we will use to create the prefab of our item. Rename that Game Object to GEAR_ + the name of your item, in this case: *GEAR_PlantNutientsCrafted*.

The name of your item should be as descriptive and "specific" as possible, as you probably want your mod to coexist with other mods and don't cause compatibility issues. This [list](https://github.com/TLD-Mods/Data/wiki/Modded-gear-items), although it might be not complete whenever you check this guide, contains GEAR_ names of a few mods, so you might want to check it to see if your names conflict with those from other mods. 

When the renaming is done, **drag the Game Object into the Prefab folder**. The Game Object in the scene will now look like this:

![PrefabInScene](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/PrefabInScene.PNG)

This means the Game Object is now considered a prefab by Unity. **Double click the prefab in the Project Manager** to open the prefab editor. You will know you're inside the editor because you will see a grid over a blue background in the scene preview window.

Always check the **Transform** component of your GEAR_ Game Object, as it can have some offset upon creation. Set everything to 0.

**Drag and drop the mesh** into the prefab hierarchy, making it a "child" (dependant) of the GEAR_ Game Object, wich is now the "parent" object. This will create a Game Object within our "parent" GEAR_ with a **Mesh Filter** and a **Mesh Renderer** components inside. These two determine how the item looks and it's where you can change the mesh and material. The transform here is also important: However the mesh is positioned here, it will affect how the item looks in game, so if it is rotated awkwardly or with a huge offset... funny placements will happen.

![PrefabEditor](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/PrefabEditor.PNG)

As the last step, we will add a **collider** to the "parent" object, clicking **Add Component**, then searching collider. This can either be a **Box Collider** or a **Mesh Collider**. Other 3D colliders are fine too, but these are the most widespread options.
* Box colliders are simpler and less resource heavy, but also less accurate. You will have to edit them manually **using the "three dots" button**.
* Mesh colliders are more complex and take more memory, but they're more accurate, as they adapt to the mesh. Click the **Convex checkbox** and Unity will automatically create the collider for you.

Whether the collider is box or mesh is completely up to you. Use them as you see fit.

### 4.2. Material

Once the prefab is set, it's time to create the material for our item. Go to the Materials folder, then **right click > Create > Material**. The name of the material is not important, but ideally you should properlly name them. When you click on the material, the inspector will show you this: 

![MaterialInspector](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/MaterialInspector.PNG)

This menu shows you the material settings that you can edit however you like but, for most cases in The Long Dark, we will only need to make two simple changes:
* Set the **Smoothness** slider to 0, so the item don't have weird reflections in-game.
* Change the **Albedo**. You can change it by **clicking the small dot** next to the name, then searching for your texture. You can also tint the texture with the color picker, where white is the default.

Drag it now into the "child" object in the prefab.

![MaterialOnObject](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/MaterialOnObject.PNG)

If your mesh have "submeshes" inside (is set to include more than a single material), go to **Mesh Renderer > Materials** and **click the "+" button** in the list. As we did for the texture, click the dot then search for the materials you want to add.

### 4.3. Icons

We will obviously need the inventory icon and the craft menu icon. These are always **512x512** and **MUST** be named **ico_GearItem__*ItemName*** and **ico_CraftItem__*ItemName***, always with **two** underscores between the type of icon and the name of the item, which needs to be the same as the GEAR_ name. In this guide, we will name these *ico_GearItem__PlantNutrientsCrafted* and *ico_CraftItem__PlantNutrientsCrafted*.

For these you will need some editing knowledge (or sometimes drawing knowledge for the crafting icons). Fortunately, we can make these easily following these steps:
* Open the prefab editor and deactivate the grid. It's located in the **upper bar of the editor window**.
* Rotate the camera, get the angle you want, and use the Windows **snipping tool** (or something similar) to get the image.
* Open a project in the image editing software of your liking, open a 512x512 canvas and paste your image.
* Cut the blue background. For the craft icon you can paint everything white, and leave the inventory icon like that.

You can edit them more, maybe add some noise filter or shadow, but they'll do the trick without. Here are the Plant Nutrients icons as examples:

![Icons](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Icons.png)

## 5. Addressables 

When the prefab is all set, is time for us to move on to the next step: **Addressables.**

**Addressables** is a Unity package that is used for asset organization and management. TLD use this package to short objects by type: animals are *WILDLIFE*, items are *GEAR*, things you interact with are *INTERACTIVE*... you get the idea.

We are going to use this package because we want the game to recognise our items as such.

The first step would be set a bundle group. Select the folders where the assets you want to export are in, or in this guide example, **CraftingIcons**, **GridInventoryIcons** and **Prefabs** folders. **Only export those folders that have the final assets.** Then go to the bottom of the inspector, where you'll see **Assetbundle**. Click in the *None* just next to it, then click **New**. 

![BundleInspector](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/BundleInspector.PNG)

When **New** is clicked, Unity will let you write where the *None* was before. Write a name that represents your mod. In this case, we will use **PlantNutrientsIG**. Once you have done this, upon selecting any of the folders you selected in the previous step, that corner should show the name we just wrote. 

After this, go to **Window > Asset Management > Addressables > Groups**. This should open the following tab:

![Addressables1](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Addressables1.PNG)

Click the button, then **Convert**. That tab should now look like this:

![Addressables2](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Addressables2.PNG)

**Right click** on the group, then click **Simplify Addressable Names**. All names should look like the ones we writed in the first place. This will make our assets recognisable by the game. If we don't do this step, the game **will not recognise them.**

Now go to **Window > Asset Management > Addressables > Profiles**. This will open a tab like this one: 

![Addressables3](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Addressables3.PNG)

In the *Local* section, instead of **Built-In** select **Custom**. Both paths should be editable now. Change both of those for **.\\AssetBundles\\** (Dot included). When built, a folder with that name will be created, and our bundle will be exported there.

Go to **Window > Asset Management > Addressables > Settings**. This will open the settings on the inspector: 

![Addressables4](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Addressables4.PNG)

* **1. Player Version Override:** Write here the name of your bundle, or anything related to your mod. **PlantNutrientsIG** in this case.
* **2: Shader Bundle Naming:** Instead of *Project Name Hash*, select **Custom**. This will open a writting space just under it. Write there the same as what we did in the last step.
* **3: Build Remote Catalog:** Select the checkbox then, instead of *Remote*, choose **Local**

Go back to the Addressables Groups window (*Window > Asset Management > Addressables > Groups*), click on the group and this will show in the inspector:

![Addressables5](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/Addressables5.PNG)

Go to **Bundle Naming Mode** and select **Filename**.

With all of these steps done, you can go back to the groups tab, then **Build > New Build > Default Build Script**. This will automatically generate all the bundle files in our folder, just created, **AssetBundles**. If you want to add more items later, drop the assets in the group, simplify addressable names and build again. 

## 6. ModComponent configuration

This is the last step before introducing our items into the game, but it's as important as the rest. In our **Asset Bundle** folder, there should be the following files now: 

![AddressablesFilesOutput](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/AddressablesFilesOutput.PNG)

Copy the **catalog JSON** file, the **assets_all .bundle** file and the **unitybuiltinshaders** file. Do not copy the .hash file. Paste those in the **Bundles** folder we created in step 2.

### 6.1. Automap file

Now it is time for us to create the **ModComponent automap** file of our item. Automap files, as briefly explained in step 2, are JSON files that define how our item will behave in game. In this guide we will go through the very basics of how these works. Open the text editor of your liking, in this case we will use Notepad++, and paste the following:

```json
{
    "ModGenericComponent": {
                                "DisplayNameLocalizationId" : "GAMEPLAY_SampleItem",
                                "DescriptionLocalizatonId" : "GAMEPLAY_SampleItemDescription",
                                "InventoryActionLocalizationId" : "GAMEPLAY_SampleItemAction",
                                "WeightKG": 0.1,
                                "DaysToDecay" : 0,
                                "MaxHP" : 100,
                                "InitialCondition" : "Perfect",
                                "InventoryCategory" : "Auto",
                                "PickUpAudio" : "",
                                "PutBackAudio" : "",
                                "StowAudio" : "Play_InventoryStow",
                                "WornOutAudio" : "",
                                "InspectOnPickup" : true,
                                "InspectDistance" : 0.4,
                                "InspectAngles" : [0, 0, 0],
                                "InspectOffset" : [0, 0, 0],
                                "InspectScale" :  [1, 1, 1],
                                "NormalModel" : "",
                                "InspectModel" : ""
                            }
}
```

**ModGenericComponent** is the most basic of the types of items we can create, as it contains all the code every item need. It's good for materials and items with no special functionalities. Here is a brief explanation of the lines of this code:
* **DisplayNameLocalizationId, DescriptionLocalizatonId and InventoryActionLocalizationId:** These three point to the Localization file we will create later, one for the **display name** of the item, another for the **description** of the item, and the last one for the **action** button in the inventory, like *drink* or *use*. The first two **MUST** always be named **GAMEPLAY_*ItemName*** and **GAMEPLAY_*ItemName*Description**. The last one, if your item is a material or a simple item, can be omitted, like in this case. 
* **WeightKG:** Pretty self explanatory. Determines the weight with a float (decimal) number.
* **DaysToDecay:** In-game days that it takes for our item to reach 0% condition. 0 here means that the item **won't decay**.
* **MaxHP:** Life points of an item. This **does not** change maximum condition %. If you put here, let's say, 5, 5 will still be 100% condition. This is only relevant when coding with these items.
* **InitialCondition:** This determine the condition % in which our item will spawn. Can either be `Perfect`, `High`, `Medium`, `Low` or `Random`. This will not affect the condition % when you craft that item (if it's craftable).
* **InventoryCategory:** Tab of the inventory in which our item will be shorted. Options are `Clothing`, `FirstAid`, `Firestarting`, `Food`, `Material` or `Tool`. Can be `Auto` too, but that will make the item go into the "general" tab of the inventory. It doesn't matter the real type of your item. You can have a food item in tools section if you like.
* **PickUpAudio, PutBackAudio, StowAudio and WornOutAudio:** Pretty self explanatory. Determines the audio of our item in these cases. Write the name of the audio call in these. You can leave the StowAudio as it is here. Most up to date audio list [here](https://github.com/dommrogers/TLD-Assembly-Archive/blob/master/2.17/call_analysis_cs/AK/EVENTS.cs).
* **InspectOnPickUp:** Determine if the inspection will shown when picking up the item.
* **InspectDistance, InspectAngles, InspectOffset and InspectScale:** These are used to change how our item will be displayed upon inspection. Distance determine how far or close the item is from us, Angles set the ratation (X, Y, Z), Offset determine horizontal, vertical and depth (X, Y, Z) positions and Scale changes the model's size. These will require a lot of fine tunning. Unity will be of great help to figure these out.
* **NormalModel and InspectModel:** If we want to change how these look, we need two different models in the prefab's hierachy, defined in step 4.1, then put the names of those models in these 2 lines.This guide's example does not use this feature, but we can see an example on the following GIF:

![GIF](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/ModelsExample.gif)

### 6.2. Behaviours

Behaviours are an optional part you can add to your item's automap file. These allow you to add extra functionalities, like making your item stackable, or burnable in a fire. These are simple and require less code lines, but there is quite a few of them and there's no room in this guide to explain them all. Instead, you can take a look at the [ModComponent Documentation](https://github.com/dommrogers/ModComponent/tree/master/docs) docs, with all the information necesary to make all types of items and understand how behaviours really work. However, we will add two of these to our Plant Nutirents: **StackableBehaviour** and **HarvestableBehaviour**, that will allow us to stack the item and break it down to recover some of its materials.

Here is the full code for the Plant Nutrients: 

```json
{
    "ModGenericComponent": {
                                "DisplayNameLocalizationId" : "GAMEPLAY_PlantNutrientsCrafted",
                                "DescriptionLocalizatonId" : "GAMEPLAY_PlantNutrientsCraftedDescription",
                                "InventoryActionLocalizationId" : "",
                                "WeightKG": 0.2,
                                "DaysToDecay" : 0,
                                "MaxHP" : 100,
                                "InitialCondition" : "Perfect",
                                "InventoryCategory" : "Material",
                                "PickUpAudio" : "Play_SndInvGenericTiny",
                                "PutBackAudio" : "Play_SndInvGenericTiny",
                                "StowAudio" : "Play_InventoryStow",
                                "WornOutAudio" : "",
                                "InspectOnPickup" : true,
                                "InspectDistance" : 0.4,
                                "InspectAngles" : [0, 0, 0],
                                "InspectOffset" : [0, -0.1, 0],
                                "InspectScale" :  [1, 1, 1],
                                "NormalModel" : "",
                                "InspectModel" : ""
                            },
    "ModHarvestableBehaviour": {
                                "Audio" : "Play_HarvestingPlants",
                                "Minutes" : 10,
                                "YieldCounts" : [1],
                                "YieldNames" : ["GEAR_BarkTinder"],
                                "RequiredToolNames" : []
                            },
    "ModStackableBehaviour": {
                                "SingleUnitTextId" : "GAMEPLAY_PNCSingle",
                                "MultipleUnitTextId" : "GAMEPLAY_PNCMultiple",
                                "StackSprite" : "",
                                "UnitsPerItem" : 1,
                                "ChanceFull" : 100
                             }							
}
```

Once you have defined the automap file, save it as a JSON in the **auto-map** folder we created in step 2. The name of this file **MUST** be the name of the item with no capital letters and no prefix. Using our example, **GEAR_PlantNutrientsCrafted** automap file should be called **plantnutrientscrafted**.

### 6.3. Localization file

The localization file, that should be just called **Localization**, is a JSON that contains all the displayed text for our mod's needs. It defines names, descriptions, buttons... everything that have text in-game. You can set these to support diferent languages too. You can find [here](https://github.com/dommrogers/ModComponent/blob/master/docs/Localizations.md) the list of languages supported and how to write them in the file. If it doesn't recognise a language, it will always default to English, so be sure you always define English localization first. Here's how the Plant Nutrients localization file would look like, both in English and Spanish: 

![LocalizationFile](https://github.com/TLD-Mods/Guides/blob/main/IMGS/ModComponentTutorial/LocalizationFile.PNG)

### 6.4. Blueprints

Blueprints can be an esential part of a mod if it relies heavyly on craftable items. Using ModComponent's sibling mod, [CraftingRevisions](https://github.com/dommrogers/CraftingRevisions/), we can easily add these in the game. Current CraftingRevisions format looks like this: 

```json
{
    "Name": "PlantNutrientsCrafted",
    "RequiredGear": [{"Item": "GEAR_BarkTinder", "Count": 2}],
    "KeroseneLitersRequired": 0.0,
    "GunpowderKGRequired": 0.0,
    "RequiredTool": "",
    "RequiredCraftingLocation": "Anywhere",
    "RequiresLitFire": false,
    "RequiresLight": true,
    "CraftedResult": "GEAR_PlantNutrientsCrafted",
    "CraftedResultCount": 1,
    "DurationMinutes": 15,
    "CraftingAudio": "Play_HarvestingPlants",
    "AppliedSkill" : "None",
    "ImprovedSkill" : "None"
}
```
Although quite self-explanatory just reading the names, you can read more about how these parameters work [here](https://github.com/dommrogers/ModComponent/blob/master/docs/Blueprints.md), but bear in mind that this documentation have changed, so **RequiredGear** and **RequiredGearUnits** have been fused in a single line, as shown above.

### 6.5. BuildInfo file (optional)

This file is a completely optional JSON file, as described in step 2. It defines some very basic info of your mod, like version number and author name. This is really helpful while testing, because you can see if your mod is loading or not. This is an example of a BuildInfo file, borrowed from the parent mod of Plant Nutrients, Indoors Greenery: 

```json
{
  "Name" : "Jods' Indoors Greenery",
  "Version" : "2.1.0",
	 "Author" : "Jods-Its"
}
```

### 6.6. Making .modcomponent file

The last step is making the file ModComponent is going to read while starting the game. It is always made using the parent folder that contains auto-map folder, bundle folder, etc. We have two options to achieve this:
* We can use a compression tool, like [7-Zip](https://www.7-zip.org), to compress the parent folder, then rename the file extension **.zip** into a **.modcomponent** extension.
* We can use [ModComponent Extractor](https://github.com/ds5678/ModComponentExtractor). By dropping the parent folder into the .exe, the extractor will automatically make the .modcomponent file for you.

Once done, you can drop the file into your mods folder, as you normally would with any other mod.

## 7. Testing your mod

For this step, you will need [Developer Console](https://github.com/FINDarkside/TLD-Developer-Console) mod.

Enter the game. Always check if MelonLoader console throws any errors and, if it does not and you have followed every step correctly, your mod should work fine. Enter an already existing save or create a new one, then press **F1** key. This will open the dev console. Use **add + the name of your item** to get the item in your inventory. Drop it, check how it looks in inspection, look at the icons, search in the crafting menu... make sure everything looks and works as intended. If it doesn't, you can always go back to the previous steps and add some fine tunning. 

If, for whatever reason, MelonLoaders log console throws some red text while loading your mod, and you are not sure how to deal wit it, you can contact us modders through the [TLD Modding Discord server](https://discord.com/invite/nb2jQez) and we can help you with troubleshooting.

## 8. Finishing touches

With your mod working, you may want to add your item in some places of the game or to some containers. You will need [Coordinates Grabber](https://github.com/ds5678/Coordinates-Grabber) mod for this, as it let you grab the position information of an item, copy the scene name in which you're located and, if you are looking at a container, what loot table it has. Usage intructions can be read in the mod repository.

Spawn files are simple .txt files that goes into the **gear-spawns** folder. These files are defined by ModComponent's sibling mod [Gear Spawner](https://github.com/dommrogers/GearSpawner), and they look like this:

```
scene=FarmHouseA
item=GEAR_PlantNutrientsCrafted p=3.1299,0.8833,7.1645 r=0.0000,282.6049,0.0000 c=80

loottable=LootTablePlasticBox
item=PlantNutrientsCrafted w=4
```

First example is defining an item in a specfic location and position. C here means **chance**, or % of spawn.

Second example is adding the item to the loot table of a container. W here means **weight**, or probabilites of the item to appear as loot from a container.

While doing this, it's heavily recommended to use [Placing Anywhere](https://github.com/Xpazeman/tld-placing-anywhere), as it let you rotate the item as you like.

## Wrapping up

Congratulations! You're now good to go to continue your modding journey on TLD.

For any suggestion, feedback, fixes or even support you want to add/need, feel free to contact me at any time, and I will try to give you an answer as soon as possible ^^.

## Resource List
* [ModComponent Documentation docs.](https://github.com/dommrogers/ModComponent/tree/master/docs)
* [ModComponent Documentation GitHub IO.](https://ds5678.github.io/ModComponent/)
* [Sound list (TLD v2.17).](https://github.com/dommrogers/TLD-Assembly-Archive/blob/master/2.17/call_analysis_cs/AK/EVENTS.cs)
* [Blender Guru](https://www.youtube.com/user/AndrewPPrice/videos)
* [Sketchfab](https://sketchfab.com/feed)
* [CGTrader](https://www.cgtrader.com/free-3d-models)
* [Turbo Squid](https://www.turbosquid.com/Search/3D-Models/free)
* [Free3D](https://free3d.com)
* [Unity Asset Store](https://assetstore.unity.com)
