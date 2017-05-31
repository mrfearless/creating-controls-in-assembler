# Painting Our Control - \_SB\_Paint {#sbpaint}

In the `_SB_Paint` function we make the task of painting our **SimpleButton** control a bit easier for ourselves by separating the main tasks into other sub-functions: `_SB_PaintBackground`, `_SB_PaintText` and `_SB_PaintBorder`. We create a double buffer beforehand and BitBlt after the calls to the other `_SB_Paint` sub-functions. 

`_SB_Paint` and the other sub-functions make use of our both our internal and external variables \(with calls to the internal helper functions: `__GetIntProperty`, `__GetExtProperty`\) to detect states \(selected/not selected\), mouse over \(or not\) and to control styles, colors, fonts and other attributes used in our **SimpleButton** control.

The sub functions typically are passed some internal variables as parameters, for example:

```
    ...
    LOCAL EnabledState:DWORD
    LOCAL MouseOver:DWORD
    LOCAL SelectedState:DWORD
    ...

    ...
    Invoke __GetIntProperty, hControl, @SimpleButtonEnabledState
    mov EnabledState, eax
    Invoke __GetIntProperty, hControl, @SimpleButtonMouseOver
    mov MouseOver, eax
    Invoke __GetIntProperty, hControl, @SimpleButtonSelectedState
    mov SelectedState, eax  

    Invoke _SB_PaintBackground, hControl, hdcMem, Addr rect, EnabledState, MouseOver, SelectedState
    Invoke _SB_PaintText, hControl, hdcMem, Addr rect, EnabledState, MouseOver, SelectedState
    Invoke _SB_PaintBorder, hControl, hdcMem, Addr rect, EnabledState, MouseOver, SelectedState
    ...
```

With all these attributes the end-user will have control our how the **SimpleButton** will look depending on the default properties set and any additional ones the end-user sets themselves.

