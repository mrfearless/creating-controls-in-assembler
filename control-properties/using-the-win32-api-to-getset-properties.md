# Using The Win32 API To Get/Set Properties

Ultimately, as the controls I created became more complex with more internal variables stored in the `cbWndExtra` bytes, I changed my technique to only use 8 bytes in the `cbWndExtra` field. 

I used the GetWindowLong api function to retrieve data in the extra window memory of `cbWndExtra`, and the SetWindowLong api function to set data in this memory.

The data to get or set was one `DWORD` value \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stored internal variables, and another `DWORD` value \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stored variables that are visible and can be modified externally by the user of the control.

The actual allocation of memory for these structures used for internal and external variables is handled in the `WM_CREATE` message of the main controls window procedure by the `__AllocMemProperties` internal function \(see [Inside Our Control](//inside-our-control.md) for details\).

The freeing of this allocated memory is handled by `__FreeMemProperties` internal function in the WM\_NCDESTROY message.

In our example **SimpleButton** control the `_SB_WndProc` procedure calls the `__AllocMemProperties` function to allocate thie memory and store the pointer in the appropriate `cbWndExtra` location - \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stores **internal** variables, and \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stores **external** variables.

