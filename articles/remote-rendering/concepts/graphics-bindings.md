---
title: 图形绑定
description: 图形绑定设置和用例
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: d29500db5efd0abde4c9555fde9a7e3d5bbe070a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564983"
---
# <a name="graphics-binding"></a>图形绑定

要在自定义应用程序中使用 Azure 远程渲染，需要将其集成到应用程序的渲染管道中。 此集成可通过图形绑定完成。

设置后，可通过图形绑定访问影响渲染图像的各种函数。 这些函数可以分为两类：始终可用的常规函数和仅与所选 `Microsoft.Azure.RemoteRendering.GraphicsApiType` 相关的特定函数。

## <a name="graphics-binding-in-unity"></a>Unity 中的图形绑定

在 Unity 中，整个绑定由传递到 `RemoteManagerUnity.InitializeManager` 中的 `RemoteUnityClientInit` 结构处理。 要设置图形模式，必须将 `GraphicsApiType` 字段设置为所选绑定。 该字段将根据是否存在 XRDevice 自动填充。 该行为可手动替代为以下行为：

* HoloLens 2：始终使用 [Windows 混合现实](#windows-mixed-reality)图形绑定。
* **平面 UWP 桌面应用**：始终使用[模拟](#simulation)。
* **Unity 编辑器**：始终使用[模拟](#simulation)，除非连接了 WMR VR 耳机，在这种情况下，将禁用 ARR，以便调试应用程序的非 ARR 相关部分。 另请参阅[全息远程处理](../how-tos/unity/holographic-remoting.md)。

Unity 的唯一其他相关部分是访问[基本绑定](#access)，可跳过下面的所有其他部分。

## <a name="graphics-binding-setup-in-custom-applications"></a>自定义应用程序中的图形绑定设置

要选择图形绑定，请执行以下两个步骤：首先，初始化程序时，必须以静态方式初始化图形绑定：

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

需要进行上述调用，才可将 Azure 远程渲染初始化为全息 API。 在调用任意全息 API 和访问任何其他远程渲染 API 之前都必须先调用此函数。 同样，在不再调用全息 API 之后，应调用相应的 de-init 函数 `RemoteManagerStatic.ShutdownRemoteRendering();`。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">访问图形绑定

设置客户端后，可以使用 `AzureSession.GraphicsBinding` getter 访问基本图形绑定。 例如，可以按如下所示检索最后一帧的统计信息：

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>图形 API

当前可以选择两个图形 API：`WmrD3D11` 和 `SimD3D11`。 存在第三个图形 API：`Headless`，但客户端尚不支持。

### <a name="windows-mixed-reality"></a>Windows 混合现实

`GraphicsApiType.WmrD3D11` 是要在 HoloLens 2 上运行的默认绑定。 它将创建 `GraphicsBindingWmrD3d11` 绑定。 在此模式下，Azure 远程渲染直接连接到全息 API。

要访问派生的图形绑定，必须强制转换基本 `GraphicsBinding`。
要使用 WMR 绑定，需要执行以下两项操作：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知所用坐标系统的远程渲染

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


上面的 `ptr` 必须是一个指向本机 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 对象的指针，该对象定义了表示 API 中坐标的世界空间坐标系统。

#### <a name="render-remote-image"></a>渲染远程图像

每个帧开始时，需要将远程帧渲染到后台缓冲区中。 可通过调用 `BlitRemoteFrame` 来完成此操作，这会将颜色和深度信息填充到当前绑定的渲染目标。 因此，需要在将后台缓冲区绑定为渲染目标后完成此操作。

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>模拟

`GraphicsApiType.SimD3D11` 是模拟绑定，如果选择，它将创建 `GraphicsBindingSimD3d11` 图形绑定。 此界面用于模拟头部运动，例如，在桌面应用程序中，以及渲染单视场图像时。
设置过程要稍微复杂一点，其工作方式如下：

#### <a name="create-proxy-render-target"></a>设置代理渲染目标

需要使用 `GraphicsBindingSimD3d11.Update` 函数提供的代理照相机数据，将远程和本地内容渲染到名为“代理”的屏幕外颜色/深度渲染目标。 代理必须与后台缓冲区的分辨率匹配。 会话准备就绪后，需要先调用 `GraphicsBindingSimD3d11.InitSimulation` 才可连接到该会话：

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

需要为 init 函数提供指向本机 d3d 设备以及代理渲染目标的颜色和深度纹理的指针。 初始化后，可以多次调用 `AzureSession.ConnectToRuntime` 和 `DisconnectFromRuntime`，但切换到不同会话时，需要先在旧会话上调用 `GraphicsBindingSimD3d11.DeinitSimulation`，然后才能在另一个会话上调用 `GraphicsBindingSimD3d11.InitSimulation`。

#### <a name="render-loop-update"></a>渲染循环更新

渲染循环更新包含多个步骤：

1. 在对每个帧进行任何渲染之前，将使用发送到服务器进行渲染的最新照相机转换调用 `GraphicsBindingSimD3d11.Update`。 同时，应将返回的代理转换应用于代理照相机，以渲染到代理渲染目标中。
如果返回的代理更新 `SimulationUpdate.frameId` 为 NULL，则还没有远程数据。 在这种情况下，应使用最新照相机数据直接将任何本地内容渲染到后台缓冲区（而不是渲染到代理渲染目标中），并跳过接下来的两个步骤。
1. 现在，应用程序应绑定代理渲染目标并调用 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 这会将远程颜色和深度信息填充到代理渲染目标。 现在可以使用代理照相机转换将任何本地内容渲染到代理上。
1. 接下来，需要将后台缓冲区绑定为渲染目标，并调用 `GraphicsBindingSimD3d11.ReprojectProxy`，此时可以显示后台缓冲区。

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>后续步骤

* [教程：查看远程呈现的模型](../tutorials/unity/view-remote-models/view-remote-models.md)
