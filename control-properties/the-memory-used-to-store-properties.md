# The Memory Used To Store Properties

Our `__AllocMemProperties` function which is used in the `WM_CREATE` message of the main processing function `_SB_WndProc`, will allocate the memory size required based on a structure we use \(can use other means, I just felt that it was more convenient to use a structure for better organisation of variables used\).

It is entirely possible to expand on this and create a function for the end-user that can make use of the `SIMPLEBUTTON_PROPERTIES` structure and allows the end-user to pass as a parameter to the function. a pointer to a variable defined as a `SIMPLEBUTTON_PROPERTIES` type. The function could then iterate through each field and set the appropriate values for the control to make use of. Additional checks would be required for the size of the passed parameter to ensure it is correct, to prevent buffer overflows for example. A lot of Microsoft's own structures make use of a `cbSize` field to validate the structure.

`__AllocMemProperties` will store the pointer to the memory allocated, in the extra window bytes allocated by `cbWndExtra`, by making use of calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx).

Note: `INTERNAL_PROPERTIES` is a constant that equals `0`, whereas the`EXTERNAL_PROPERTIES` constant equals `4` - created for myself  just for convenience as a wrapper to call the [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) function with the specified offset into the extra memory reserved by `cbWndExtra` at our controls registration.

Internally the `__AllocaMemProperties` function calls either of the following:

`Invoke SetWindowLong, hControl, 0, PtrAllocMem`-  To set the pointer to the memory that will store the internal properties.

`Invoke SetWindowLong, hControl, 4, PtrAllocMem` - To set the pointer to memory that will store the external properties.

`PtrAllocMem` is the pointer to our memory that was allocated using [GlobalAlloc](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366574%28v=vs.85%29.aspx) from within `__AllocMemProperties`

At the end of the `WM_CREATE` message we then call our own initialization routine `_SB_Init` to handle our own setup of our **SimpleButton** control. At this point we can safely assume the memory allocated for our control has been done so, thus we can call our `_SB_Init` function to handle the next step \(see [Initializing Our Control](//initializing-our-control.md)\).

See the [Control Properties](/control-properties.md) section later on for more details on the technical aspect of how we handle setting and getting our control's properties.
