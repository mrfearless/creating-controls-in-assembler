# External End-User Functions

Functions that will defined for use **externally** by the end-user of our control will be:

```x86asm
SimpleButtonRegister    PROTO
SimpleButtonCreate      PROTO :DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD,:DWORD
SimpleButtonGetProperty PROTO :DWORD, :DWORD
SimpleButtonSetProperty PROTO :DWORD, :DWORD, :DWORD
SimpleButtonGetState    PROTO :DWORD
SimpleButtonSetState    PROTO :DWORD, :DWORD
```

The `SimpleButton.asm` will contain these functions _and_ other functions designed to be used internally by the control itself. I prefix the functions that will be used **internally** with an **underscore** and an **abbreviation**, but you can use whatever naming convention you desire.

##### Custom Messages For The End-User

Our two custom message which are used in conjunction with [SendMessage](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644950%28v=vs.85%29.aspx) api call, can be used instead of the `SimpleButtonSetProperty` / `SimpleButtonGetProperty` functions, are defined as:

```x86asm
SB_GETPROPERTY           EQU WM_USER + 1800
SB_SETPROPERTY           EQU WM_USER + 1799
```

We will cover the usage of some of these functions and mesages later on.

