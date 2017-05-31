# Inside Our Control - \_SB\_WndProc {#sbwndproc}

`_SB_WndProc` is a standard main window messaging procedure, that handles specific `WM_` messages for our **SimpleButton** control. We have complete control over what messages are handled but we also have responsibility to handle specific messages, as we are implementing the core code and have to handle painting, mouse & keyboard interaction, and other features ourself.



We wont cover all the messages implemented in the `_SB_WndProc`, see the code for the full project if you wish to see more detail. We will cover a few important messages that the control handles.



We use the `WM_CREATE` message of our **SimpleButton** control to initialize the memory that will store blocks of memory for our internal and external variables that will be used by us and by any end-user, and we then call our own initialization routine. I opted to use `WM_CREATE` instead of `WM_NCCREATE` because I found over the years that sometimes the `cbWndExtra` extra data wasn't being allocated sometimes, and any initialization routine that set some defaults values in these extra memory areas could cause an invalid memory address error. I found moving all this code to the `WM_CREATE` was more reliable.

`WM_NCCREATE` is one of the first messages a control receives and the `WM_NCDESTROY` message is one of the last. Its still possible to make use of the `WM_NCCREATE` message to handle some basic setup of controls, with the above mentioned caveat taken into account.

Our `WM_CREATE` message of our `_SB_WndProc` for our **SimpleButton** control looks like this:

```
    ...
    .ELSEIF eax == WM_CREATE
        Invoke __AllocMemProperties, hWin, INTERNAL_PROPERTIES, SIZEOF _SIMPLEBUTTON_PROPERTIES
        Invoke __AllocMemProperties, hWin, EXTERNAL_PROPERTIES, SIZEOF SIMPLEBUTTON_PROPERTIES
        Invoke _SB_Init, hWin
        mov eax, 0
        ret 
    ...
```

Note that I use two structures for defining the variables  I will use in the control internally and externally \(named `_SIMPLEBUTTON_PROPERTIES` and `SIMPLEBUTTON_PROPERTIES`\), although they aren't used directly by most controls, instead I opt to use constant values that are offsets into the allocated memory. 

For example, the internal variables structure I use is defined as such:

```
_SIMPLEBUTTON_PROPERTIES        STRUCT
    dwEnabledState              DD ?
    dwMouseOver                 DD ?
    dwSelectedState             DD ?
    dwMouseDown                 DD ?
_SIMPLEBUTTON_PROPERTIES        ENDS
```

with the variables \(or properties\) used, defined as:

```
@SimpleButtonEnabledState       EQU 0
@SimpleButtonMouseOver          EQU 4
@SimpleButtonSelectedState      EQU 8
@SimpleButtonMouseDown          EQU 12
```

So the memory block that the four internal variables are stored in, start at offset 0 for the first one, offset 4 for the next and so on \(assuming all variables are dword values of course\). We will see later on getting and setting these internal and external variables using the helper functions: `__GetIntProperty`, ` __SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`. See the Simple Button internal and external variables section later on for more details.



# The Memory Used To Store Our Controls Properties

Our `__AllocMemProperties` function will allocate the memory size required based on a structure we use \(can use other means, I just felt that it was more convenient to use a structure for better organisation of variables used\). 

`__AllocMemProperties` will then store the pointer to this memory in the bytes allocated by `cbWndExtra`, by making use of calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx)

`INTERNAL_PROPERTIES` is a constant that equals `0`, whereas the`EXTERNAL_PROPERTIES` constant equals `4` - again just for convenience as a wrapper to call the [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) function with the specified offset into the memory reserved by `cbWndExtra` at our controls registration. 

Internally the `__AllocaMemProperties` function calls either of the following:

`Invoke SetWindowLong, hControl, 0, PtrAllocMem`-  To set the pointer to the memory that will store the internal properties.

`Invoke SetWindowLong, hControl, 4, PtrAllocMem` - To set the pointer to memory that will store the external properties. 

`PtrAllocMem` is the pointer to our memory that was allocated using [GlobalAlloc](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366574%28v=vs.85%29.aspx) from within `__AllocMemProperties`

At the end of the `WM_CREATE` message we then call our own initialization routine `_SB_Init` to handle our own setup of our **SimpleButton** control

