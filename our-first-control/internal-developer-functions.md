# Internal Functions

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

##### 



