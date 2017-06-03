# Painting Our Control

In the `_SB_Paint` function we make the task of painting our **SimpleButton** control a bit easier for ourselves by separating the main tasks into other sub-functions: `_SB_PaintBackground`, `_SB_PaintText` and `_SB_PaintBorder`. We create a double buffer beforehand and BitBlt after the calls to the other `_SB_Paint` sub-functions.

`_SB_Paint` and the other sub-functions make use of our both our internal and external variables \(with calls to the internal helper functions: `__GetIntProperty`, `__GetExtProperty`\) to detect states \(selected/not selected\), mouse over \(or not\) and to control styles, colors, fonts and other attributes used in our **SimpleButton** control.

The sub functions typically are passed some internal variables as parameters, for example:

```x86asm
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

Each of the sub-functions will read a particular external property depending on the status of the internal properties, so in our `_SB_PaintBackground` function, depending on whether the mouse is over the control or not it will fetch a different external property.

Here is the complete `_SB_PaintBackground` function to illustrate this:

```x86asm
;-------------------------------------------------------------------------------------
; _SB_PaintBackground - Paints the background of the SimpleButton control
;-------------------------------------------------------------------------------------
_SB_PaintBackground PROC PRIVATE hControl:DWORD, hdc:DWORD, lpRect:DWORD, bEnabledState:DWORD, bMouseOver:DWORD, bSelectedState:DWORD
    LOCAL BackColor:DWORD
    LOCAL hBrush:DWORD
    LOCAL hOldBrush:DWORD

    .IF bEnabledState == TRUE
        .IF bSelectedState == FALSE
            .IF bMouseOver == FALSE
                Invoke __GetExtProperty, hControl, @SimpleButtonBackColor
            .ELSE
                Invoke __GetExtProperty, hControl, @SimpleButtonBackColorAlt
            .ENDIF
        .ELSE
            .IF bMouseOver == FALSE
                Invoke __GetExtProperty, hControl, @SimpleButtonBackColorSel
            .ELSE
                Invoke __GetExtProperty, hControl, @SimpleButtonBackColorSelAlt 
            .ENDIF
        .ENDIF
    .ELSE
        Invoke __GetExtProperty, hControl, @SimpleButtonBackColorDisabled
    .ENDIF
    .IF eax == 0 ; try to get default back color if others are set to 0
        Invoke __GetExtProperty, hControl, @SimpleButtonBackColor
    .ENDIF
    mov BackColor, eax
    Invoke GetStockObject, DC_BRUSH
    mov hBrush, eax
    Invoke SelectObject, hdc, eax
    mov hOldBrush, eax
    Invoke SetDCBrushColor, hdc, BackColor
    Invoke FillRect, hdc, lpRect, hBrush

    .IF hOldBrush != 0
        Invoke SelectObject, hdc, hOldBrush
        Invoke DeleteObject, hOldBrush
    .ENDIF     
    .IF hBrush != 0
        Invoke DeleteObject, hBrush
    .ENDIF    
    ret
_SB_PaintBackground ENDP
```

So if `bEnabled` is `TRUE` and the **SimpleButton** control's selected state \(`bSelectedState`\) is `FALSE` we just have to determine if the mouse is over the control or not.

If the mouse is **not** over the control we fetch the default `@SimpleButtonBackColor` property which is a `DWORD` [COLORREF](https://msdn.microsoft.com/en-us/library/vs/alm/dd183449%28v=vs.85%29.aspx) value to paint the background with. If however the mouse _is_ over the control we fetch the `@SimpleButtonBackColorAlt` property which is another `DWORD` [COLORREF](https://msdn.microsoft.com/en-us/library/vs/alm/dd183449%28v=vs.85%29.aspx) value \(which may be the same value or a different value than the `@SimpleButtonBackColor` property\). Similarly we can fetch colors for when our control is disabled or if is selected state has been set with our `SimpleButtonSetState` function, or if the state of our control has changed automatically as a result of specifying the `SBBS_AUTOSTATE` flag when created the control \(which allows us to toggle the state of our **SimpleButton** control when it is clicked each time\)

Then we proceed to paint the background of our **SimpleButton** control with the color retrieved, using the standard [FillRect](https://msdn.microsoft.com/en-us/library/windows/desktop/dd162719%28v=vs.85%29.aspx) api call.

We then clean up the brushes used, and return to `_SB_Paint` ready to call the next sub-function to handle painting our text and border. Each of those sub-functions works in a similar manner, by fetching the appropriate external property value and then painting based on those values for text or border colors, depending on the status of the control: selected, enabled or if the mouse is over the control.

