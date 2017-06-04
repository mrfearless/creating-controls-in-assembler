# Control Properties

When registering our custom control \(see [Registering Our Control](//registering-our-control.md) for details\), the `cbWndExtra` field of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) is used for storing additional extra bytes per instance of the control we create.

In the early days of my control creation I used these extra storage bytes to store anywhere up to 128 bytes of data to hold various variables used by the control. I came across some documentation later on that suggested there was a limit of 40 bytes in some earlier versions of windows os, and that as the bytes are being allocated from the local system heap, the [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx) function might fail if greater than 40 bytes is requested. See [https://msdn.microsoft.com/en-us/library/ms633574\(VS.85\).aspx\#extra\_window\_memory](https://msdn.microsoft.com/en-us/library/ms633574%28VS.85%29.aspx#extra_window_memory)

Once the extra bytes are allocated, they can accessed and read via calls to [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) \(or [GetWindowLongPtr](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633585%28v=vs.85%29.aspx) if compiling for x64\) and passing the handle of the window \(our control's handle\) and an index offset to the bytes required. For setting the values we use the calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) \(or [SetWindowLongPtr](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644898%28v=vs.85%29.aspx) if compiling for x64\), again passing the window handle, an offset to the bytes to be set and a value to set.

[GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) retrieves the 32-bit `DWORD` value at the specified offset into the extra window memory, whereas [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) sets a 32-bit `DWORD` value at the specified offset into the extra window memory. The extra window memory is reserved by specifying a nonzero value in the `cbWndExtra` member of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) structure used with the [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx) function.

##### 

##### 

##### 

##### 



