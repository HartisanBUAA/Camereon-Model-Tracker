<a name="top"></a>

<br/>
<div align="center">
  <h1 align="center">Camereon Model Tracker</h1>
  <h3 align="center">基于三维模型与单目图像的刚体六自由度位姿实时跟踪算法</h3>
  <h3 align="center">( <a href="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/README.md">English</a> / <a href="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/README_CN.md">中文</a> )</h3>
</div>
<br/>

<!--
[TOC]
-->
- [项目简介](#项目简介)
  * [理论背景](#理论背景)
  * [主要特性](#主要特性)
  * [演示视频](#演示视频)
  * [适用场景](#适用场景)
  * [项目形式](#项目形式)
- [使用准备](#使用准备)
  * [设备支持](#设备支持)
  * [软件版本](#软件版本)
  * [插件获取](#插件获取)
  * [模型准备](#模型准备)
- [使用方法](#使用方法)
  * [ARFoundation版](#arfoundation版)
    + [1. 导入基础对象](#1-导入基础对象)
    + [2. 导入本项目包](#2-导入本项目包)
    + [3. 导入本项目对象](#3-导入本项目对象)
    + [4. 导入模型](#4-导入模型)
    + [5. 对象设置](#5-对象设置)
    + [6. 打包设置](#6-打包设置)
    + [7. 程序交互](#7-程序交互)
  * [MRTK版](#mrtk版)
    + [1. 导入基础对象](#1-导入基础对象)
    + [2. 导入本项目包](#2-导入本项目包)
    + [3. 导入本项目对象](#3-导入本项目对象)
    + [4. 导入模型](#4-导入模型)
    + [5. 对象设置](#5-对象设置)
    + [6. 打包设置](#6-打包设置)
    + [7. 程序交互](#7-程序交互)
  * [公共API](#公共api)
- [许可证](#许可证)
- [联系我们](#联系我们)
<br/>


## 项目简介

### 理论背景
&emsp;&emsp;**本算法用于实时计算三维物体相对于单目相机的六自由度位姿**（平移+旋转），可用于增强现实、机器人抓取、目标跟踪等领域。与德国的[VisionLib](https://visionlib.com/) 和 PTC的[Vuforia Model Target](https://developer.vuforia.com/library/objects/model-targets)模块属于同类型技术。其主要原理为：利用物体的CAD模型提取三维边缘特征，在单目相机获取的RGB图像中提取二维边缘特征，通过搜寻匹配建立三维特征与二维特征之间的对应关系，进而求解物体位姿。

### 主要特性
- 只需要提供目标物体的CAD模型，无需预先训练
- 快速稳定的实时位姿跟踪，可在主流移动设备上达到60FPS
- 跟踪过程中特征自动提取更新
- 对复杂背景、局部遮挡、快速运动具有较好的鲁棒性
- 跟踪失败时具备一定的自恢复能力
- 与SLAM融合，提高位姿稳定性和自恢复能力
- 跨平台支持

### 演示视频
&emsp;&emsp;[DEMO](https://www.bilibili.com/video/BV1s3411Z7ac/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf) 

### 适用场景
&emsp;&emsp;基于上述技术原理，本算法主要适用（但不限于）**原生具备CAD模型的弱纹理场景**，如工业领域。同时，鉴于其依赖边缘特征，物体的几何结构和边缘数量会对跟踪效果产生一定影响，因此在模型和参数选择上应遵循一些建议参考：[VisionLib参考](https://docs.visionlib.com/v2.4.1/vl_unity_s_d_k__preparing_models.html) & [Vuforia参考](https://developer.vuforia.com/library/model-targets/model-targets-supported-objects-cad-model-best-practices)。

### 项目形式
&emsp;&emsp;作者采用C++编写了算法内核。同时为了便于使用，又基于该内核开发了Unity插件，分为ARFoundation版和MRTK版。前者适用于iOS、Android等移动设备，后者适用于Microsoft Hololens 2。直接基于原生相机的视频流而不依赖ARFoundation也可以实现大部分功能，但ARFoundation可以带来两个益处：**1. 与SLAM融合，提高位姿稳定性和自恢复能力；2. 实时提供相机内参，无需要求用户预先标定相机（尤其在自动对焦功能可能引起内参变化时）**。考虑到ARFoundation对后续设备的支持覆盖越来越广，作者暂时选择基于该框架进行了插件开发。  
&emsp;&emsp;当前插件为实验室原型版本，尚未经过大面积测试，在某些设备上可能会出现适配bug，欢迎反馈。在试用期间，算法内核自程序启动之时起运行5分钟，超时后自动停止。
<br/>


## 使用准备

### 设备支持
- 对于iOS设备，需支持ARKit，近几年的iOS设备基本都支持
- 对于Android设备，需支持ARCore，具体参见官方的[设备支持清单](https://developers.google.cn/ar/devices?hl=zh-cn)。对于不在清单上的设备，也有一些[Trick](https://www.getdroidtips.com/enable-arcore-android-smartphones/)可以让设备支持ARCore
- 对于Hololens设备，需为第二代产品

### 软件版本
- ARFoundation 5.1.0
- MRTK 2.8.3
- Unity 2022.3 LTS

### 插件获取
- 对于ARFoundation版，通过Unity Package Manager 安装 ARFoundation，根据目标平台选择安装ARKit XR或者ARCore XR插件，[参考教程](https://www.bilibili.com/video/BV144411N7sW/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf)
- 对于MRTK版，通过MixedRealityFeatureTool安装MRTK，[参考教程](https://learn.microsoft.com/zh-cn/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)
- 通过本项目的[Release](https://github.com/HartisanBUAA/Camereon-Model-Tracker/releases)页下载对应版本的Camereon unitypackage

### 模型准备
- 对CAD模型的格式没有特殊要求，能导入Unity正常渲染即可
- 模型不需要纹理，对颜色也没有要求
- 要求创建模型时局部坐标系为**右手系**，推荐Y轴向上
- 要求创建模型时使用“**米**”作为单位，才能得到真实位姿。例如实际物体的长度为10厘米，则其模型的长度数值应为0.1
- 局部坐标系的原点最好位于模型中心附近，方便调整位姿，同时也可以提高自恢复的成功率
<br/>


## 使用方法

### ARFoundation版
（<a href="#mrtk">转至MRTK版</a>）
#### 1. 导入基础对象
&emsp;&emsp;根据上述[参考教程](https://www.bilibili.com/video/BV144411N7sW/?vd_source=587567287ab7e8ab9d2dfbe660b36ddf)安装ARFoundation后，从场景中删除默认的*Main Camera*，并从“*工具栏 - GameObject - XR*”中向场景添加*AR Session*和*XR Origin*对象，其中*XR Origin*对象下挂载了新的*Main Camera*。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/basic%20components%20-%20arf.png" width = "300" alt="basic components - arf" />
</div>

#### 2. 导入本项目包
&emsp;&emsp;向Unity中导入*Camereon.ModelTracker.ARFoundation-vx.x.x.unitypackage*，该package包含3个文件夹（*Plugins、Scripts、Shaders*）以及1个预制体*CMRModelTracker*。  
&emsp;&emsp;**Tips**：由于Unity的bug，导出unitypackage时*Plugins*文件夹无法包含iOS平台的 framework库。所以iOS平台用户暂时需要单独从Release页下载*iOS framework*压缩包，解压后将*iOS*文件夹放入*Plugins*文件夹下即可。该bug正在被官方修复中。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20package%20-%20arf.png" width = "350" alt="import package - arf" />
</div>

#### 3. 导入本项目对象
&emsp;&emsp;将预制体*CMRModelTracker*拖入场景中，该预制体包含以下对象：
- ***CMR Camera***. 该相机不参与显示，只用于算法对CAD模型进行后台处理
- ***Canvas***
	- ***Edge Image***. 用于显示边缘线条，这对于初始化流程是必需的
	- ***Resume Button***. 恢复按钮，可在跟踪彻底失败时点击，重新开始初始化。该按钮不是必需的，可通过下文脚本API自行设计触发逻辑
- ***Target Anchor***. 用于承载位姿的空对象。当目标物体相对于相机的位姿被成功解算时，该位姿会被进一步转换为物体在SLAM坐标系（*XR Origin*）中的位姿，并实时更新到该对象上（该对象会被自动移至*XR Origin*下作为其儿子节点）。通过这种方式，针对静态场景的跟踪失败情况，可由SLAM继续维护物体的位姿。在实际使用时，用户只需基于该对象进行内容制作即可
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20tracker%20-%20arf.png" width = "300" alt="import tracker - arf" />
</div>
 
#### 4. 导入模型
&emsp;&emsp;将目标物体的CAD模型拖入场景中，置于*CMR Camera*下作为其儿子节点，并通过*Transform*组件调整其相对于*CMR Camera*的初始位姿。该位姿将被用于初始化流程，用户应根据应用场景选择合适的初始视角。  
&emsp;&emsp;添加最后一层*Layer*，命名为“CMR”，同时将*CMR Camera*和CAD模型所属的*Layer*都设置为“CMR”。此举是为了避免对模型进行后台处理时与主场景互相干扰。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20layer.png" width = "500" alt="add layer" />
</div>

#### 5. 对象设置
&emsp;&emsp;预制体*CMRModelTracker*应挂载*Scripts*文件夹中的*CMRModelTrackerManager*脚本，并将脚本中的变量与场景中的对象连接，如下图所示。其中*VLCar*是作为示例而导入的CAD模型。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/cmr%20settings%20-%20arf.png" width = "700" alt="cmr settings - arf" />
</div>

&emsp;&emsp;*CMRModelTrackerManager*脚本中提供了若干设置选项，主要包括：
- ***Auto Start***. 程序启动后自动进入跟踪状态，开始初始化流程
- ***Display Edges***. 初始化成功后，跟踪过程中是否继续显示边缘特征
- ***Use FPS60***. 是否启用60帧率。高帧率可以获得更流畅的体验，但也会增加功耗，并且不是所有设备都支持60帧率
- ***Initialization Only***. 只执行初始化流程，初始化成功后即停止跟踪，后续位姿依靠SLAM维护。常用于静态场景
- ***Edge Magnitude Thresh***. 在图像中检测边缘特征时的阈值，用来表示边缘两侧的灰度差值。阈值过大可能导致边缘漏检，阈值过小可能引入噪声干扰，需根据实际应用场景进行调整，默认值为20
- ***Initialization Quality Thresh***. 初始化质量阈值，反映了图像中边缘特征与目标物体的吻合程度。在某些情况下，图像中的边缘特征与目标物体并没有完全吻合，例如物体被局部遮挡、CAD模型不准确等。阈值过大可能导致初始化困难，阈值过小可能导致跟踪到错误的物体，需根据实际应用场景进行调整，默认值为0.65
- ***Control Points Max Number***. 控制点最大数量。算法会在边缘上按照一定步长采样单点（被称为控制点）进行匹配。增加控制点可提高算法鲁棒性，但计算量也会增加，减少控制点反之，默认值为2500
- ***Pose Smooth Factor***. 位姿平滑系数。为减少抖动，算法可对输出位姿进行一定程度的平滑。数值为0代表不进行平滑，数值越大代表平滑程度越大，但相应地也会导致位姿滞后。默认值为0.5

#### 6. 打包设置
&emsp;&emsp;切换到目标平台正常进行打包即可，但Project Settings中个别设置需要注意：
- 需勾选“Allow ‘unsafe’ Code”
- “Scripting Backend” 选择 “IL2CPP”
- Android平台的“Graphics API”需禁用Vulkan，使用OpenGLES3
- “Target Architectures”选择“ARM64”
- iOS平台勾选“Requires ARKit Support”
- 在“XR Plug-in Management”中确认勾选了“Apple ARKit”或“Google ARCore”

#### 7. 程序交互
&emsp;&emsp;程序在设备上开始运行时，摄像头被ARFoundation自动开启并将视频流显示到屏幕上。当本项目算法开始工作时（通过API控制或者在上文脚本设置中勾选了“*Auto Start*”），目标物体在初始位姿下（在“4.导入模型”中设置的）的边缘特征会自动显示在屏幕上，用户移动设备使得边缘与图像中的目标物体大致对齐，即可完成初始化流程进入跟踪阶段。初始化成功具体表现为红色边缘消失或变为绿色，这取决于是否在脚本设置中勾选了“*Display Edges*”。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/interaction%20-%20arf.png" width = "700" alt="interaction - arf" />
</div>
<br/>

<a name="mrtk"></a>
### MRTK版
#### 1. 导入基础对象
&emsp;&emsp;根据上述[参考教程](https://learn.microsoft.com/zh-cn/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)安装MRTK后，从场景中删除默认的*Main Camera*，并通过“*工具栏 - MixedReality - Toolkit - Add to Scene and Configure*”向场景添加*MixedRealityToolkit*、*MixedRealitySceneContent* 和 *MixedRealityPlayspace*对象，其中*MixedRealityPlayspace*对象下挂载了新的*Main Camera*。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/basic%20components%20-%20mrtk.png" width = "300" alt="basic components - mrtk" />
</div>

#### 2. 导入本项目包
&emsp;&emsp;向Unity中导入*Camereon.ModelTracker.MRTK2-vx.x.x.unitypackage*,该package包含3个文件夹（*Plugins、Scripts、Shaders*）以及1个预制体*CMRModelTracker*。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20package%20-%20mrtk.png" width = "350" alt="import package - mrtk" />
</div>

#### 3. 导入本项目对象
&emsp;&emsp;将预制体*CMRModelTracker*拖入场景中，该预制体包含以下对象：
- ***CMR Camera***. 该相机不参与显示，只用于算法对CAD模型进行后台处理
- ***Target Anchor***. 用于承载位姿的空对象。当目标物体相对于相机的位姿被成功解算时，该位姿会被进一步转换为物体在世界坐标系中的位姿，并实时更新到该对象上（该对象会被自动移至场景根节点下作为其儿子节点）。通过这种方式，在静态场景中可由Hololens继续维护物体的位姿。在实际使用时，用户只需基于该对象进行内容制作即可
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/import%20tracker%20-%20mrtk.png" width = "300" alt="import tracker - mrtk" />
</div>

#### 4. 导入模型
&emsp;&emsp;将目标物体的CAD模型拖入场景中，置于*CMR Camera*下作为其儿子节点，并通过*Transform*组件调整其相对于*CMR Camera*的初始位姿。该位姿将被用于初始化流程，用户应根据应用场景选择合适的初始视角（相对于人眼，而非PV相机）。  
&emsp;&emsp;添加最后一层*Layer*，命名为“CMR”，同时将*CMR Camera*和CAD模型所属的*Layer*都设置为“CMR”。此举是为了避免对模型进行后台处理时与主场景互相干扰。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/add%20layer.png" width = "500" alt="add layer" />
</div>

#### 5. 对象设置
&emsp;&emsp;预制体*CMRModelTracker*应挂载*Scripts*文件夹中的*CMRModelTrackerManager*脚本，并将脚本中的变量与场景中的对象连接，如下图所示。其中*VLCar*是作为示例而导入的CAD模型。
<div align="center">
  <img src="https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/Images/cmr%20settings%20-%20mrtk.png" width = "700" alt="cmr settings - mrtk" />
</div>

&emsp;&emsp;*CMRModelTrackerManager*脚本中提供了若干设置选项，主要包括：
- ***Auto Start***. 程序启动后自动进入跟踪状态，开始初始化流程
- ***Display Model***. 初始化成功后，跟踪过程中是否继续显示CAD模型
- ***Initialization Only***. 只执行初始化流程，初始化成功后即停止跟踪，后续位姿依靠Hololens维护。常用于静态场景或只需要解算单帧位姿的场景。**Tips：由于MRTK中获取相机图像的API为异步执行且帧率很低，导致实时跟踪模式下延迟极大，所以建议应用于静态场景并勾选该选项**
- ***Edge Magnitude Thresh***. 在图像中检测边缘特征时的阈值，用来表示边缘两侧的灰度差值。阈值过大可能导致边缘漏检，阈值过小可能引入噪声干扰，需根据实际应用场景进行调整，默认值为20
- ***Initialization Quality Thresh***. 初始化质量阈值，反映了图像中边缘特征与目标物体的吻合程度。在某些情况下，图像中的边缘特征与目标物体并没有完全吻合，例如物体被局部遮挡、CAD模型不准确等。阈值过大可能导致初始化困难，阈值过小可能导致跟踪到错误的物体，需根据实际应用场景进行调整，默认值为0.65
- ***Control Points Max Number***. 控制点最大数量。算法会在边缘上按照一定步长采样单点（被称为控制点）进行匹配。增加控制点可提高算法鲁棒性，但计算量也会增加，减少控制点反之，默认值为2500
- ***Pose Smooth Factor***. 位姿平滑系数。为减少抖动，算法可对输出位姿进行一定程度的平滑。数值为0代表不进行平滑，数值越大代表平滑程度越大，但相应地也会导致位姿滞后。默认值为0.5

#### 6. 打包设置
&emsp;&emsp;切换到UWP平台正常进行打包即可，但Project Settings中个别设置需要注意：
- 需勾选“Allow ‘unsafe’ Code”
- “Architecture”选择“ARM 64-bit”
- 在“XR Plug-in Management”中确认勾选了“Open XR”以及“Microsoft Hololens feature group”

#### 7. 程序交互
&emsp;&emsp;程序在设备上启动后，当本项目算法开始工作时（通过API控制或者在上文脚本设置中勾选了“*Auto Start*”），目标物体的CAD模型将在初始位姿下（在“4.导入模型”中设置的）显示在用户眼前，用户移动头部使得模型与现实中的目标物体大致对齐，即可完成初始化流程（有少许延迟）。初始化成功具体表现为CAD模型消失或变为绿色，这取决于是否在脚本设置中勾选了“*Display Model*”。
<br/>

### 公共API
&emsp;&emsp;为方便用户对程序逻辑进行控制，*CMRModelTrackerManager*脚本初步提供了少量API，同时适用于ARFoundation版和MRTK版：
- `public void StartCMR()`：开启Model Tracker，进入初始化流程
- `public void StopCMR()`：停止跟踪
- `public void ResumeCMR()`：重置跟踪，常用于跟踪失败或跟踪到错误目标的情形
- `public void SetModelTarget(GameObject model)`：指定跟踪使用的CAD模型。目前的版本只支持对单个物体进行跟踪，若场景中需要切换跟踪目标，可以先将多个模型同时挂到*CMR Camera*下并分别设置好初始位姿和Layer，然后将第一个要使用的模型与*CMRModelTrackerManager*脚本关联，后续通过程序调用此方法切换为其它模型。**注意**：调用该方法会中断跟踪
- `public void SetModelTargetAndStart(GameObject model)`：与上一方法类似，区别在于该方法设置模型后会自动开启Tracker，进入初始化流程，效果等同于SetModelTarget()+StartCMR()
- `public void CalibrateSLAMPose()`：单纯依靠SLAM维护位姿时，可能由于误差累计或传感器精度不够导致位置漂移，该方法可尝试对漂移进行校正。注意：该方法只在“*Initialization Only*”被勾选时有效，同时需保证目标物体位于图像视野内，并且漂移过大时不保证成功
<br/>


## 许可证
[GPL v3](https://github.com/HartisanBUAA/Camereon-Model-Tracker/blob/master/LICENSE) 
<br/>


## 联系我们
Hartisan@buaa.edu.cn  
项目链接: [Github](https://github.com/HartisanBUAA/Camereon-Model-Tracker)

<p align="right">(<a href="#top">返回顶部</a>)</p>
