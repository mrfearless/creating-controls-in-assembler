# Inside Our Control

`_SB_WndProc` is a standard main window messaging procedure, that handles specific `WM_` messages for our **SimpleButton** control. We have complete control over what messages are handled but we also have responsibility to handle specific messages, as we are implementing the core code ourselves and have to handle painting, mouse & keyboard interaction, and other features.

We wont cover all the messages implemented in the `_SB_WndProc`, see the code for the full project if you wish to see more detail. We will cover a few important messages that the control handles.

### Handling The First Message: WM\_CREATE vs WM\_NCCREATE

We use the `WM_CREATE` message of our **SimpleButton** control to initialize the memory that will store blocks of memory for our internal and external variables that will be used by us and by any end-user, and we then call our own initialization routine. I opted to use `WM_CREATE` instead of `WM_NCCREATE` because I found over the years that sometimes the `cbWndExtra` extra data wasn't being allocated sometimes, and any initialization routine that set some defaults values in these extra memory areas could cause an invalid memory address error. I found moving all this code to the `WM_CREATE` was more reliable.

`WM_NCCREATE` is one of the first messages a control receives and the `WM_NCDESTROY` message is one of the last. Its still possible to make use of the `WM_NCCREATE` message to handle some basic setup of controls, with the above mentioned caveat taken into account.

Our `WM_CREATE` message of the `_SB_WndProc` function for our **SimpleButton** control looks like this:

```x86asm
...
.ELSEIF eax == WM_CREATE
    Invoke __AllocMemProperties, hWin, INTERNAL_PROPERTIES, SIZEOF _SIMPLEBUTTON_PROPERTIES
    Invoke __AllocMemProperties, hWin, EXTERNAL_PROPERTIES, SIZEOF SIMPLEBUTTON_PROPERTIES
    Invoke _SB_Init, hWin
    mov eax, 0
    ret 
...
```

### Property Structures

Note that I use two structures for defining the variables  I will use in the control internally and externally \(named `_SIMPLEBUTTON_PROPERTIES` and `SIMPLEBUTTON_PROPERTIES`\), although they aren't used directly by most controls, instead I opt to use constant values that are offsets into the allocated memory.

For example, the **internal** variables structure I use is defined as such:

```x86asm
_SIMPLEBUTTON_PROPERTIES        STRUCT
    dwEnabledState              DD ?
    dwMouseOver                 DD ?
    dwSelectedState             DD ?
    dwMouseDown                 DD ?
_SIMPLEBUTTON_PROPERTIES        ENDS
```

with the variables \(or properties\) used, defined as:

```x86asm
@SimpleButtonEnabledState       EQU 0
@SimpleButtonMouseOver          EQU 4
@SimpleButtonSelectedState      EQU 8
@SimpleButtonMouseDown          EQU 12
```

So the memory block that the four internal variables are stored in, start at offset 0 for the first one, offset 4 for the next and so on \(assuming all variables are dword values of course\). We will see later on getting and setting these internal and external variables using the helper functions: `__GetIntProperty`, `__SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`.

### The Memory Used To Store Our Controls Properties

Our `__AllocMemProperties` function will allocate the memory size required based on a structure we use \(can use other means, I just felt that it was more convenient to use a structure for better organisation of variables used\).

`__AllocMemProperties` will then store the pointer to this memory in the bytes allocated by `cbWndExtra`, by making use of calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx)

`INTERNAL_PROPERTIES` is a constant that equals `0`, whereas the`EXTERNAL_PROPERTIES` constant equals `4` - again just for convenience as a wrapper to call the [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) function with the specified offset into the memory reserved by `cbWndExtra` at our controls registration.

Internally the `__AllocaMemProperties` function calls either of the following:

`Invoke SetWindowLong, hControl, 0, PtrAllocMem`-  To set the pointer to the memory that will store the internal properties.

`Invoke SetWindowLong, hControl, 4, PtrAllocMem` - To set the pointer to memory that will store the external properties.

`PtrAllocMem` is the pointer to our memory that was allocated using [GlobalAlloc](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366574%28v=vs.85%29.aspx) from within `__AllocMemProperties`

At the end of the `WM_CREATE` message we then call our own initialization routine `_SB_Init` to handle our own setup of our **SimpleButton** control.

See the Simple Button internal and external variables section later on for more details.

### Handling Other Messages For Our Control

Along with handling the `WM_CREATE` message as described above, we have to handle a few more, for when the control is destroyed, when it is painted and other features such as mouse and keyboard interaction \(if applicable to our control\).

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

