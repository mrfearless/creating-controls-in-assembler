# Our First Control

I'm going to use a simple button control example to help cover some of the code used in this article. The name of this control will be **SimpleButton**. I wont be covering every single function used, merely the most important or interesting ones. The code for the full project including examples is available for you to browse, read and download here:

[https://github.com/mrfearless/SimpleButton](https://github.com/mrfearless/SimpleButton)

Here is what our **SimpleButton** control will look like when its in operation:

![](/assets/SimpleButtonDemo.gif)

Very simple, but for our purposes should be fine to help cover the steps required to create the controls behaviour. We will allow the end-user to specify the background, text and border colors for seperate states such as: selected, enabled and mouse over. We will paint the background, the text and border based on those current states and handle mouse interaction, cursors and emulate a push button effect and/or a toggle state. All these options will be handled by using flags and properties that we define for use with our **SimpleButton** control.

Firstly I create two files: `SimpleButton.asm` that will contain the main code for the control and an include file called `SimpleButton.inc`. The `SimpleButton.inc` file be included from within `SimpleButton.asm` and will also be distributed along with the compiled static library of the control once we are finished `SimpleButton.lib`.

`SimpleButton.inc` will include function prototypes for users to call that will create and manipulate the **SimpleButton** control. Also included will be any structures or constants that will be required for the user to use in conjunction with the functions we create, and we will also include some custom messages for our control as well.

##### 

##### Internal Developer Functions

Functions that will defined for use **internally** by the control will be:

```x86asm
_SB_WndProc             PROTO :DWORD, :DWORD, :DWORD, :DWORD
_SB_Init                PROTO :DWORD
_SB_Cleanup             PROTO :DWORD
_SB_Paint               PROTO :DWORD
_SB_PaintBackground     PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
_SB_PaintText           PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
_SB_PaintBorder         PROTO :DWORD, :DWORD, :DWORD, :DWORD, :DWORD, :DWORD
```

In addition, we will define a few other internal helper functions for use in this control. In my own development projects I combine the functions next into a library or framework for easy re-use, but I have recreated them here and included them in the `SimpleButton.asm` file just for ease of use and clarity in covering the features in the control. The internal helper functions are:

```x86asm
__AllocMemProperties     PROTO :DWORD, :DWORD, :DWORD
__FreeMemProperties      PROTO :DWORD, :DWORD
__GetIntProperty         PROTO :DWORD, :DWORD
__SetIntProperty         PROTO :DWORD, :DWORD, :DWORD
__GetExtProperty         PROTO :DWORD, :DWORD
__SetExtProperty         PROTO :DWORD, :DWORD, :DWORD
```

##### Custom Messages For The End-User

Our two custom message which are used in conjunction with [SendMessage](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644950%28v=vs.85%29.aspx) api call, can be used instead of the `SimpleButtonSetProperty` / `SimpleButtonGetProperty` functions, are defined as:

```x86asm
SB_GETPROPERTY           EQU WM_USER + 1800
SB_SETPROPERTY           EQU WM_USER + 1799
```

We will cover the usage of some of these functions and mesages later on.

