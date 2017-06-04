# Handling Other Messages For Our Control

Along with handling the `WM_CREATE` message, we have to handle a few more, for when the control is destroyed, when it is painted and other features such as mouse and keyboard interaction \(if applicable to our control\).

When the control is destroyed we free the memory we allocated and call a cleanup routine for any other business \(if required\). Note our `_SB_Cleanup` function in our `WM_NCDESTROY` message \(shown below,\) is called **first** whilst the memory for our variables/properties still exists - This is important in case we have to free any other resources, handles or objects that we have stored previously in our internal or external properties.

```x86asm
...
.ELSEIF eax == WM_NCDESTROY
    Invoke _SB_Cleanup, hWin ; cleanup any other stuff as required
    Invoke __FreeMemProperties, hWin, INTERNAL_PROPERTIES
    Invoke __FreeMemProperties, hWin, EXTERNAL_PROPERTIES
    mov eax, 0
    ret
...
```

Our internal helper function `__FreeMemProperties` used [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) to retrieve the appropriate pointer for the internal and external memory blocks used to store our variables and then frees that memory with calls to [GlobalFree](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366579%28v=vs.85%29.aspx).

We handle our own painting in our **SimpleButton** like so with these two messages:

```x86asm
...
.ELSEIF eax == WM_ERASEBKGND
    mov eax, 1
    ret

.ELSEIF eax == WM_PAINT
    Invoke _SB_Paint, hWin
    mov eax, 0
    ret
...
```

We are specifying that we will handle erasing of our control's background ourself and painting of our **SimpleButton** control via our own paint function `_SB_Paint`

We can also allow our **SimpleButton** control to be enabled or disabled via a standard `WM_ENABLE` message, which forces a repaint of our control. And similarly we can allow a font change and repaint via a `WM_SETFONT` message.

```x86asm
...
.ELSEIF eax == WM_ENABLE
    Invoke __SetIntProperty, hWin, @SimpleButtonEnabledState, wParam
    Invoke InvalidateRect, hWin, NULL, TRUE
    mov eax, 0

.ELSEIF eax == WM_SETTEXT
    Invoke DefWindowProc, hWin, uMsg, wParam, lParam
    Invoke InvalidateRect, hWin, NULL, TRUE
    ret

.ELSEIF eax == WM_SETFONT
    Invoke __SetExtProperty, hWin, @SimpleButtonTextFont, lParam
    .IF lParam == TRUE
        Invoke InvalidateRect, hWin, NULL, TRUE
    .ENDIF  
```

Our custom messages \(`SB_GETPROPERTY` and `SB_SETPROPERTY`\) are included as well and are just simple calls to our internal helper functions \(or framework library if using one\):

```x86asm
...
.ELSEIF eax == SB_GETPROPERTY
    Invoke __GetExtProperty, hWin, wParam
    ret

.ELSEIF eax == SB_SETPROPERTY   
    Invoke __SetExtProperty, hWin, wParam, lParam
    ret
...
```

Other standard messages are used to handle mouse interactions: the mouse moving over our control, leaving it, clicking on it etc via `WM_LBUTTONDOWN`, `WM_LBUTTONUP`, `WM_MOUSEMOVE`, `WM_MOUSELEAVE` and other standard win32 api messages.

