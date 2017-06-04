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

