# On Destroying: WM\_NCDESTROY

When the control is destroyed we free the memory we allocated and call a cleanup routine for any other business \(if required\). Note our `_SB_Cleanup` function in our `WM_NCDESTROY` message \(shown below,\) is called **first** whilst the memory for our variables/properties still exists - This is important in case we have to free any other resources, handles or objects that we have stored previously in our internal or external properties.

```x86asm
...
.ELSEIF eax == WM_NCDESTROY
    Invoke _SB_Cleanup, hWin ; cleanup any other stuff as required
    Invoke __FreeMemProperties, hWin, INTERNAL_PROPERTIES
    Invoke __FreeMemProperties, hWin, EXTERNAL_PROPERTIES
    mov eax, 0
    ret
...
```

Our internal helper function `__FreeMemProperties` used [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) to retrieve the appropriate pointer for the internal and external memory blocks used to store our variables and then frees that memory with calls to [GlobalFree](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366579%28v=vs.85%29.aspx).

