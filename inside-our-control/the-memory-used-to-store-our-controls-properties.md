# The Memory Used To Store Our Controls Properties

Our `__AllocMemProperties` function will allocate the memory size required based on a structure we use \(can use other means, I just felt that it was more convenient to use a structure for better organisation of variables used\).

`__AllocMemProperties` will then store the pointer to this memory in the bytes allocated by `cbWndExtra`, by making use of calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx)

`INTERNAL_PROPERTIES` is a constant that equals `0`, whereas the`EXTERNAL_PROPERTIES` constant equals `4` - again just for convenience as a wrapper to call the [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) function with the specified offset into the memory reserved by `cbWndExtra` at our controls registration.

Internally the `__AllocaMemProperties` function calls either of the following:

`Invoke SetWindowLong, hControl, 0, PtrAllocMem`-  To set the pointer to the memory that will store the internal properties.

`Invoke SetWindowLong, hControl, 4, PtrAllocMem` - To set the pointer to memory that will store the external properties.

`PtrAllocMem` is the pointer to our memory that was allocated using [GlobalAlloc](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366574%28v=vs.85%29.aspx) from within `__AllocMemProperties`

At the end of the `WM_CREATE` message we then call our own initialization routine `_SB_Init` to handle our own setup of our **SimpleButton** control.

See the [Control Properties](/control-properties.md) section later on for more details.

