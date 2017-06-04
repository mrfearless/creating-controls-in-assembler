# Inside Our Control

`_SB_WndProc` is a standard main window messaging procedure, that handles specific `WM_` messages for our **SimpleButton** control. We have complete control over what messages are handled but we also have responsibility to handle specific messages, as we are implementing the core code ourselves and have to handle painting, mouse & keyboard interaction, and other features.

We wont cover all the messages implemented in the `_SB_WndProc`, see the code for the full project if you wish to see more detail. We will cover a few important messages that the control handles.

##### Handling The First Message: WM\_CREATE vs WM\_NCCREATE

We use the `WM_CREATE` message of our **SimpleButton** control to initialize the memory that will store blocks of memory for our internal and external variables that will be used by us and by any end-user, and we then call our own initialization routine. I opted to use `WM_CREATE` instead of `WM_NCCREATE` because I found over the years that sometimes the `cbWndExtra` extra data wasn't being allocated sometimes, and any initialization routine that set some defaults values in these extra memory areas could cause an invalid memory address error. I found moving all this code to the `WM_CREATE` was more reliable.

`WM_NCCREATE` is one of the first messages a control receives and the `WM_NCDESTROY` message is one of the last. Its still possible to make use of the `WM_NCCREATE` message to handle some basic setup of controls, with the above mentioned caveat taken into account.

Our `WM_CREATE` message of the `_SB_WndProc` function looks like this:

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



