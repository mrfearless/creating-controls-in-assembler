# Registering Our Control

The `SimpleButtonCreate` function will call our `SimpleButtonRegister` function to register the class associated with our control. So why do we need the `SimpleButtonRegister` function?, surely we can just handle all this in the `SimpleButtonCreate` function?

Well, there are number of reasons we want to have a `SimpleButtonRegister` function. When we register a custom window class \(our control\) we allow the creation of it via the [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx "https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680\(v=vs.85\).aspx") api call if we specify the classname to create, and also we can facilitate the creation of our control via a dialog resource.

RadASM allows a custom control to be placed on a dialog and all it requires is a classname. When you look at the dialog resource file it shows the control and the classname. At runtime when the dialog is instantiated, it will automatically enumerate and create all controls in the dialog, and if our custom **SimpleButton** control is registered it will allow the dialog to create the control for our usage. This technique will apply to other IDEs that allow you to specify the control classname \(or if you need, you can edit the Dialog's .rc resource file directly to include this information\)

The image below shows what a .rc dialog resource file might look like when creating our custom control in this manner:

![](/assets/SimpleButtonResourceCreation2.png)

Using RadASM's UserDefinedControl, shown in the image below, we can specify the text to be used in the `Caption` property, the class to use, which in this example will be '_SimpleButton\_Control_' and we can also modify the `xStyle` property flags to include `0x6` which corresponds to `SBBS_HAND` plus `SBBS_PUSHBUTTON`. These flags are covered in more detail later on.

![](/assets/IDC_SB2.png)

Later in our code we can retrieve the handle to the dialog's child control that is our **SimpleButton**, and use this handle to set other properties of our control, like in the example image below:

![](/assets/GetDlgItemHandle.png)

So we allow our end-user the ability to create our custom **SimpleButton** control, directly with the `SimpleButtonCreate` function, or via the [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) function or via a resource dialog control entry. The last two will require the `SimpleButtonRegister` function to have been called before the call to [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) or before the dialog is created.

##### SimpleButtonRegister

The `SimpleButtonRegister` function makes use of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx\) structure to define the behaviour of our control. When you create a win32 api control via [CreateWindowEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms632680%28v=vs.85%29.aspx) using one of the predefined class names: static, edit, button, listbox etc, once it returns you have a handle to that control that can be used in other related api functions for that control. But where is the main code routine and how is it defined. Well the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) structure is where this main function for the class is defined.

Here is our `SimpleButtonRegister` function, that registers out new **SimpleButton** control and sets the main internal procedure \(`_SB_WndProc`\) that we use for handling the messages to this control.

In the .data section we create a string that will be unique for use with our control:

```x86asm
.DATA
SimpleButtonClass DB "SimpleButton_Control",0
```

And this is the control's register function:

```x86asm
;-------------------------------------------------------------------------------------
; SimpleButtonRegister - Registers the SimpleButton control
; Can be used at start of program for use with RadASM custom control
; Custom control class must be set as 'SimpleButton_Control'
;-------------------------------------------------------------------------------------
SimpleButtonRegister PROC PUBLIC
    LOCAL wc:WNDCLASSEX
    LOCAL hinstance:DWORD

    Invoke GetModuleHandle, NULL
    mov hinstance, eax

    invoke GetClassInfoEx, hinstance, Addr SimpleButtonClass, Addr wc 
    .IF eax == 0 ; if class not already registered do so
        mov wc.cbSize, SIZEOF WNDCLASSEX
        lea eax, SimpleButtonClass
        mov wc.lpszClassName, eax
        mov eax, hinstance
        mov wc.hInstance, eax
        mov wc.lpfnWndProc, OFFSET _SB_WndProc ; points to main controls window proc
        mov wc.hCursor, NULL ; not set, we can handle this in our main window proc
        mov wc.hIcon, 0
        mov wc.hIconSm, 0
        mov wc.lpszMenuName, NULL ; no menu needed
        mov wc.hbrBackground, NULL ; handle drawing ourselves in main window proc
        mov wc.style, NULL
        mov wc.cbClsExtra, 0
        mov wc.cbWndExtra, 8 ; dword ptr to internal & external properties memory
        Invoke RegisterClassEx, Addr wc
    .ENDIF  
    ret
SimpleButtonRegister ENDP
```

When registering a class with [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx), the `lpszClassName` field of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) will hold our custom classname \(in this case '_SimpleButton\_Control_'\) and the `lpfnWndProc` field will hold the address of our custom controls main window procedure \(`_SB_WndProc`\), which is very much like a standard windows procedure, that handles specific `WM_` messages.

The `cbWndExtra` of [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) is used to store some extra bytes for use with our control. See the [Control Properties](/control-properties.md) section later on for more details.

