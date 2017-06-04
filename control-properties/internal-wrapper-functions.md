# Internal Wrapper Functions

The helper functions, used in our example **SimpleButton** control, are just wrapper functions using [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) and [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx) to get and set the internal and external variables/properties: `__GetIntProperty`,  `__SetIntProperty`, `__GetExtProperty` and `__SetExtProperty`.

For the end-user who will use our **SimpleButton** control they can access the **external** properties via calls to `SimpleButtonGetProperty` and `SimpleButtonSetProperty` or by using the custom messages: `SB_GETPROPERTY` and `SB_SETPROPERTY` with the SendMessage api call. These functions then call the appropriate internal helper functions \(wrapper functions for [GetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633584%28v=vs.85%29.aspx) and [SetWindowLong](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633591%28v=vs.85%29.aspx)\).

The **internal** variables/properties are _**not**_ exposed to the end-user by design \(this can of course be changed if you need to or your control requires it for whatever reason\).

I borrowed my earlier technique of using constants to name the properties/variables used, and to define the offsets into the memory blocks, as this allows us to define easily readable property \(or variable\) names to use with out helper functions. It is much easier to use and read code like:

```x86asm
Invoke __GetExtProperty, hControl, @SimpleButtonBackColor
```

than:

```x86asm
Invoke __GetExtProperty, hControl, 24
```

We can clearly see the property we are referring to, in this case it relates to a back color or background color of our control.

