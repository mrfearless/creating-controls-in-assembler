# Property Structures

Note that I use two structures for defining the variables  I will use in the control internally and externally \(named `_SIMPLEBUTTON_PROPERTIES` and `SIMPLEBUTTON_PROPERTIES`\), although they aren't used directly by most controls, instead I opt to use constant values that are offsets into the allocated memory.

For example, the **internal** variables structure I use is defined as such:

```x86asm
_SIMPLEBUTTON_PROPERTIES   STRUCT
    dwEnabledState         DD ?
    dwMouseOver            DD ?
    dwSelectedState        DD ?
    dwMouseDown            DD ?
_SIMPLEBUTTON_PROPERTIES   ENDS
```

with the variables \(or properties\) used, defined as:

```x86asm
@SimpleButtonEnabledState  EQU 0
@SimpleButtonMouseOver     EQU 4
@SimpleButtonSelectedState EQU 8
@SimpleButtonMouseDown     EQU 12
```

So the memory block that the four internal variables are stored in, start at offset 0 for the first one, offset 4 for the next and so on \(assuming all variables are dword values of course\). We will see later on getting and setting these internal and external variables using the helper functions: `__GetIntProperty`, `__SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`.

