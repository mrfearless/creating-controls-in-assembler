# RadASM Auto-complete

Another reason I used the approach of defining properties as constants, is RadASM's auto-complete / intelliSense** **feature. By editing two .api files and adding some information I can include this information to make it easier to code using the **SimpleButton** control and other controls.

So I easily can add autocomplete for the `SimpleButtonGetProperty` function and it will show a dropdown list of possible values when I am ready to supply the 2nd parameter \(`dwProperty`\)

We can edit the contents of RadASM\Masm\masmApiCall.api and place the following text:

```
SimpleButtonGetProperty,hSimpleButton,dwProperty
```

And the same with RadASM\Masm\masmApiConst.api, we edit and place the following text:

```
2SimpleButtonGetProperty,@SimpleButtonTextFont,@SimpleButtonTextColor,@SimpleButtonTextColorAlt,
@SimpleButtonTextColorSel,@SimpleButtonTextColorSelAlt,@SimpleButtonTextColorDisabled,@SimpleButtonBackColor,
@SimpleButtonBackColorAlt,@SimpleButtonBackColorSel,@SimpleButtonBackColorSelAlt,@SimpleButtonBackColorDisabled,
@SimpleButtonBorderColor,@SimpleButtonBorderColorAlt,@SimpleButtonBorderColorSel,@SimpleButtonBorderColorSelAlt,
@SimpleButtonBorderColorDisabled,@SimpleButtonBorderStyle
```

Save the files and restart RadASM for the change to take effect. This is what RadASM's auto-complete looks like when has been implemented for our `SimpleButtonGetProperty` function:

![](/assets/SimpleButtonGetProperty.gif)

The project source for **SimpleButton** also includes the `masmApiCall.api.txt`, `masmApiConst.api.txt`and `masmMessage.api.txt` text files that incorporate these calls and constants for use with RadASM's auto-complete / intelliSense feature, you just need to open the appropriate .api file and paste the contents at the end of the file and restart RadASM for them to be available to you. Instructions are included in the text files themselves as to which file it related to and the typical location of those .api files.

