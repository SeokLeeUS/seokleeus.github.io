
### Things to learn:

This is a minimum to learn in order to carry Unreal Engine(UE)/C++ related project work 
(work in progress to add reference materials, demo codes, and reference):

- Visual Studio (VS) and Visual Code IDEs (Integrated Development Environment) set up, some additional steps to expand features, and its associated useful shortcut keys
- User Interface (UI) set up- i.e. HUD
- Debugging and its tip
- Object Oriented Programming (OOP)
- Class inheritance 
- Handful tips to operate Unreal UI and BluePrint(BP) interfaces 
- Access modifier (private/public/protected)
- Architect and refactor the code 
- Polymorphism 
- Handling static mesh (LOD, Lighting, Shadowing) ,scene, actor,collision
- Version control (Unreal/VS/github)
- C++ syntax @ low and high level (pointers and references, type casting, macro,  etc.) and Unreal coding standard
- Handling camera control 
- Reference materials (C++ Unreal API reference) 
- Unreal naming convention 

### Here are learning material links:

1.	Unreal BP has a built in feature to shoot out box shape ray tracing for debugging purpose. [This](https://api.unrealengine.com/INT/BlueprintAPI/Collision/BoxTraceByChannel/index.html) is the official material from Epic. A [video](https://www.youtube.com/watch?v=QRk4_43tgS8) demonstrates how to set up Box trace by channel. 

2.	In order to apply a function globally available to other BPs globally, there’s a solution called [BP function library](https://docs.unrealengine.com/en-US/Programming/BlueprintFunctionLibraries/index.html).

3.	 In order to make parameters available on other BPs globally, there’s a [solution](https://answers.unrealengine.com/questions/235471/where-can-i-set-a-global-variable-that-can-be-adju.html) to make the variables available via a BP actor. 

4.	This [demo](https://www.youtube.com/watch?v=5DK8jF1ygeM) walks you through how to set ray tracing using BP. 

5.	[MathWorksSimulation plugin](https://www.mathworks.com/matlabcentral/fileexchange/65966-vehicle-dynamics-blockset-interface-for-unreal-engine-4-projects) allows you to transfer ray tracing information to Simulink for co-simulation.  Check the compatible Unreal version for plugin installation. 

6.	There’s a [radar HUD demo](https://github.com/SeokLeeUS/RadarHUD/blob/master/README.md) which you might find some insights for different HUD implementation. 

7.	For ray tracing, [collision type setting](https://www.youtube.com/watch?v=XmSnzDIKfTw) is important for other players. Please see this detailed information.

8.	 This [reference](https://wiki.unrealengine.com/Blueprints,_Creating_C%2B%2B_Functions_as_new_Blueprint_Nodes) explains how to make C++ class accessible by BP. 

9.	This is the [form](https://epicsupport.force.com/unrealengine/s/) when you want to report a UE bug to Epic games. Before submitting a bug report, you can search [here](https://issues.unrealengine.com/) if your issue is already reported by someone else. 

10.	In order to utilize input command via keyboard or mouse to move the player, consider this [material](https://www.unrealengine.com/en-US/blog/input-action-and-axis-mappings-in-ue4).

11.	 This [material](https://docs.unrealengine.com/en-US/Engine/Rendering/LightingAndShadows/Lightmass/Basics/index.html?utm_source=editor&utm_medium=docs&utm_campaign=doc_anchors) explains about lighting property setting. 

12.	When you want to know about the class inheritance hierarchy, please check [Unreal official documentation](https://api.unrealengine.com/INT/API/Runtime/Engine/PhysicsEngine/UPhysicsHandleComponent/index.html). For instance,  you will see the inheritance at the top header file. 

13.	[This](https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/TArrays/index.html) does explain array container in UE. You can find some insight how the array works in BP from this documentation as well. 

14.	[This](https://udn.unrealengine.com/questions/509566/view.html) describes how to remove unwanted C++ class from unreal project. 

15.	When you build code from VS, check [this](https://udn.unrealengine.com/questions/509520/view.html) out first. 

16.	This describes [directory structure](https://docs.unrealengine.com/en-US/Engine/Basics/DirectoryStructure/index.html). The bottom line is, you can remove build/binary/intermediate/saved folders when you recreate visual studio code file. 

17.	This describes how to [control viewport](https://docs.unrealengine.com/en-US/Engine/UI/LevelEditor/Viewports/ViewportControls/index.html). 

18.	This does describes UE [gameplay framework](http://www.tomlooman.com/ue4-gameplay-framework/). 

19. [This](https://raw.githubusercontent.com/github/gitignore/master/UnrealEngine.gitignore) shows a good example of .gitignore which works for Unreal file structure. 
