---
Description: You can use the InkPicture Control to render ink on Microsoft Windows 2000, Windows Server 2003, and non-Tablet PC editions of Windows XP. However, you cannot use the control to input ink on these operating systems.
ms.assetid: e5d00b0f-0a4f-4ea2-ae63-dfb6bc8c2caf
title: Using InkPicture on Earlier Versions of Windows
ms.date: 05/31/2018
ms.topic: article
ms.author: windowssdkdev
ms.prod: windows
ms.technology: desktop
---

# Using InkPicture on Earlier Versions of Windows

You can use the [InkPicture Control](inkpicture-control.md) to render ink on Microsoft Windows 2000, Windows Server 2003, and non-Tablet PC editions of Windows XP. However, you cannot use the control to input ink on these operating systems.

The control's [**InkEnabled**](/windows/win32/msinkaut/?branch=master) property is set to **FALSE**, and attempting to change the property has no effect. You can assign values to other properties and programmatically manipulate ink, but you cannot use the control to collect ink.

 

 


