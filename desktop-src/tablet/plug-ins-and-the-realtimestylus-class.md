---
Description: The RealTimeStylus object is designed to provide real-time access to the data stream from the tablet pen.
ms.assetid: 7217e2d5-ca62-4d65-bf22-9511b367c798
title: Plug-ins and the RealTimeStylus Class
ms.date: 05/31/2018
ms.topic: article
ms.author: windowssdkdev
ms.prod: windows
ms.technology: desktop
---

# Plug-ins and the RealTimeStylus Class

The [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object is designed to provide real-time access to the data stream from the tablet pen. Plug-ins, objects that implement the [**IStylusSyncPlugin**](/windows/win32/RTSCom/?branch=master) or [**IStylusAsyncPlugin**](/windows/win32/RTSCom/?branch=master) interface, can be added to a **RealTimeStylus** object. Synchronous plug-ins are generally called directly by the **RealTimeStylus** object on a high-priority thread, while asynchronous plug-ins are generally called on the application's user interface (UI) thread. Create or use synchronous plug-ins for tasks that require real-time access to the data stream and are computationally undemanding, such as packet filtering. Create or use asynchronous plug-ins for tasks that do not require real-time access to the data stream, such as ink collection.

Certain tasks may be computationally demanding yet require real-time access to the data stream, such as multistroke gesture recognition. To address these needs, the StylusInput APIs provide a cascaded [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) model that allows you to use two **RealTimeStylus** objects, each running on its own thread. For more information about the cascaded **RealTimeStylus** model, see [The Cascaded RealTimeStylus Model](the-cascaded-realtimestylus-model.md).

Both the [**IStylusSyncPlugin**](/windows/win32/RTSCom/?branch=master) and [**IStylusAsyncPlugin**](/windows/win32/RTSCom/?branch=master) interfaces define the same methods. These methods allow the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object to pass the pen data to each plug-in, regardless of whether it is an asynchronous or synchronous plug-in. The [Microsoft.StylusInput.IStylusSyncPlugin.DataInterest](frlrfMicrosoftStylusInputIStylusSyncPluginClassDataInterestTopic) and [Microsoft.StylusInput.IStylusAsyncPlugin.DataInterest](frlrfMicrosoftStylusInputIStylusAsyncPluginClassDataInterestTopic) properties allow each plug-in to subscribe to specific data in the tablet pen data stream. A plug-in should only subscribe to the data necessary to perform its task, minimizing performance demands. For more information about threading and the **RealTimeStylus** class, see [Threading Considerations for the StylusInput APIs](threading-considerations-for-the-stylusinput-apis.md).

The [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object uses objects in the [Microsoft.StylusInput.PluginData](frlrfMicrosoftStylusInputPluginData) namespace to pass the pen data to its plug-ins. The **RealTimeStylus** also catches exceptions thrown by plug-ins. When the **RealTimeStylus** catches exceptions, it notifies the plug-ins by calling the [IStylusSyncPlugin.Error](frlrfMicrosoftStylusInputIStylusSyncPluginClassErrorTopic) or [IStylusAsyncPlugin.Error](frlrfMicrosoftStylusInputIStylusAsyncPluginClassErrorTopic) method. For more information about using plug-in data, see [Plug-in Data and the RealTimeStylus](plug-in-data-and-the-realtimestylus-class.md) Class. For more information about error handling, see [Error Handling Considerations for the StylusInput APIs](error-handling-considerations-for-the-stylusinput-apis.md) and the Error Data section of Plug-in Data and the RealTimeStylus Class.

> [!Note]  
> At least one plug-in must be attached to the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object before the **RealTimeStylus** object can be enabled.

 

The following topics describe some common categories of plug-ins.

-   [Ink-Collection Plug-ins](ink-collection-plug-ins.md)
-   [Dynamic-Renderer Plug-ins](dynamic-renderer-plug-ins.md)
-   [Recognizer Plug-ins](recognizer-plug-ins.md)

## Special Considerations

Because of the complex nature of the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object, you should take note of the following.

The [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object throws an exception if a plug-in modifies the plug-in collection to which it is attached. This happens only when the plug-in does so while it is being called by the **RealTimeStylus** object as a member of that collection.

The following C\# example is code that causes the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object to throw an exception.


```C++
using Microsoft.Ink;
using Microsoft.StylusInput;
using Microsoft.StylusInput.PluginData;

// ...
public class thePlugin : Microsoft.StylusInput.IStylusAsyncPlugin
{
    // ...

    // Called when a system gesture occurs.
    public void SystemGesture(RealTimeStylus sender, SystemGestureData data)
    {
        // The following line will cause the realtime stylus that calls this method
        // to throw an exception.
        sender.Dispose();
    }
    
    // ...
}
```



The [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object throws an exception if a plug-in calls the **RealTimeStylus** object's [Dispose](frlrfMicrosoftStylusInputRealTimeStylusClassDisposeTopic) method. This happens only when the plug-in does so while it is being called by the **RealTimeStylus** object.

The following C\# example is code that causes the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object to throw an exception.


```C++
using Microsoft.Ink;
using Microsoft.StylusInput;
using Microsoft.StylusInput.PluginData;

// ...
public class thePlugin : Microsoft.StylusInput.IStylusAsyncPlugin
{
    Microsoft.StylusInput.GestureRecognizer theGestureRecognizer;

    // ...

    // Called when a system gesture occurs.
    public void SystemGesture(RealTimeStylus sender, SystemGestureData data)
    {
        // The following line will cause the realtime stylus that calls this method
        // to throw an exception.
        sender.AsyncPluginCollection.Add(this.theGestureRecognizer);
    }
    
    // ...
}
```



Plug-in collections can be modified while the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object is enabled; however, this can make the behavior of your application harder to predict. When a plug-in is added while the **RealTimeStylus** object is enabled, the **RealTimeStylus** object calls the plug-in's [IStylusAsyncPlugin.RealTimeStylusEnabled](frlrfMicrosoftStylusInputIStylusAsyncPluginClassRealTimeStylusEnabledTopic) or [IStylusSyncPlugin.RealTimeStylusEnabled](frlrfMicrosoftStylusInputIStylusSyncPluginClassRealTimeStylusEnabledTopic) method. When a plug-in is removed while the **RealTimeStylus** object is enabled, the **RealTimeStylus** object calls the plug-in's [IStylusAsyncPlugin.RealTimeStylusDisabled](frlrfMicrosoftStylusInputIStylusAsyncPluginClassRealTimeStylusDisabledTopic) or [IStylusSyncPlugin.RealTimeStylusDisabled](frlrfMicrosoftStylusInputIStylusSyncPluginClassRealTimeStylusDisabledTopic) method. This allows the plug-in to maintain its proper state without having to check the [Enabled](frlrfMicrosoftStylusInputRealTimeStylusClassEnabledTopic) property of the **RealTimeStylus** object.

When a plug-in is added while the [**RealTimeStylus**](/windows/win32/RTSCom/?branch=master) object is enabled, the plug-in data that the plug-in receives may not contain enough information to adequately establish the context of the initial data. For example, the newly added plug-in could start receiving packet data from a pen that is mid-stroke. Similarly, when a plug-in removed while the **RealTimeStylus** object is enabled, the plug-in data that the plug-in has received may be insufficient to finish processing the data.

> [!Note]  
> For overall stability, reset a plug-in's internal state when either its [**RealTimeStylusEnabled**](/windows/win32/RTSCom/nf-rtscom-istylusplugin-realtimestylusenabled?branch=master) or [**RealTimeStylusDisabled**](/windows/win32/RTSCom/nf-rtscom-istylusplugin-realtimestylusdisabled?branch=master) method is called.

 

## Related topics

<dl> <dt>

[The Cascaded RealTimeStylus Model](the-cascaded-realtimestylus-model.md)
</dt> <dt>

[Error Handling Considerations for the StylusInput APIs](error-handling-considerations-for-the-stylusinput-apis.md)
</dt> <dt>

[Plug-in Data and the RealTimeStylus Class](plug-in-data-and-the-realtimestylus-class.md)
</dt> <dt>

[Threading Considerations for the StylusInput APIs](threading-considerations-for-the-stylusinput-apis.md)
</dt> </dl>

 

 


