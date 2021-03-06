---
title: WM_CAP_SEQUENCE message (Vfw.h)
description: The WM\_CAP\_SEQUENCE message initiates streaming video and audio capture to a file. You can send this message explicitly or by using the capCaptureSequence macro.
ms.assetid: 33d53abc-e37e-48c6-bfc8-9cd02fde5cb6
keywords:
- WM_CAP_SEQUENCE message Windows Multimedia
topic_type:
- apiref
api_name:
- WM_CAP_SEQUENCE
api_location:
- Vfw.h
api_type:
- HeaderDef
ms.topic: reference
ms.date: 05/31/2018
---

# WM\_CAP\_SEQUENCE message

The **WM\_CAP\_SEQUENCE** message initiates streaming video and audio capture to a file. You can send this message explicitly or by using the [**capCaptureSequence**](/windows/desktop/api/Vfw/nf-vfw-capcapturesequence) macro.


```C++
WM_CAP_SEQUENCE 
wParam = (WPARAM) 0; 
lParam = 0L; 
```



## Return Value

Returns **TRUE** if successful or **FALSE** otherwise.

If an error occurs and an error callback function is set using the [**WM\_CAP\_SET\_CALLBACK\_ERROR**](wm-cap-set-callback-error.md) message, the error callback function is called.

## Remarks

If you want to alter the parameters controlling streaming capture, use the [**WM\_CAP\_SET\_SEQUENCE\_SETUP**](wm-cap-set-sequence-setup.md) message prior to starting the capture.

By default, the capture window does not allow other applications to continue running during capture. To override this, either set the **fYield** member of the [**CAPTUREPARMS**](/windows/win32/api/vfw/ns-vfw-captureparms) structure to **TRUE**, or install a yield callback function.

During streaming capture, the capture window can optionally issue notifications to your application of specific types of conditions. To install callback procedures for these notifications, use the following messages:

-   [**WM\_CAP\_SET\_CALLBACK\_ERROR**](wm-cap-set-callback-error.md)
-   [**WM\_CAP\_SET\_CALLBACK\_STATUS**](wm-cap-set-callback-status.md)
-   [**WM\_CAP\_SET\_CALLBACK\_YIELD**](wm-cap-set-callback-yield.md)
-   [**WM\_CAP\_SET\_CALLBACK\_VIDEOSTREAM**](wm-cap-set-callback-videostream.md)
-   [**WM\_CAP\_SET\_CALLBACK\_WAVESTREAM**](wm-cap-set-callback-wavestream.md)

## Requirements



|                                     |                                                                                  |
|-------------------------------------|----------------------------------------------------------------------------------|
| Minimum supported client<br/> | Windows 2000 Professional \[desktop apps only\]<br/>                       |
| Minimum supported server<br/> | Windows 2000 Server \[desktop apps only\]<br/>                             |
| Header<br/>                   | <dl> <dt>Vfw.h</dt> </dl> |



## See also

<dl> <dt>

[Video Capture](video-capture.md)
</dt> <dt>

[Video Capture Messages](video-capture-messages.md)
</dt> </dl>

 

 





