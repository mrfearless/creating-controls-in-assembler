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



