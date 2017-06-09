# Control Properties

This section and the topics within it should be considered a bit more technical in nature. They cover more in depth the details on the techniques used for defining, allocating memory for, and setting/getting a control's properties.

When registering our custom control \(see [Registering Our Control](//registering-our-control.md) for details\), the `cbWndExtra` field of the [WNDCLASSEX](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633577%28v=vs.85%29.aspx) is used for storing additional extra bytes per instance of the control we create.

In the early days of my control creation I used these extra storage bytes to store anywhere up to 128 bytes of data to hold various variables used by the control. I came across some documentation later on that suggested there was a limit of 40 bytes in some earlier versions of windows os, and that as the bytes are being allocated from the local system heap, the [RegisterClassEx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633587%28v=vs.85%29.aspx) function might fail if greater than 40 bytes is requested - [https://msdn.microsoft.com/en-us/library/ms633574\(VS.85\).aspx\#extra\_window\_memory](https://msdn.microsoft.com/en-us/library/ms633574%28VS.85%29.aspx#extra_window_memory)

> Extra Window Memory
>
> The system maintains an internal data structure for each window. When registering a window class, an application can specify a number of additional bytes of memory, called extra window memory. When creating a window of the class, the system allocates and appends the specified amount of extra window memory to the end of the window's structure. An application can use this memory to store window-specific data.
>
> Because extra memory is allocated from the system's local heap, an application should use extra window memory sparingly. The RegisterClassEx function fails if the amount of extra window memory requested is greater than 40 bytes. If an application requires more than 40 bytes, it should allocate its own memory and store a pointer to the memory in the extra window memory.
>
> The SetWindowLong function copies a value to the extra memory. The GetWindowLong function retrieves a value from the extra memory. The cbWndExtra member of the WNDCLASSEX structure specifies the amount of extra window memory to allocate. An application that does not use the memory must initialize cbWndExtra to zero.

Once the extra bytes are allocated, they can accessed and read via calls to [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) and passing the handle of the window \(our control's handle\) and an index offset to the bytes required. For setting the values we use the calls to [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx), again passing the window handle, an offset to the bytes to be set and a value to set.

