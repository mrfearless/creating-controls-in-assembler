# Initializing Our Control {#initializing-our-control}

`_SB_Init` function is called from the `WM_CREATE` message of the `_SB_WndProc` main window messaging function. It initializes our **SimpleButton** with some default values: like colors for background and text and others like default font to use. In `_SB_Init` we also check the `dwStyle` flags passed to the **SimpleButton** control \(via direct call to [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx), call to `SimpleButtonCreate` or via dialog resource child control creation\) and we can override the `dwStyle` flags specified and/or force specific flags to be used or excluded \(in case the end-user forgot\), for example we can set our control to always use `WM_CHILD` plus `WM_VISIBLE` etc:

```x86asm
    ...
    ; get style and check it is our default at least
    Invoke GetWindowLong, hControl, GWL_STYLE
    mov dwStyle, eax
    and eax, WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
    .IF eax != WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
        mov eax, dwStyle
        or eax, WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
        mov dwStyle, eax
        Invoke SetWindowLong, hControl, GWL_STYLE, dwStyle
    .ENDIF
    ...
```

We also set some default values for our control's external properties, like standard colors that might be used for text and background when creating our control. So some of the default values we will set for our control's properties are:

```x86asm
    ...
    Invoke __SetExtProperty, hControl, @SimpleButtonTextColor, SBRGBCOLOR(51,51,51)
    Invoke __SetExtProperty, hControl, @SimpleButtonBackColor, SBRGBCOLOR(255,255,255)
    Invoke __SetExtProperty, hControl, @SimpleButtonBorderColor, SBRGBCOLOR(204,204,204)
    Invoke __SetExtProperty, hControl, @SimpleButtonBorderStyle, SBES_ALL
    ...
```

Only some of the external properties are shown for brevity, the ones that are shown are define in `SimpleButton.inc` as:

```x86asm
@SimpleButtonTextColor          EQU 4
@SimpleButtonBackColor          EQU 24
@SimpleButtonBorderColor        EQU 44
@SimpleButtonBorderStyle        EQU 64
```

For color properties \([COLORREF](https://msdn.microsoft.com/en-us/library/vs/alm/dd183449%28v=vs.85%29.aspx)\) we make use of a macro that is included in `SimpleButton.inc`:

```x86asm
SBRGBCOLOR MACRO red:REQ, green:REQ, blue:REQ
    EXITM < red or green shl 8 or blue shl 16 >
ENDM
```

This means the user can use the **SimpleButton** control straight away, or they can choose to modify any of the external properties to customize it to their requirements after the control is created, whichever suits. See the Simple Button internal and external variables section later on for more details.

