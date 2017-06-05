# Using Our Control

The end-user will make use of our control **SimpleButton**, using the functions, custom messages, and constants \(properties\) that we define and are available for use in our SimpleButton.inc file.

Just to remind ourselves, these are the **external** functions available to the end-user:

```x86asm
SimpleButtonRegister    PROTO
SimpleButtonCreate      PROTO :DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD
SimpleButtonGetProperty PROTO :DWORD, :DWORD
SimpleButtonSetProperty PROTO :DWORD, :DWORD, :DWORD
SimpleButtonGetState    PROTO :DWORD
SimpleButtonSetState    PROTO :DWORD, :DWORD
```

And the custom messages for our control are:

```x86asm
SB_GETPROPERTY          EQU WM_USER + 1800
SB_SETPROPERTY          EQU WM_USER + 1799
SB_GETSTATE             EQU WM_USER + 1798
SB_SETSTATE             EQU WM_USER + 1797
```

With very little code we can create our control, all we need to define is the text to display and the resource id we will use for our control:

```x86asm
.const
IDC_SB1                 EQU 1001 ; resource id to use

.data
SBText                  DB 'SimpleButton Rules!',0 ; text to display

.data?
hSB1                    DD ? ; handle for SimpleButton control
```

And we then call our `SimpleButtonCreate` function and save the returned handle \(in `eax`\) in a variable:

```x86asm
Invoke SimpleButtonCreate, hWin, Addr SBText, 40, 60, 200, 30, IDC_SB1, /
                           WS_CHILD or WS_VISIBLE or SBBS_CENTER or /
                           SBBS_HAND or SBBS_PUSHBUTTON
mov hSB1, eax ; save handle to our control
```

Our `SimpleButtonCreate` function takes a number of parameters:

* `hWndParent` \(`DWORD`\) is the parent handle of our main dialog window, with which to create a child control for.
* `xpos` \(`DWORD`\) is the left position of our control relative to the parent's client space.
* `ypos` \(`DWORD`\) is the top position of our control relative to the parent's client space.
* `controlwidth` \(`DWORD`\) is the width of our control.
* `controlheight` \(`DWORD`\) is the height of our control
* `dwResourceID` \(`DWORD`\) is the resource id used by our control
* `dwStyle` \(`DWORD`\) is a combination of constants \(flags\) that define some features of our control.

The `dwStyle` parameter can accept windows style flags: `WS_CHILD`, `WS_VISIBLE`, and flags we defined for our controls usage in `SimpleButton.inc`:

```x86asm
SBBS_CENTER             EQU 0h  ; Align text centrally (default)
SBBS_LEFT               EQU 1h  ; Align text to the left of the button
SBBS_HAND               EQU 2h  ; Show a hand when mouse moves over button.
SBBS_PUSHBUTTON         EQU 4h  ; Simulate button movement when clicked.
SBBS_AUTOSTATE          EQU 8h  ; Automatically toggle state when clicked.
```

With our example code above, we create our control with the the text aligned in the center \(`SBBS_CENTER`\) and an option to show a hand cursor when the mouse moves over our control \(`SBBS_HAND`\), and we simulate a small movement of our control when it is clicked  \(`SBBS_PUSHBUTTON`\) - the **SimpleButton** controil moves very slightly down when the left click button is pressed and back again when the left click button is released.

Additional properties of our control which are defined in our SimpleButton.inc file allow us to change the look and feel. This is the full list of properties \(constant values\) that we define for **SimpleButton**:

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

The end-user can choose which properties are set, with default values having been applied to the control when we initialized it \(see [Initializing Our Control](//initializing-our-control.md) section for details\)

For example we can set the text color of our control, the border color and the border color when the mouse moves over our control:

```x86asm
Invoke SimpleButtonSetProperty, hSB1, @SimpleButtonTextColor, SBRGBCOLOR(26,103,140)
Invoke SimpleButtonSetProperty, hSB1, @SimpleButtonBorderColor, SBRGBCOLOR(27,161,226)
Invoke SimpleButtonSetProperty, hSB1, @SimpleButtonBorderColorAlt, SBRGBCOLOR(27,161,226)
```

As we have specified the resource id defined for our control \(`IDC_SB1`\), we can handle processing of when our control is clicked using the standard WM\_COMMAND message in our example project's main dialog mesasge processing procedure:

```x86asm
...
.ELSEIF eax == WM_COMMAND
    mov eax, wParam
    and eax, 0FFFFh
    .IF eax == IDM_FILE_EXIT
        Invoke SendMessage,hWin,WM_CLOSE,0,0
			
    .ELSEIF eax == IDM_HELP_ABOUT
        Invoke ShellAbout,hWin,addr AppName,addr AboutMsg,NULL
		
    .ELSEIF eax == IDC_SB1 ; handle our SimpleButton being clicked, show a message.
        Invoke MessageBox, NULL, Addr szMsgText, Addr szMsgTitle, MB_OK
			
    .ENDIF
...
```



