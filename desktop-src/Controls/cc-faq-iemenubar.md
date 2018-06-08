---
title: How to Create an Internet Explorer-Style Menu Bar
description: At first glance, the menu bar in Microsoft Internet Explorer 5 and later looks similar to a standard menu. However, it looks quite different when you begin using it.
ms.assetid: e0fe25f2-3d49-4c5a-a3f9-2f468f2cfef2
ms.technology: desktop
ms.prod: windows
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# How to Create an Internet Explorer-Style Menu Bar

At first glance, the menu bar in Microsoft Internet Explorer 5 and later looks similar to a standard menu. However, it looks quite different when you begin using it.

The following screen shot shows the Windows Internet Explorer menu bar with the Tools menu selected.

![screen shot that shows the windows internet explorer menu bar, with the tools menu selected](images/howto8.jpg)

The menu bar is actually a toolbar control that looks like a standard menu. Instead of top-level menu items, a menu bar has a series of text-only buttons that display a drop-down menu when clicked. However, as a specialized type of toolbar, a menu bar has some capabilities that standard menus lack:

-   It can be customized using standard toolbar techniques, allowing the user to move, delete, or add items.
-   It can have hot-tracking, so that users will know when they are over a top-level item without having to click it first.

A menu bar can be incorporated into a rebar control, giving it the following features:

-   It can have a gripper, which allows the user to move or resize the band.
-   It can share a strip in the rebar control with other bands, such as the standard toolbar in the preceding illustration.
-   It can display a chevron when it is covered by an adjacent band, giving the user access to the hidden items.
-   It can have an application-defined background bitmap.

This topic discusses how to implement a menu bar. Since a menu bar is a specialized implementation of a toolbar control, the focus will be on topics that are specific to menu bars. For a discussion of how to incorporate a toolbar into a rebar control, see [How to Create an Internet Explorer-Style Toolbar](cc-faq-ietoolbar.md) and [About Rebar Controls](rebar-controls.md).

-   [Making a Toolbar into a Menu Bar](#making-a-toolbar-into-a-menu-bar)
-   [Handling Navigation with Menu Hot-Tracking Disabled](#handling-navigation-with-menu-hot-tracking-disabled)
    -   [Mouse Navigation](#mouse-navigation)
    -   [Keyboard Navigation](#keyboard-navigation)
    -   [Mixed Navigation](#mixed-navigation)
-   [Handling Navigation with Menu Hot-Tracking Enabled](#handling-navigation-with-menu-hot-tracking-enabled)
    -   [Message Processing for Menu Hot-Tracking](#message-processing-for-menu-hot-tracking)
    -   [Mouse Navigation](#mouse-navigation)
    -   [Keyboard Navigation](#keyboard-navigation)

## Making a Toolbar into a Menu Bar

To make a toolbar into a menu bar:

-   Create a flat toolbar by including [**TBSTYLE\_FLAT**](toolbar-control-and-button-styles.md) with the other window style flags. The **TBSTYLE\_FLAT** style also enables hot-tracking. With this style, the menu bar looks much like a standard menu until the user activates a button. Then, the button appears to stand out from the toolbar and depress when it is clicked, just like a standard button. Because hot-tracking is enabled, all that is needed to activate a button is for the cursor to hover over it. If the cursor moves to another button, it will be activated and the old button deactivated.
-   Create list-style buttons by including [**TBSTYLE\_LIST**](toolbar-control-and-button-styles.md) with the other window style flags. This style creates a thinner button that looks more like a standard top-level menu item.
-   Make the buttons text-only by setting the **iBitmap** member of the button's [**TBBUTTON**](/windows/desktop/api/Commctrl/ns-commctrl-_tbbutton) structure to I\_IMAGENONE and the **iString** member to the button text.
-   Give each button the [**BTNS\_DROPDOWN**](toolbar-control-and-button-styles.md) style. When the button is clicked, the toolbar control sends your application a [TBN\_DROPDOWN](tbn-dropdown.md) notification to prompt it to display the button's menu.
-   Incorporate the menu bar into a rebar band. Enable both grippers and chevrons, as discussed in [How to Create an Internet Explorer-Style Toolbar](cc-faq-ietoolbar.md).
-   Implement a [TBN\_DROPDOWN](tbn-dropdown.md) handler to display the button's *drop-down menu* when it is clicked. The drop-down menu is a type of pop-up menu. It is created by using the [**TrackPopupMenu**](https://msdn.microsoft.com/library/windows/desktop/ms648002) function, with its upper-left corner aligned with the lower-left corner of the button.
-   Implement keyboard navigation, so that the menu bar is fully accessible without a mouse.
-   Implement menu hot-tracking. With standard menus, once a top level menu item's menu has been displayed, moving the cursor over another top-level item automatically displays its menu and collapses the menu of the previous item. The toolbar control will hot-track the cursor and change the button image, but it does automatically display the new menu. Your application must do so explicitly.

Most of these items are straightforward to implement and are discussed elsewhere. See [How to Create an Internet Explorer-Style Toolbar](cc-faq-ietoolbar.md), [About Toolbar Controls](toolbar-controls-overview.md), or [About Rebar Controls](rebar-controls.md) for a general discussion of how to use toolbars and rebar controls. See [Menus](https://msdn.microsoft.com/library/windows/desktop/ms646977) for a discussion of how to handle pop-up menus. The final two items, keyboard navigation and menu hot-tracking, are discussed in the remainder of this topic.

## Handling Navigation with Menu Hot-Tracking Disabled

Users can choose to navigate the menu bar with the mouse, the keyboard, or a mixture of both. To implement menu bar navigation, your application must handle toolbar notifications and monitor the mouse and keyboard. This task can be broken into two distinct parts: with and without menu hot-tracking. This section discusses how to handle navigation when no menus are displayed and menu hot-tracking is not enabled.

### Mouse Navigation

If menu hot-tracking is disabled, you can treat a menu bar as a normal toolbar. If the user is navigating with a mouse, all your application needs to do is handle the [TBN\_DROPDOWN](tbn-dropdown.md) notification. When this notification is received, display the appropriate drop-down menu, and enable menu hot-tracking. From then on, you are in the menu hot-tracking regime discussed in [Handling Navigation with Menu Hot-Tracking Enabled](#handling-navigation-with-menu-hot-tracking-enabled).

As discussed in [Mixed Navigation](#mixed-navigation), you also need to handle the [TBN\_HOTITEMCHANGE](tbn-hotitemchange.md) notification to keep track of the active button. This notification is irrelevant if the user is only navigating with the mouse, but it is necessary when mouse and keyboard navigation are mixed.

### Keyboard Navigation

As noted in the previous section, the user can do a number of navigation operations with the keyboard when menu hot-tracking is disabled. Toolbar controls support keyboard navigation only if they have focus. They also do not handle all the key presses that are needed for menu bars. The simplest solution to handling keyboard navigation is to process the relevant key press events explicitly.

If menu hot-tracking is disabled, your application must handle these key press events in the following way:

-   The F10 key. Activate the first button with [**TB\_SETHOTITEM**](tb-sethotitem.md).
-   The LEFT ARROW and RIGHT ARROW keys. Activate the adjacent button with [**TB\_SETHOTITEM**](tb-sethotitem.md). If the user attempts to navigate off either end of the menu bar, activate the first button at the opposite end.
-   The ESCAPE key. Deactivate the active button with [**TB\_SETHOTITEM**](tb-sethotitem.md). The menu bar should be returned to the state it had prior to activating the first button.
-   An ALT-*Key* accelerator key. Use the [**TB\_MAPACCELERATOR**](tb-mapaccelerator.md) message to determine which button the *Key* character corresponds to. Display the button's drop-down menu and enable menu hot-tracking.
-   The DOWN ARROW key. If a button is active but no menu has been displayed, display the button's menu and enable menu hot-tracking.
-   The ENTER key. Deactivate the active button with [**TB\_SETHOTITEM**](tb-sethotitem.md). The menu bar should be returned to the state it had prior to activating the first button.

### Mixed Navigation

The keyboard navigation handlers outlined in the preceding section basically do two tasks: keep track of the active button and display the appropriate menu when a button is selected. These handlers are sufficient as long as the user navigates only with the keyboard. However, users often mix keyboard and mouse navigation. For example, they might activate the first button with the F10 key, use the mouse to activate a different button, and then open its menu with the DOWN ARROW key. If you are only monitoring key presses to keep track of the active key, you will display the wrong menu. You must also handle the [TBN\_HOTITEMCHANGE](tbn-hotitemchange.md) notification to accurately keep track of the active button.

## Handling Navigation with Menu Hot-Tracking Enabled

When menu hot-tracking is enabled, your application must change the way it responds to user navigation. To replicate the behavior of standard menus, you must implement the following features explicitly.

With mouse navigation:

-   If the user moves the cursor over another button, that menu immediately appears and the previous menu disappears.
-   Menu hot-tracking stays active until the user selects a command or clicks a point that is not part of the menu region. Either action deactivates menu hot-tracking until another button is clicked.
-   If the cursor moves off the menu, the current drop-down menu remains until the cursor moves back onto, or the user clicks a point outside, the area covered by the menu. If the cursor returns to a different button than the one currently being displayed, the old menu is collapsed and the new menu is displayed.

With keyboard navigation:

-   The RIGHT ARROW key moves the focus to the right. If the item has a submenu, display the submenu. If the item does not have a submenu, collapse the menu and any submenus, activate the next button with [**TB\_SETHOTITEM**](tb-sethotitem.md), and display the menu for the adjacent button. If the last button is active when this message is received, display the menu for the first button.
-   The LEFT ARROW key moves the focus to the left. If the item is a submenu, collapse it and shift focus to its parent menu. If the item is not a submenu, collapse the menu, activate the next button with [**TB\_SETHOTITEM**](tb-sethotitem.md), and display the menu for that button.

<!-- -->

-   The ESCAPE key backs the display up one step.
    -   If a submenu is displayed, it is collapsed and focus shifts to the parent menu.
    -   If a button's menu is displayed, it is collapsed and menu hot-tracking is disabled. The item's button remains active.
    -   If no menus are displayed but a button is active, the button is deactivated and menu hot-tracking is disabled.
-   The UP ARROW and DOWN ARROW keys are used only to navigate within a particular menu.
-   The ENTER key launches the command associated with a menu item. If the menu item has a submenu, the ENTER key displays it.

As with the menu hot-tracking disabled case, your application must be able to handle mouse, keyboard, and mixed navigation. However, the fact that a menu is displayed means that messaging will have to be handled somewhat differently.

### Message Processing for Menu Hot-Tracking

Menu hot-tracking requires that a menu be displayed at all times, apart from the brief interval required to switch to a new menu. However, the drop-down menu that is displayed by [**TrackPopupMenu**](https://msdn.microsoft.com/library/windows/desktop/ms648002) is modal. Your application will continue to receive some messages directly, including [**WM\_COMMAND**](https://msdn.microsoft.com/library/windows/desktop/ms647591), [TBN\_HOTITEMCHANGE](tbn-hotitemchange.md), and normal menu-related messages such as [**WM\_MENUSELECT**](https://msdn.microsoft.com/library/windows/desktop/ms646352). However, it will not receive low-level keyboard or mouse messages directly. To handle messages such as [**WM\_MOUSEMOVE**](https://msdn.microsoft.com/library/windows/desktop/ms645616), you must set a message hook to intercept messages that are directed to the menu.

When a drop-down menu is displayed, set the message hook by calling the [**SetWindowsHookEx**](https://msdn.microsoft.com/library/windows/desktop/ms644990) function with the *idHook* parameter set to WH\_MSGFILTER. All messages intended for the menu will be passed to your [hook procedure](https://msdn.microsoft.com/library/windows/desktop/ms644987). For example, the following code fragment sets a message hook that will trap messages that are going to a drop-down menu. `MsgHook` is the name of the hook procedure, and `hhookMsg` is the handle to the procedure.


```
hhookMsg = SetWindowsHookEx(WH_MSGFILTER, MsgHook, HINST_THISDLL, 0);
```



Menu messages are identified by setting the hook procedure's *nCode* parameter to MSGF\_MENU. The *lParam* parameter will point to a [**MSG**](https://msdn.microsoft.com/library/windows/desktop/ms644958) structure with the message. The details of which messages need to be handled, and how, are discussed in the remainder of this topic.

Your application must pass all messages on to the next message hook by calling the [**CallNextHookEx**](https://msdn.microsoft.com/library/windows/desktop/ms644974) function. You must also send mouse messages directly to the toolbar control, making sure to convert any point coordinates to the toolbar client coordinate space. Sending the messages directly ensures that the toolbar control receives the appropriate mouse messages. For instance, the toolbar needs to receive [**WM\_MOUSEMOVE**](https://msdn.microsoft.com/library/windows/desktop/ms645616) messages in order to hot-track its buttons.

When a new button is activated, your application must collapse the old drop-down menu with a [**WM\_CANCELMODE**](https://msdn.microsoft.com/library/windows/desktop/ms632615) message, and display a new menu. It must also collapse the drop-down menu when focus is taken from the menu bar with keyboard navigation or by clicking outside the menu area. Whenever you collapse a menu, you should free its message hook by using the [**UnhookWindowsHookEx**](https://msdn.microsoft.com/library/windows/desktop/ms644993) function. If you need to display another drop-down menu, create a new message hook. When a command is launched, the menu will be collapsed automatically but you must explicitly free the message hook.

The following code example frees the message hook that was set in the previous example.


```
UnhookWindowsHookEx(hhookMsg);
```



### Mouse Navigation

When a normal toolbar control hot-tracks buttons, it highlights the active button and sends the application a [TBN\_HOTITEMCHANGE](tbn-hotitemchange.md) notification each time a new button is activated. Your application is responsible for displaying the appropriate drop-down menu. It must:

-   Handle the [TBN\_HOTITEMCHANGE](tbn-hotitemchange.md) notification to keep track of the active button. When the active button changes, collapse the old menu and create a new one.
-   Handle the [TBN\_DROPDOWN](tbn-dropdown.md) notification that is sent when a button is clicked. It should then collapse the menu and disable menu hot-tracking. The button remains active.
-   Handle the [**WM\_LBUTTONDOWN**](https://msdn.microsoft.com/library/windows/desktop/ms645607), [**WM\_RBUTTONDOWN**](https://msdn.microsoft.com/library/windows/desktop/ms646242), and [**WM\_MOUSEMOVE**](https://msdn.microsoft.com/library/windows/desktop/ms645616) messages in your message hook procedure, to keep track of the mouse position. If the mouse is clicked outside the menu area, collapse the current drop-down menu, deactivate menu hot-tracking, and return the menu bar to its preactivation state.
-   When a menu command is launched, disable menu hot-tracking. The menu will be collapsed automatically but you must explicitly free the message hook.

You must also handle menu-related messaging, such as the [**WM\_INITMENUPOPUP**](https://msdn.microsoft.com/library/windows/desktop/ms646347) message that is sent when a menu item needs to display a submenu. For a discussion of how to handle such messages, see [Menus](https://msdn.microsoft.com/library/windows/desktop/ms646977).

### Keyboard Navigation

Your application must process keyboard messages in the message hook procedure and act on those that affect menu hot-tracking. The following key presses must be handled:

-   The ESCAPE key. The ESCAPE key backs the display up one level. If a submenu is being displayed, it must be collapsed. If a button's primary menu is displayed, collapse it and disable menu hot-tracking. The button remains active.
-   The RIGHT ARROW key. If the item has a submenu, display it. If the item does not have a submenu, collapse the menu and any submenus, activate the next button with [**TB\_SETHOTITEM**](tb-sethotitem.md), and display its menu. If the last button was active when this notification was received, display the menu for the first button.
-   The LEFT ARROW key. If the item is in a submenu, collapse it and shift focus to its parent menu. If the item is not a submenu, collapse the menu, activate the adjacent button with [**TB\_SETHOTITEM**](tb-sethotitem.md), and display its menu. If the first button was active when this notification was received, display the menu for the last button.
-   The UP ARROW and DOWN ARROW keys. These keys are used to navigate within a menu but do not directly affect menu hot-tracking.
-   An ALT-*Key* accelerator key. Use the [**TB\_MAPACCELERATOR**](tb-mapaccelerator.md) message to determine which button the *Key* character corresponds to. If it corresponds to a different button than the currently active one, collapse the current drop-down menu, activate the new button with [**TB\_SETHOTITEM**](tb-sethotitem.md), and display the menu for the adjacent button. If the *Key character* corresponds to the currently displayed button, collapse the drop-down menu and disable menu hot-tracking. The button should remain active.

 

 



