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

Here is and example of some macros used to get and set the various 'properties' as defined above as constants:

```x86asm
_GetFont MACRO hControl:REQ
    Invoke GetWindowLong, hControl, @ControlFont        
ENDM

_SetFont MACRO hControl:REQ, ptrControlData:REQ
    Invoke SetWindowLong, hControl, @ControlFont, ptrControlData
ENDM

_GetMouseOverFlag MACRO hControl:REQ
    Invoke GetWindowLong, hControl, @MouseOverFlag        
ENDM

_SetMouseOverFlag MACRO hControl:REQ, ptrControlData:REQ
    Invoke SetWindowLong, hControl, @MouseOverFlag, ptrControlData
ENDM

_GetSelectedState MACRO hControl:REQ
    Invoke GetWindowLong, hControl, @SelectedState
ENDM

_SetSelectedState MACRO hControl:REQ, ptrControlData:REQ
    Invoke SetWindowLong, hControl, @SelectedState, ptrControlData
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

...
_SetMouseOverFlag hControl, TRUE
...

...
_GetSelectedState hControl
mov bState, eax
...
```

Using macros to get and set our control's properties is still a valid technique if the amount of data store for these properties is less than 40 bytes \(see the [Control Properties](/control-properties.md) section and [here](https://msdn.microsoft.com/en-us/library/ms633574%28VS.85%29.aspx#extra_window_memory) for why\). 

It is also possible to make use of the [GetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633564%28v=vs.85%29.aspx) / [SetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633568%28v=vs.85%29.aspx) api calls, which make use of [integer atoms](https://msdn.microsoft.com/en-us/library/windows/desktop/ms649053%28v=vs.85%29.aspx#_win32_Integer_Atoms) - which is faster than [GetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633564%28v=vs.85%29.aspx) / [SetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633568%28v=vs.85%29.aspx) with a [string atoms](https://msdn.microsoft.com/en-us/library/windows/desktop/ms649053%28v=vs.85%29.aspx#_win32_String_Atoms), but use of [GetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633564%28v=vs.85%29.aspx) / [SetProp](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633568%28v=vs.85%29.aspx) with both atoms types are still slower than [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) / [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) as far as I am aware.

