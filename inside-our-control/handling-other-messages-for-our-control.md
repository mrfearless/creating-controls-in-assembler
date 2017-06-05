# Handling Other Messages For Our Control

Along with handling the `WM_CREATE` message, we have to handle a few more, for when the control is destroyed, when it is painted and other features such as mouse and keyboard interaction \(if applicable to our control\).



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

Our custom messages \(`SB_GETPROPERTY` ,`SB_SETPROPERTY`, `SB_GETSTATE` and `SB_SETSTATE`\) are included as well and are just simple calls to our internal helper functions \(or framework library if using one\):

```x86asm
...
.ELSEIF eax == SB_GETPROPERTY ; wParam = dwProperty, lParam = NULL. 
    Invoke __GetExtProperty, hWin, wParam ; EAX = dwPropertyValue
    ret

.ELSEIF eax == SB_SETPROPERTY ; wParam = dwProperty, lParam = dwPropertyValue
    Invoke __SetExtProperty, hWin, wParam, lParam
    ret
.ELSEIF eax == SB_GETSTATE ; wParam = NULL, lParam = NULL. 
    Invoke __GetIntProperty, hWin, @SimpleButtonSelectedState ; EAX = dwState
    ret

.ELSEIF eax == SB_SETSTATE ; wParam = TRUE/FALSE, lParam = NULL
    Invoke __SetIntProperty, hWin, @SimpleButtonSelectedState, wParam
    Invoke InvalidateRect, hWin, NULL, TRUE ; repaint control
    ret    
...
```

Other standard messages are used to handle mouse interactions: the mouse moving over our control, leaving it, clicking on it etc via `WM_LBUTTONDOWN`, `WM_LBUTTONUP`, `WM_MOUSEMOVE`, `WM_MOUSELEAVE` and other standard win32 api messages.

