<a name="top"></a>

<br/>
<div align="center">
  <h1 align="center">Camereon Model Tracker</h1>
  <h3 align="center">Real-time 6-DOF pose tracking of rigid objects based on CAD models and monocular images</h3>
  <h3 align="center">( <a href="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/README.md">English</a> / <a href="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/README_CN.md">中文</a> )</h3>
</div>
<br/>

<!--
[TOC]
-->
- [Introduction](#introduction)
  * [Theory](#theory)
  * [Features](#features)
  * [Demo](#demo)
  * [Scenes](#scenes)
  * [Implementation](#implementation)
- [Preparation](#preparation)
  * [Device Support](#device-support)
  * [Software](#software)
  * [Installation](#installation)
  * [Model Preparation](#model-preparation)
- [Usage](#usage)
  * [ARFoundation Version](#arfoundation-version)
  * [MRTK Version](#mrtk-version)
  * [Meta Version](#meta-version)
  * [Public APIs](#public-apis)
- [License](#license)
- [Contact Information](#contact-information)
<br/>


## Introduction

### Theory
&emsp;&emsp;This algorithm is used to calculate the 6-DOF pose, including translation and rotation, of a 3D object relative to a monocular camera in real time, which can be used in the fields of augmented reality, robotic grasping, and target tracking etc.. It is similar with [VisionLib SDK](https://visionlib.com/) of Visometry Company and [Vuforia SDK - Model Target](https://developer.vuforia.com/library/objects/model-targets) of PTC Company.

### Features
- Only CAD models and monocular color images are used. 
- Stable and fast 6DOF pose tracking @60FPS. 
- Automatic features detection and update during tracking process. 
- Improved robustness against cluttered background, partial occlusion, and fast motion. 
- Certain self-recovery capabilities after tracking failure. 
- Enhanced robustness with the integration of SLAM. 
- Supports offline AI pre-training on CAD models, enabling automatic initialization from arbitrary viewpoints and significantly enhancing self-recovery capabilities.
- Cross-platform supported.

### Demo
&emsp;&emsp;[VIDEO : Without AI pre-training](https://www.bilibili.com/video/BV1s3411Z7ac/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf) 
&emsp;&emsp;[VIDEO : With AI pre-training](https://www.bilibili.com/video/BV1sSVozpEXF/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf) 

### Scenes
&emsp;&emsp;This algorithm is mainly applicable to (but not limited to) texture-less scenes that natively have CAD models, such as the industrial field. Meanwhile, given its dependence on edge features, the geometric structure of the object and the number of edges will have a certain impact on the tracking effect, so the model and parameter selection should follow [suggestions from VisionLib](https://docs.visionlib.com/v2.4.1/vl_unity_s_d_k__preparing_models.html) or [suggestions from Vuforia](https://developer.vuforia.com/library/model-targets/model-targets-supported-objects-cad-model-best-practices).

### Implementation
&emsp;&emsp;The author implemented the algorithm kernel using C++. Meanwhile, in order to make it easier to use, the author has also developed a Unity plugin based on the kernel. There are three versions of this plugin:  ARFoundation version (iOS/Android), MRTK version (Microsoft Hololens 2), and Meta version (Quest 3/3s). Most features can also work based on the video stream from the native camera directly without relying on the ARFoundation, but ARFoundation can bring two benefits: 1. Fusion with SLAM can improve the stability of the pose and the ability of self-recovery; 2. ARFoundation provides camera intrinsics in real time without requiring the user to pre-calibrate the camera (especially those with Auto Focus).  
&emsp;&emsp;The plugin is currently a lab prototype version and has not been fully tested, there may be adaptation bugs on some devices. During the trial period, the algorithm kernel runs for 5 minutes from the time the program starts, and stops automatically after the timeout.
<br/>


## Preparation

### Device Support
- For iOS devices, most of them in recent years with ARKit are supported.
- For Android devices, ARCore support is required, please see the official [device support list](https://developers.google.cn/ar/devices?hl=zh-cn). For those devices that are not on the list, there are some [tricks](https://www.getdroidtips.com/enable-arcore-android-smartphones/) to support ARCore.
- For Microsoft Hololens, only the second generation is supported.
- For Meta devices, only Quest 3 and 3s with at least v74 system are supported.
- For devices on other platforms such as Windows, Linux, and MacOS, custom adaptation based on the underlying core is required. Please contact us.

### Software
- ARFoundation 5.1.0
- MRTK 2.8.3
- Unity 2022.3 LTS
- Unity 6000.1 (For Meta)

### Installation
- (ARFoundation version only) Install ARFoundation through Unity Package Manager, and install ARKit XR or ARCore XR according to the target platform. [Tutorial](https://www.bilibili.com/video/BV144411N7sW/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf)
- (MRTK version only) Install MRTK through MixedRealityFeatureTool. [Tutorial](https://learn.microsoft.com/en-us/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)
- (Meta version only) Install MRUK through Unity Package Manager. [Tutorial](https://skarredghost.com/2025/03/17/how-to-camera-access-meta-quest-3-unity-6/)
- Download Camereon unitypackage from the [Release](https://github.com/HartisanBUAA/Camereon-Model-Tracker/releases) page.

### Model Preparation
- There are no special requirements for the format of the CAD model, as long as it can be imported into Unity and rendered properly.
- If you want to utilize texture edges on the surface of the object, you need to provide a texture map that is consistent with the real object and ensure that it can be rendered correctly. Otherwise, there are no requirements for the model's texture and material.
- The model's local coordinate system should be **right-handed**.
- The model's unit should be "meter".
- The origin of the local coordinate system should preferably be located near the center of the model to facilitate the adjustment of the pose and also to increase the probability of successful self-recovery.
- **The automated AI training tool is currently under development. For CAD models pre-training requirements, please contact us.**
<br/>


## Usage

### ARFoundation Version
#### 1. Import basic objects
&emsp;&emsp;Delete the default *Main Camera* from the scene，and import *AR Session* and *XR Origin* through "*Toolbar - GameObject - XR*". There will be a new *Main Camera* under *XR Origin*.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/basic%20components%20-%20arf.png" width = "300" alt="basic components - arf" />
</div>

#### 2. Import Camereon package
&emsp;&emsp;Import *Camereon.ModelTracker.ARFoundation-vx.x.x.unitypackage*, there are three folders（*Plugins、Scripts、Shaders*）and one prefab (*CMRModelTracker*) in this package.  
&emsp;&emsp;**NOTE**: There is a known bug in unity that the *Plugins* folder does not contain the iOS framework library when exporting the unitypackage. Therefore, iOS users need to download the *iOS framework* zip from the Release page, unzip it and put the *iOS* folder into the *Plugins* folder. This bug has been fixed in the latest version of Unity.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20package%20-%20arf.png" width = "400" alt="import package - arf" />
</div>

#### 3. Import Camereon objects
&emsp;&emsp;Add the prefab *CMRModelTracker* to the scene, the prefab contains：
- ***CMR Camera***. This camera is not involved in the display and is only used by the algorithm for background processing of CAD models.
- ***Canvas***
	- ***Edge Image***. An image that is used to display edges.
	- ***Resume Button***. A button that is used to resume the tracking when the tracking fails or the wrong target is tracked. This button is not required, you can design your own trigger logic using the scripting API below.
- ***Target Anchor***. An empty object to maintain the pose. When the target object's pose relative to the camera is successfully solved, the pose is further converted to the object's pose in the SLAM coordinate system (*XR Origin*) and updated to this *Target Anchor* object (which will be automatically moved under *XR Origin* as its son node). And in practice, the user only needs to create virtual content based on this object.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20tracker%20-%20arf.png" width = "300" alt="import tracker - arf" />
</div>
 
#### 4. Import CAD model
&emsp;&emsp;Add the target's model to the scene, place it under *CMR Camera* as a son node. If the model hasn't been pre-trained, you need to adjust it's initial pose relative to the *CMR Camera* through the *Transform* component. This pose will be used for manually initialization, and the user should choose the appropriate pose based on the application scenario. If the model has already been pre-trained, it is not necessary to set the initial pose.
&emsp;&emsp;Add the last *Layer* and name it "CMR" in the inspector, and set the *Layer* of both the *CMR Camera* and the model to "CMR". This is to avoid interfering with the main scene when performing background processing on the model.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20layer.png" width = "500" alt="add layer" />
</div>

#### 5. Import pre-training data (Optional)
&emsp;&emsp;If the model has been pre-trained, import the pre-training data (.dat files) to the folder *Assets-StreamingAssets* (create if not exist). 

#### 6. Object settings
&emsp;&emsp;The prefab *CMRModelTracker* should load the script *CMRModelTrackerManager*, which is under the *Scripts* folder. Connect the objects in the scene to the variables in the script  as shown in the figure, where *VLCar* is the CAD model as an example.
&emsp;&emsp;If the pre-trained data has been imported and AI-enhanced mode needs to be activated, enter the dat filename (excluding extension) in the *Training Data Name* field.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/cmr%20settings%20-%20arf.png" width = "700" alt="cmr settings - arf" />
</div>

&emsp;&emsp;The script *CMRModelTrackerManager* provides several setup options：
- ***Auto Start***. Start initializing and tracking automatically after program startup.
- ***Display Edges***. Display edges during the tracking (after initialization).
- ***Use FPS60***. Use 60fps. Higher frame rates will increase power consumption and not all devices support 60 fps.
- ***Initialization Only***. The tracking stops after successful initialization, and the subsequent poses are maintained by SLAM. Commonly used in static scenes.
- ***Use Texture Edges***. Besides geometric edge features, edge features in the surface texture of the object are also used. It is required to provide a texture map that is consistent with the real object and ensure correct rendering.
- ***Use CPU For AI Inference***. Use CPU for inference in the AI-enhanced mode; otherwise, different acceleration frameworks will be automatically selected for inference based on the platform. Specifically, Windows uses DirectML, iOS uses CoreML, and Android uses NNAPI. **It is important to note that due to the varying optimization characteristics of different devices and platforms, the combination of acceleration frameworks and quantized AI models does not always yield the fastest inference. Users may need to experiment with different combinations.** For example, the combination of CoreML and FP32 is the fastest on iOS devices, while the combination of CPU and INT8 may be the fastest on some Android devices.
- ***Edge Magnitude Thresh***. Threshold for detecting edge features in an image, reflecting the difference in grayscale between the two sides of the edge. Too large a threshold may lead to incomplete edge detection, and too small a threshold may introduce noise interference. It needs to be adjusted according to the application scenario, and the default value is 15.
- ***Initialization Quality Thresh***. Threshold for the initialization quality, reflecting how much edge features in the image are aligned with the target object. In some cases, the edge features in the image do not completely match the target object, for example, the object is partially occluded, the CAD model is inaccurate, etc. A threshold that is too large may cause initialization difficulties, and a threshold that is too small may result in tracking the wrong object. It needs to be adjusted according to the application scenario, and the default value is 0.65.
- ***Control Points Max Number***. The maximun number of control points. The algorithm will sample points, which are called control points, at a certain step on the edges. Increasing the number of control points can improve the robustness of the algorithm, but the computation effort will also increase. Reducing the number of control points will do the opposite. The default value is 2500.
- ***Pose Smooth Factor***. The factor to smooth the pose. To reduce jitter, the algorithm can smooth the output pose. A value of 0 means no smoothing is performed. The larger the value, the smoother the pose, but also the greater the delay. The default value is 0.5.

#### 7. Project settings
- Enable "Allow ‘unsafe’ Code"
- Set "Scripting Backend" to "IL2CPP"
- For Android, set "Graphics API" to "OpenGLES3", not "Vulkan"
- Set "Target Architectures" to "ARM64"
- For iOS, enable "Requires ARKit Support"
- Confirm "Apple ARKit" or "Google ARCore" is checked in "XR Plug-in Management"
- In "Graphics - Always Included Shaders", add four shaders in the package according to the type of render pipeline used by the project
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20shaders.png" width = "800" alt="add shaders" />
</div>

#### 8. Program interaction
&emsp;&emsp;When the program starts running on the device, the camera is automatically turned on by ARFoundation and the video stream is displayed on the screen. When the Camereon tracker starts running (through API or "*Auto Start*" is checked in the script), 
- **For Non-AI mode** : the edge features of the target with the initial pose, which was setup in "4. Import CAD model", will display on the screen. The user just need to move the device so that the edges are roughly aligned with the target object in the image, then the initialization will be completed and tracking begins. 
- **For AI mode** : Manual initial pose setup is no longer required — AI will automatically detect objects and completes initialization from arbitrary viewpoints.

&emsp;&emsp;Successful initialization is manifested by the red edges disappearing or turning green, depending on whether "*Display Edges*" is checked in the script settings.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/interaction%20-%20arf.png" width = "700" alt="interaction - arf" />
</div>
<br/>


### MRTK Version
#### 1. Import basic objects
&emsp;&emsp;Delete the default *Main Camera* from the scene，and import *MixedRealityToolkit*, *MixedRealitySceneContent* and *MixedRealityPlayspace* through "*Toolbar - MixedReality - Toolkit - Add to Scene and Configure*". There will be a new *Main Camera* under *MixedRealityPlayspace*.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/basic%20components%20-%20mrtk.png" width = "300" alt="basic components - mrtk" />
</div>

#### 2. Import Camereon package
&emsp;&emsp;Import *Camereon.ModelTracker.MRTK2-vx.x.x.unitypackage*, there are three folders（*Plugins、Scripts、Shaders*）and one prefab (*CMRModelTracker*) in this package.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20package%20-%20mrtk.png" width = "400" alt="import package - mrtk" />
</div>

#### 3. Import Camereon objects
&emsp;&emsp;Add the prefab *CMRModelTracker* to the scene, the prefab contains：
- ***CMR Camera***. This camera is not involved in the display and is only used by the algorithm for background processing of CAD models.
- ***Target Anchor***. An empty object to maintain the pose. When the target object's pose relative to the camera is successfully solved, the pose is further converted to the object's pose in the world coordinate system and updated to this *Target Anchor* object (which will be automatically moved under scene root as its son node). And in practice, the user only needs to create content based on this object.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20tracker%20-%20mrtk.png" width = "300" alt="import tracker - mrtk" />
</div>

#### 4. Import CAD model
&emsp;&emsp;Add the target's model to the scene, place it under *CMR Camera* as a son node. If the model hasn't been pre-trained, you need to adjust it's initial pose relative to the *CMR Camera* through the *Transform* component. This pose will be used for manually initialization, and the user should choose the appropriate pose (relative to eyes, not the PV camera) based on the application scenario. If the model has already been pre-trained, it is not necessary to set the initial pose.
&emsp;&emsp;Add the last *Layer* and name it "CMR" in the inspector, and set the *Layer* of both the *CMR Camera* and the model to "CMR". This is to avoid interfering with the main scene when performing background processing on the model.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20layer.png" width = "500" alt="add layer" />
</div>

#### 5. Import pre-training data (Optional)
&emsp;&emsp;If the model has been pre-trained, import the pre-training data (.dat files) to the folder *Assets-StreamingAssets* (create if not exist). 

#### 6. Object settings
&emsp;&emsp;The prefab *CMRModelTracker* should load the script *CMRModelTrackerManager*, which is under the *Scripts* folder. Connect the objects in the scene to the variables in the script  as shown in the figure, where *VLCar* is the CAD model as an example.
&emsp;&emsp;If the pre-trained data has been imported and AI-enhanced mode needs to be activated, enter the dat filename (excluding extension) in the *Training Data Name* field.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/cmr%20settings%20-%20mrtk.png" width = "700" alt="cmr settings - mrtk" />
</div>

&emsp;&emsp;The script *CMRModelTrackerManager* provides several setup options：
- ***Auto Start***. Start initializing and tracking automatically after program startup.
- ***Display Model***. Display CAD model during the tracking (after initialization). 
- ***Initialization Only***. The tracking stops after successful initialization, and the subsequent poses are maintained by Hololens. Commonly used in static scenes.
- ***Use Texture Edges***. Besides geometric edge features, edge features in the surface texture of the object are also used. It is required to provide a texture map that is consistent with the real object and ensure correct rendering.
- ***Use CPU For AI Inference***. Use CPU for inference in the AI-enhanced mode, otherwise DirectML will be used. **It is important to note that due to the varying optimization characteristics of different devices and platforms, the combination of acceleration frameworks and quantized AI models does not always yield the fastest inference. Users may need to experiment with different combinations.** On HoloLens 2, using CPU inference may be faster, and using DirectML may even cause the GPU to be suspended.
- ***Edge Magnitude Thresh***. Threshold for detecting edge features in an image, reflecting the difference in grayscale between the two sides of the edge. Too large a threshold may lead to incomplete edge detection, and too small a threshold may introduce noise interference. It needs to be adjusted according to the application scenario, and the default value is 15.
- ***Initialization Quality Thresh***. Threshold for the initialization quality, reflecting how much edge features in the image are aligned with the target object. In some cases, the edge features in the image do not completely match the target object, for example, the object is partially occluded, the CAD model is inaccurate, etc. A threshold that is too large may cause initialization difficulties, and a threshold that is too small may result in tracking the wrong object. It needs to be adjusted according to the application scenario, and the default value is 0.65.
- ***Control Points Max Number***. The maximun number of control points. The algorithm will sample points, which are called control points, at a certain step on the edges. Increasing the number of control points can improve the robustness of the algorithm, but the calculation effort will also increase. Reducing the number of control points will do the opposite. The default value is 2500.
- ***Pose Smooth Factor***. The factor to smooth the pose. To reduce jitter, the algorithm can smooth the output pose. A value of 0 means no smoothing is performed. The larger the value, the smoother the pose, but also the greater the delay. The default value is 0.5.

#### 7. Project settings
- Enable "Allow ‘unsafe’ Code"
- Set "Architecture" to "ARM 64-bit"
- Confirm "Open XR" and "Microsoft Hololens feature group" are checked in "XR Plug-in Management"
- In "Graphics - Always Included Shaders", add four shaders in the package according to the type of render pipeline used by the project
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20shaders.png" width = "800" alt="add shaders" />
</div>

#### 8. Program interaction
&emsp;&emsp;When the Camereon tracker starts running (through API or "*Auto Start*" is checked in the script), 
- **For Non-AI mode** : the CAD model of the target with initial pose, which was set in "4. Import CAD model", will display in front of the user's eyes.The user just need to move his head so that the model is roughly aligned with the real target object, then the initialization will be completed (with some delays) and tracking begins. 
- **For AI mode** : Manual initial pose setup is no longer required — AI will automatically detect objects and completes initialization from arbitrary viewpoints (The object must remain within the PV camera’s field of view). **It is important to note that inference on the Hololens requires 3-4 seconds per pass (potentially constrained by computing resources; using DirectML could even lead to GPU suspension). Consequently, it is advisable to keep the target object relatively still with respect to the Hololens during the initialization phase.**

&emsp;&emsp;Successful initialization is manifested by the red model disappearing or turning green, depending on whether "*Display Model*" is checked in the script settings.
<br/>


### Meta Version
#### 1. Import basic objects
&emsp;&emsp;Delete the default *Main Camera* from the scene，and import the *Passthrough BuildingBlock*.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/basic%20components%20-%20meta.png" width = "300" alt="basic components - meta" />
</div>

#### 2. Import Camereon package
&emsp;&emsp;Import *Camereon.ModelTracker.META-vx.x.x.unitypackage*, there are four folders（*Plugins、Scripts、Shaders、PassthroughCamera*）and one prefab (*CMRModelTracker*) in this package.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20package%20-%20meta.png" width = "400" alt="import package - meta" />
</div>

#### 3. Import Camereon objects
&emsp;&emsp;Add the prefab *CMRModelTracker* to the scene, the prefab contains：
- ***CMR Camera***. This camera is not involved in the display and is only used by the algorithm for background processing of CAD models.
- ***Target Anchor***. An empty object to maintain the pose. When the target object's pose relative to the camera is successfully solved, the pose is further converted to the object's pose in the world coordinate system and updated to this *Target Anchor* object (which will be automatically moved under scene root as its son node). And in practice, the user only needs to create content based on this object.
- ***WebCamTextureManagerPrefab***. A prefab to manage camera imges.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20tracker%20-%20meta.png" width = "300" alt="import tracker - meta" />
</div>

#### 4. Import CAD model
&emsp;&emsp;Add the target's model to the scene, place it under *CMR Camera* as a son node. If the model hasn't been pre-trained, you need to adjust it's initial pose relative to the *CMR Camera* through the *Transform* component. This pose will be used for manually initialization, and the user should choose the appropriate pose (relative to eyes, not left/right camera) based on the application scenario. If the model has already been pre-trained, it is not necessary to set the initial pose.
&emsp;&emsp;Add the last *Layer* and name it "CMR" in the inspector, and set the *Layer* of both the *CMR Camera* and the model to "CMR". This is to avoid interfering with the main scene when performing background processing on the model.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20layer.png" width = "500" alt="add layer" />
</div>

#### 5. Import pre-training data (Optional)
&emsp;&emsp;If the model has been pre-trained, import the pre-training data (.dat files) to the folder *Assets-StreamingAssets* (create if not exist). 

#### 6. Object settings
&emsp;&emsp;The prefab *CMRModelTracker* should load the script *CMRModelTrackerManager*, which is under the *Scripts* folder. Connect the objects in the scene to the variables in the script  as shown in the figure, where *VLCar* is the CAD model as an example.
&emsp;&emsp;If the pre-trained data has been imported and AI-enhanced mode needs to be activated, enter the dat filename (excluding extension) in the *Training Data Name* field.
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/cmr%20settings%20-%20meta.png" width = "700" alt="cmr settings - meta" />
</div>

&emsp;&emsp;The script *CMRModelTrackerManager* provides several setup options：
- ***Auto Start***. Start initializing and tracking automatically after program startup.
- ***Display Model***. Display CAD model during the tracking (after initialization). 
- ***Initialization Only***. The tracking stops after successful initialization, and the subsequent poses are maintained by Quest. Commonly used in static scenes.
- ***Use Texture Edges***. Besides geometric edge features, edge features in the surface texture of the object are also used. It is required to provide a texture map that is consistent with the real object and ensure correct rendering.
- ***Use CPU For AI Inference***. Use CPU for inference in the AI-enhanced mode, otherwise NNAPI will be used. **It is important to note that due to the varying optimization characteristics of different devices and platforms, the combination of acceleration frameworks and quantized AI models does not always yield the fastest inference. Users may need to experiment with different combinations.** 
- ***Edge Magnitude Thresh***. Threshold for detecting edge features in an image, reflecting the difference in grayscale between the two sides of the edge. Too large a threshold may lead to incomplete edge detection, and too small a threshold may introduce noise interference. It needs to be adjusted according to the application scenario, and the default value is 15.
- ***Initialization Quality Thresh***. Threshold for the initialization quality, reflecting how much edge features in the image are aligned with the target object. In some cases, the edge features in the image do not completely match the target object, for example, the object is partially occluded, the CAD model is inaccurate, etc. A threshold that is too large may cause initialization difficulties, and a threshold that is too small may result in tracking the wrong object. It needs to be adjusted according to the application scenario, and the default value is 0.65.
- ***Control Points Max Number***. The maximun number of control points. The algorithm will sample points, which are called control points, at a certain step on the edges. Increasing the number of control points can improve the robustness of the algorithm, but the calculation effort will also increase. Reducing the number of control points will do the opposite. The default value is 2500.
- ***Pose Smooth Factor***. The factor to smooth the pose. To reduce jitter, the algorithm can smooth the output pose. A value of 0 means no smoothing is performed. The larger the value, the smoother the pose, but also the greater the delay. The default value is 0.5.

#### 7. Project settings
- Enable "Allow ‘unsafe’ Code"
- Set "Target Architecture" to "ARM64"
- Set "Graphics API" to "Vulkan"
- In "Graphics - Always Included Shaders", add four shaders in the package according to the type of render pipeline used by the project
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20shaders%20-%20meta.png" width = "800" alt="add shaders - meta" />
</div>

#### 8. Program interaction
&emsp;&emsp;When the Camereon tracker starts running (through API or "*Auto Start*" is checked in the script), 
- **For Non-AI mode** : the CAD model of the target with initial pose, which was set in "4. Import CAD model", will display in front of the user's eyes.The user just need to move his head so that the model is roughly aligned with the real target object, then the initialization will be completed (with some delays) and tracking begins. 
- **For AI mode** : Manual initial pose setup is no longer required — AI will automatically detect objects and completes initialization from arbitrary viewpoints (The object must remain within the left camera’s field of view). 

&emsp;&emsp;Successful initialization is manifested by the red model disappearing or turning green, depending on whether "*Display Model*" is checked in the script settings. **It should be noted that Meta does not acquire camera images with timestamps, making it unable to synchronize in real-time with the device’s motion. This leads to pose calculation latency and bias. During initialization, it is advisable to keep head movements slow and to use the CalibrateSLAMPose API for correction.**
<br/>

### Public APIs
&emsp;&emsp;To facilitate user control of the program logic, the *CMRModelTrackerManager* script provides some APIs for both the ARFoundation version and the MRTK version:
- `public void StartCMR()`：Start Camereon Model Tracker.
- `public void StopCMR()`：Stop Camereon Model Tracker.
- `public void ResumeCMR()`：Resume Camereon Model Tracker. Commonly used in the case of tracking failure or tracking the wrong target.
- `public void SetModelTarget(GameObject model)`：Specify the CAD model to be used for tracking.
The current version only supports tracking a single object, if you need to switch the tracking target in the scene, you can import multiple models to *CMR Camera* at the same time and set up the initial pose and Layer respectively, then connect the first model to use to the *CMRModelTrackerManager* script. And this method can be called programmatically to switch to another model when the program is running. **NOTE**: Calling this method will break the tracking.
- `public void SetModelTargetAndStart(GameObject model)`：Similar to the previous method, the difference is that this method will automatically start the tracker after setting up the model. The effect is equivalent to SetModelTarget()+StartCMR()
- `public void CalibrateSLAMPose()`：When relying only on SLAM to maintain the pose, the translation may drift due to error accumulation or lack of sensor accuracy, and this method attempts to correct for the drift. **NOTE**: This method only works if "*Initialization Only*" is checked, and it is also necessary to ensure that the target object is in the image. This method does not guarantee success if the drift is too large.
<br/>


## License
[GPL v3](https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/LICENSE) 
<br/>


## Contact Information
Hartisan@buaa.edu.cn  
[Github](https://github.com/HartisanBUAA/Camereon-Model-Tracker)

<p align="right">(<a href="#top">Top</a>)</p>
