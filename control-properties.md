# Control Properties

When registering our custom control \(see [Registering Our Control](//registering-our-control.md) for details\), the `cbWndExtra` field of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) is used for storing additional extra bytes per instance of the control we create.

In the early days of my control creation I used these extra storage bytes to store anywhere up to 128 bytes of data to hold various variables used by the control. I came across some documentation later on that suggested there was a limit of 40 bytes in some earlier versions of windows os, and that as the bytes are being allocated from the local system heap, the [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx) function might fail if greater than 40 bytes is requested. See [https://msdn.microsoft.com/en-us/library/ms633574\(VS.85\).aspx\#extra\_window\_memory](https://msdn.microsoft.com/en-us/library/ms633574%28VS.85%29.aspx#extra_window_memory)

Once the extra bytes are allocated, they can accessed and read via calls to [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) \(or [GetWindowLongPtr](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633585%28v=vs.85%29.aspx) if compiling for x64\) and passing the handle of the window \(our control's handle\) and an index offset to the bytes required. For setting the values we use the calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) \(or [SetWindowLongPtr](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644898%28v=vs.85%29.aspx) if compiling for x64\), again passing the window handle, an offset to the bytes to be set and a value to set.

[GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) retrieves the 32-bit `DWORD` value at the specified offset into the extra window memory, whereas [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) sets a 32-bit `DWORD` value at the specified offset into the extra window memory. The extra window memory is reserved by specifying a nonzero value in the `cbWndExtra` member of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) structure used with the [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx) function.

##### Using Macros To Get/Set Properties

When I was using the earlier technique of allocating as much bytes as I wanted in `cbWndExtra` \(without realising or coming across any downsides\) I used a series of macros to help get/set the variables and constants to help define the offsets the macro would get/set, for example:

```x86asm
@Style                      EQU 0h  ; some style flags
@MouseOverFlag              EQU 4h  ; 0 = no mouse over, 1 = mouse is over control
@SelectedState              EQU 8h  ; 0 = not selected, 1 = selected
@ControlFont                EQU 12  ; hFont
...
```

```x86asm
;-------------------------------------------------------------------------------------
; _GetFont - helper function - Gets the Controls font
;-------------------------------------------------------------------------------------
_GetFont MACRO hControl:REQ
    Invoke GetWindowLong, hControl, @ControlFont        
ENDM

;-------------------------------------------------------------------------------------
; _SetFont - helper function - Sets the Controls font
;-------------------------------------------------------------------------------------
_SetFont MACRO hControl:REQ, ptrControlData:REQ
    Invoke SetWindowLong, hControl, @ControlFont, ptrControlData
ENDM
```

This is still a valid technique if the amount of bytes is less than 40. Or even using the [GetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633564%28v=vs.85%29.aspx) / [SetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633568%28v=vs.85%29.aspx) api calls is viable if using an atom - which is faster than [GetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633564%28v=vs.85%29.aspx) / [SetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633568%28v=vs.85%29.aspx) with a string, but both are still slower than [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) / [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) as far as I am aware.

##### Using GetWindowLong/SetWindowLong To Get/Set Properties

Ultimately, as the controls I created became more complex with more internal variables stored in the `cbWndExtra` bytes, I changed my technique to only use 8 bytes in the `cbWndExtra` field. One `DWORD` value \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stored internal variables, and another `DWORD` value \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stored variables that are visible and can be modified externally by the user of the control.

The actual allocation of memory for these structures used for internal and external variables is handled in the `WM_CREATE` message of the main controls window procedure. In our example **SimpleButton** control the `_SB_WndProc` procedure calls the `__AllocMemProperties` function to allocate thie memory and store the pointer in the appropriate `cbWndExtra` location - \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stores **internal** variables, and \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stores **external** variables.

##### Internal Wrapper Functions

The helper functions, used in our example **SimpleButton** control, are just wrapper functions using [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) and [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) to get and set the internal and external variables/properties: `__GetIntProperty`,  `__SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`.

For the end-user who will use our **SimpleButton** control they can access the **external** properties via calls to `SimpleButtonGetProperty` and `SimpleButtonSetProperty` or by using the custom messages: `SB_GETPROPERTY` and `SB_SETPROPERTY` with the SendMessage api call. These functions then call the appropriate internal helper functions \(wrapper functions for [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) and [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx)\).

The **internal** variables/properties are _**not**_ exposed to the end-user by design \(this can of course be changed if you need to or your control requires it for whatever reason\).

I borrowed my earlier technique of using constants to name the properties/variables used, and to define the offsets into the memory blocks, as this allows us to define easily readable property \(or variable\) names to use with out helper functions. It is much easier to use and read code like:

```x86asm
Invoke __GetExtProperty, hControl, @SimpleButtonBackColor
```

than:

```x86asm
Invoke __GetExtProperty, hControl, 24
```

We can clearly see the property we are referring to, in this case it relates to a back color or background color of our control.

##### RadASM Auto-complete

Another reason I used this approach is RadASM's auto-complete feature. By editing two .api files and adding some information I can include this information to make it easier to code using the **SimpleButton** control. So I easily can add autocomplete for the `SimpleButtonGetProperty` function and it will show a dropdown list of possible values when I am ready to supply the 2nd parameter \(`dwProperty`\)

Contents of RadASM\Masm\masmApiCall.api:

```
SimpleButtonGetProperty,hSimpleButton,dwProperty
```

Contents of RadASM\Masm\masmApiConst.api:

```
2SimpleButtonGetProperty,@SimpleButtonTextFont,@SimpleButtonTextColor,@SimpleButtonTextColorAlt,
@SimpleButtonTextColorSel,@SimpleButtonTextColorSelAlt,@SimpleButtonTextColorDisabled,@SimpleButtonBackColor,
@SimpleButtonBackColorAlt,@SimpleButtonBackColorSel,@SimpleButtonBackColorSelAlt,@SimpleButtonBackColorDisabled,
@SimpleButtonBorderColor,@SimpleButtonBorderColorAlt,@SimpleButtonBorderColorSel,@SimpleButtonBorderColorSelAlt,
@SimpleButtonBorderColorDisabled,@SimpleButtonBorderStyle
```

This is what RadASM's auto-complete looks like when implemented:

![](/assets/SimpleButtonGetPropertyAutoComplete.gif)

The project source also includes the `masmApiCall.api.txt`, `masmApiConst.api.txt`and `masmMessage.api.txt` text files that incorporate these calls and constants for use with RadASM's autocomplete feature, you just need to open the appropriate .api file and paste the contents at the end of the file and restart RadASM for them to be available to you. Instructions are included in the text files themselves as to which file it related to and the typical location of those .api files.

