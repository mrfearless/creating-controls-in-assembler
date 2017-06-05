# The Memory Used To Store Properties

Our `__AllocMemProperties` function which is used in the `WM_CREATE` message of the main processing function `_SB_WndProc`, will allocate the memory size required based on two structures we use named `_SIMPLEBUTTON_PROPERTIES` and `SIMPLEBUTTON_PROPERTIES` \(See [Property Structures](//inside-our-control/property-structures.md) for details\). Of course we could use other means to pass the size of memory required - I just felt that it was more convenient to use a structure for better organisation of the properties used.

It is entirely possible to expand on this and create a function for the end-user that can make use of the **external** `SIMPLEBUTTON_PROPERTIES` structure and allow the end-user to pass as a parameter to the function. a pointer to a variable defined as a `SIMPLEBUTTON_PROPERTIES` type. The function could then iterate through each field and set the appropriate values for the control to make use of. Additional checks would be required for the size of the passed parameter to ensure it is correct, to prevent buffer overflows for example. A lot of Microsoft's own structures make use of a `cbSize` field to validate the structure.

`__AllocMemProperties` will store the pointer to the memory allocated, in the extra window bytes allocated by `cbWndExtra`, by making use of calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx).

Note: `INTERNAL_PROPERTIES` is a constant that equals `0`, whereas the`EXTERNAL_PROPERTIES` constant equals `4` - created for myself  just for convenience as a wrapper to call the [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) function with the specified offset into the extra memory reserved by `cbWndExtra` at our controls registration.

Internally the `__AllocaMemProperties` function calls either of the following:

`Invoke SetWindowLong, hControl, 0, PtrAllocMem`-  To set the pointer to the memory that will store the internal properties.

`Invoke SetWindowLong, hControl, 4, PtrAllocMem` - To set the pointer to memory that will store the external properties.

`PtrAllocMem` is the pointer to our memory that was allocated using [GlobalAlloc](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366574%28v=vs.85%29.aspx) from within `__AllocMemProperties`

