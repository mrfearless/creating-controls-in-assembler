# Creating Our Control

The `SimpleButtonCreate` function will allow the user to create the **SimpleButton** control directly with this function. The parameters for `SimpleButtonCreate` are `hWndParent`, `lpszText`, `xpos`, `ypos`, `controlwidth`, `controlheight`, `dwResourceID`, and finally `dwStyle`. This is more or less typical for creating a control with [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) and as such we try to keep our function as similar to that as we can, so that it is easier for the end user to use.

Most of the parameters of the `SimpleButtonCreate` function are self-explanatory: the width, height, position of the control, the text to display, the parent handle of our child control and the resource id number associated with the control.

The `dwStyle` parameter gives us a number of options to allow us or the user to tailor the control to our requirements, based on the styles we define for its usage.

Unfortunately their isn't much in the microsoft documentation to indicate what values we can pass to the `dwStyle` parameter apart from the standard windows defined ones: `WS_BORDER`, `WS_VISIBLE`, `WS_CHILD` etc

The most useful information I came across that explains the `dwStyle` flags parameter of [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) is from Raymond Chen's old new thing [blog](https://blogs.msdn.microsoft.com/oldnewthing/20031203-00/?p=41633/)

> The low 16 bits of the dwStyle parameter are defined by the implementer of the window class \(by the person who calls RegisterClass\)

That means we have `0x0` - `0xFFFF` reserved for defining styles for our controls. And we can incorporate the standard style flags as well if we so desire \(`WS_BORDER`, `WS_VISIBLE` etc\). So our **SimpleButton** styles we will use are:

```x86asm
; SimpleButton Button Styles:
SBBS_CENTER             EQU 0h  ; Align text centrally
SBBS_LEFT               EQU 1h  ; Align text to the left of the button
SBBS_HAND               EQU 2h  ; Show a hand when mouse moves over button.
SBBS_PUSHBUTTON         EQU 4h  ; Simulate button movement when clicked.
SBBS_AUTOSTATE          EQU 8h  ; Automatically toggle state when clicked.

; SimpleButton Edge (Border) Styles:
SBES_NONE               EQU 0
SBES_LEFT               EQU 1
SBES_TOP                EQU 2
SBES_BOTTOM             EQU 4
SBES_RIGHT              EQU 8
SBES_ALL                EQU SBES_LEFT + SBES_TOP + SBES_BOTTOM + SBES_RIGHT
```

So our `SimpleButtonCreate` function is just a wrapper to register the control if not already registered and a call to [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) to create it, returning the handle to the newly created control in `eax`. Also we check with the `dwStyle` parameter which flags are passed to `SimpleButtonCreate`, to ensure for example that `WS_CHILD` and `WS_VISIBLE` is included and any other flags we might want as well. This check is also performed in the `_SB_Init` procedure in case our **SimpleButton** control was created via a dialog resource.

Of course you are free to define how you want your control to behave at creation and what flags if any you want to force to be included or even excluded from use.

Here is our `SimpleButtonCreate` function:

```x86asm
;-------------------------------------------------------------------------------------
; SimpleButtonCreate - Returns handle in eax of newly created control or NULL otherwise
;-------------------------------------------------------------------------------------
SimpleButtonCreate PROC PRIVATE hWndParent:DWORD, lpszText:DWORD, xpos:DWORD, /
                                ypos:DWORD, controlwidth:DWORD, controlheight:DWORD, /
                                dwResourceID:DWORD, dwStyle:DWORD
    LOCAL wc:WNDCLASSEX
    LOCAL hinstance:DWORD
    LOCAL hControl:DWORD
    LOCAL dwNewStyle:DWORD

    Invoke GetModuleHandle, NULL
    mov hinstance, eax

    Invoke SimpleButtonRegister ; register our control if its not already registered.

    mov eax, dwStyle
    mov dwNewStyle, eax
    and eax, WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
    .IF eax != WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
        or dwNewStyle, WS_CHILD or WS_VISIBLE or WS_CLIPCHILDREN
    .ENDIF

    Invoke CreateWindowEx, NULL, Addr SimpleButtonClass, lpszText, dwNewStyle, xpos, /
                           ypos, controlwidth, controlheight, hWndParent, / 
                           dwResourceID, hinstance, NULL
    mov hControl, eax
    .IF eax != NULL
        ; any other code to handle something else here, 
        ; otherwise we return handle in eax or NULL if failed to create control
    .ENDIF
    mov eax, hControl
    ret
SimpleButtonCreate ENDP
```



