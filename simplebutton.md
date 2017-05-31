# SimpleButton

I'm going to use a simple button control example to help cover some of the code used in this article. The name of this control will be **SimpleButton**. I wont be covering every single function used, merely the most important or interesting ones. The code for the full project will be available for you to browse and read, including additional comments where relevant.

Firstly I create two files: `SimpleButton.asm` that will contain the main code for the control and an include file called `SimpleButton.inc`. The `SimpleButton.inc` file be included from within `SimpleButton.asm` and will also be distributed along with the compiled static library of the control once we are finished `SimpleButton.lib`.

`SimpleButton.inc` will include function prototypes for users to call that will create and manipulate the **SimpleButton** control. Also included will be any structures or constants that will be required for the user to use in conjunction with the functions we create, and we will also include some custom messages for our control as well.

Functions that will defined for use **externally** by the user of our control will be:

```asm
SimpleButtonRegister            PROTO
SimpleButtonCreate              PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
                                ; hWndParent, lpszText, xpos, ypos, dwWidth, dwHeight, dwResourceID, dwStyle
SimpleButtonGetProperty         PROTO :DWORD, :DWORD            ; hSimpleButton, dwProperty
SimpleButtonSetProperty         PROTO :DWORD, :DWORD, :DWORD    ; hSimpleButton, dwProperty, dwPropertyValue
SimpleButtonGetState            PROTO :DWORD                    ; dwProperty
SimpleButtonSetState            PROTO :DWORD, :DWORD            ; dwProperty, dwPropertyValue
```

The `SimpleButton.asm` will contain these functions and other functions designed to be used internally by the control itself. I prefix the functions that will be used **internally** with an **underscore** and an **abbreviation**, but you can use whatever naming convention you desire.

Functions that will defined for use **internally** by the control will be:

```asm
_SB_WndProc                     PROTO :DWORD, :DWORD, :DWORD, :DWORD
_SB_Init                        PROTO :DWORD
_SB_Cleanup                     PROTO :DWORD
_SB_Paint                       PROTO :DWORD
_SB_PaintBackground             PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
_SB_PaintText                   PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
_SB_PaintBorder                 PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
```

In addition, we will define a few other internal helper functions for use in this control. In my own development I combine the functions next into a library or framework for easy re-use, but I have recreated them here and included them in the `SimpleButton.asm` file just for ease of use and clarity in covering the features in the control. The internal helper functions are:

```asm
__AllocMemProperties            PROTO :DWORD, :DWORD, :DWORD    ; hControl, cbWndExtraOffset, dwSizeToAllocate
__FreeMemProperties             PROTO :DWORD, :DWORD            ; hControl, cbWndExtraOffset
__GetIntProperty                PROTO :DWORD, :DWORD            ; hControl, dwProperty
__SetIntProperty                PROTO :DWORD, :DWORD, :DWORD    ; hControl, dwProperty, dwPropertyValue
__GetExtProperty                PROTO :DWORD, :DWORD            ; hControl, dwProperty
__SetExtProperty                PROTO :DWORD, :DWORD, :DWORD    ; hControl, dwProperty, dwPropertyValue
```

Our two custom message which are used in conjunction with [SendMessage](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644950%28v=vs.85%29.aspx) api call, can be used instead of the `SimpleButtonSetProperty` / `SimpleButtonGetProperty` functions, are defined as:

```asm
SB_GETPROPERTY                  EQU WM_USER + 1800              ; wParam = dwProperty, lParam = NULL
SB_SETPROPERTY                  EQU WM_USER + 1799              ; wParam = dwProperty, lParam = dwValueToSet
```



