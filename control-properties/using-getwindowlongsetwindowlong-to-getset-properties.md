# Using GetWindowLong/SetWindowLong To Get/Set Properties

Ultimately, as the controls I created became more complex with more internal variables stored in the `cbWndExtra` bytes, I changed my technique to only use 8 bytes in the `cbWndExtra` field. One `DWORD` value \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stored internal variables, and another `DWORD` value \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stored variables that are visible and can be modified externally by the user of the control.

The actual allocation of memory for these structures used for internal and external variables is handled in the `WM_CREATE` message of the main controls window procedure \(see [Inside Our Control](//inside-our-control.md) for details\). 

In our example **SimpleButton** control the `_SB_WndProc` procedure calls the `__AllocMemProperties` function to allocate thie memory and store the pointer in the appropriate `cbWndExtra` location - \(`Invoke GetWindowLong, hControl, 0`\) for a pointer to a structure that stores **internal** variables, and \(`Invoke GetWindowLong, hControl, 4`\) for a pointer to a structure that stores **external** variables.

