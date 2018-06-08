---
Description: The GetBitmapPalette function returns the first palette entry in a VIDEOINFOHEADER structure.
ms.assetid: 7c620f81-31d9-408f-954d-aeff39f93956
title: GetBitmapPalette function
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# GetBitmapPalette function

The `GetBitmapPalette` function returns the first palette entry in a [**VIDEOINFOHEADER**](/windows/desktop/api/amvideo/ns-amvideo-tagvideoinfoheader) structure.

## Syntax


```C++
const RGBQUAD* GetBitmapPalette(
   const VIDEOINFOHEADER *pVideoInfo
);
```



## Parameters

<dl> <dt>

*pVideoInfo* 
</dt> <dd>

Pointer to a [**VIDEOINFOHEADER**](/windows/desktop/api/amvideo/ns-amvideo-tagvideoinfoheader) structure.

</dd> </dl>

## Return value

Returns a pointer to the first palette entry.

## Requirements



|                    |                                                                                                                                                                                            |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Header<br/>  | <dl> <dt>Wxutil.h (include Streams.h)</dt> </dl>                                                                                    |
| Library<br/> | <dl> <dt>Strmbase.lib (retail builds); </dt> <dt>Strmbasd.lib (debug builds)</dt> </dl> |



 

 



