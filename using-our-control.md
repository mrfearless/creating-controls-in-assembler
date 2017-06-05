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

