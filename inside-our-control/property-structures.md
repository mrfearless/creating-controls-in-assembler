# Property Structures & Constants

Note that in the `WM_CREATE` message of the main processing function `_SB_WndProc`, I use two structures for defining the variables that I will use in the control **internally** _and_ **externally** \(named `_SIMPLEBUTTON_PROPERTIES` and `SIMPLEBUTTON_PROPERTIES`\), although they aren't used directly by most controls, instead I opt to use constant values that are offsets into the allocated memory.

For example, the **internal** variables structure I use is defined as such:

```x86asm
_SIMPLEBUTTON_PROPERTIES   STRUCT
    dwEnabledState         DD ?
    dwMouseOver            DD ?
    dwSelectedState        DD ?
    dwMouseDown            DD ?
_SIMPLEBUTTON_PROPERTIES   ENDS
```

with the **internal** variables \(or properties\) used, defined as constants:

```x86asm
@SimpleButtonEnabledState  EQU 0
@SimpleButtonMouseOver     EQU 4
@SimpleButtonSelectedState EQU 8
@SimpleButtonMouseDown     EQU 12
```

So the memory block that the four internal variables are stored in, starts at offset 0 for the first one, offset 4 for the next and so on \(assuming all variables are dword values of course\). We will see later on getting and setting these **internal** and **external** variables using the helper functions: `__GetIntProperty`, `__SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`.

The **external** properties and structure \(`SIMPLEBUTTON_PROPERTIES)`\) is defined similarly in the `SimpleButton.inc` file for the end-user. Typically the end-user wont make use of the structure directly, its just added as a convienance - mainly for the use by the developer. It is easier to use `SIZEOF` _structurename_ to pass to the `__AllocMemProperties` function to determine the size of memory to allocate for our control's properties.

Here is the list of **external** properties constants that we define for the end-user:

```x86asm
@SimpleButtonTextFont            EQU 0  ; hFont
@SimpleButtonTextColor           EQU 4  ; Colorref
@SimpleButtonTextColorAlt        EQU 8  ; Colorref
@SimpleButtonTextColorSel        EQU 12 ; Colorref
@SimpleButtonTextColorSelAlt     EQU 16 ; Colorref
@SimpleButtonTextColorDisabled   EQU 20 ; Colorref
@SimpleButtonBackColor           EQU 24 ; Colorref
@SimpleButtonBackColorAlt        EQU 28 ; Colorref
@SimpleButtonBackColorSel        EQU 32 ; Colorref
@SimpleButtonBackColorSelAlt     EQU 36 ; Colorref
@SimpleButtonBackColorDisabled   EQU 40 ; Colorref
@SimpleButtonBorderColor         EQU 44 ; Colorref
@SimpleButtonBorderColorAlt      EQU 48 ; Colorref
@SimpleButtonBorderColorSel      EQU 52 ; Colorref
@SimpleButtonBorderColorSelAlt   EQU 56 ; Colorref
@SimpleButtonBorderColorDisabled EQU 60 ; Colorref
@SimpleButtonBorderStyle         EQU 64 ; Border Style Flags
```



