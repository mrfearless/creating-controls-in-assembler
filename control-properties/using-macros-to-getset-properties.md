# Using Macros To Get/Set Properties

When I was using the earlier technique of allocating as much bytes as I wanted in `cbWndExtra` \(without realising or coming across any downsides\) I used a series of macros to help get/set the variables and constants to help define the offsets the macro would get/set, for example:

```x86asm
@Style                      EQU 0h  ; some style flags
@MouseOverFlag              EQU 4h  ; 0 = no mouse over, 1 = mouse is over control
@SelectedState              EQU 8h  ; 0 = not selected, 1 = selected
@ControlFont                EQU 12  ; hFont
...
```

With the constants defined and appropriate named \(using a prefix of '@'\) we could then define macros to get and set our property values. Each constant is an offset into the extra window bytes allocated by `cbWndExtra` during the control's registration via [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx).

The macros then called the [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) or [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) to get or set the value at the appropriate index offset.

Here is and example of two macros used to get and set the font handle - stored at index offset `0x12`

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

The macros could then be placed in code and used like so:

```x86asm
...
_GetFont hControl
mov hFont, eax
...

...
_SetFont hControl, hFont
...
```



