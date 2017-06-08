# The ModernUI Framework

Readers might be interested in my other project which is related to creating custom controls in assembler: **ModernUI**

It uses many of the techniques that we have covered in creating controls and implementing property constants to allow the end-user to easily customize the controls look and/or behaviour. Of particular interest to readers that followed the creating of our **SimpleButton** control is the **ModernUI** control `ModernUI_Button` which expands on the **SimpleButton** control  and adds more features and capabilities, like allowing bmp, ico or png images as well as text

There is two versions of the project, one for x86 **32bit **windows, and one for x64 **64bit **windows:

* [ModernUI](https://github.com/mrfearless/ModernUI)
* [ModernUI64](https://github.com/mrfearless/ModernUI64)

The **ModernUI** project, which is still a work in progress, includes a framework library that contains many of the helper functions already covered here, bundled into one convienent library \(`ModernUI.inc` & `ModernUI.lib`\).

Each control utilizes the main **ModernUI **library to handle the low level internals required to create the controls and some other functions to help ease development and use of them.

The current list of controls that are available are:

* `ModernUI_CaptionBar`
* `ModernUI_Button`
* `ModernUI_ProgressBar`
* `ModernUI_Checkbox`
* `ModernUI_SmartPanel`

In addition to the released controls, I have included some RadASM project as examples of usage for each control. Also there is RadASM Auto-complete .api text files, and RadASM custom control .dll files that help the end-user implement the current list of controls, within RadASM and allow the end-user to specify some of the initial style flags. Some icons for use with GUI are also included in the releases section along with the other files listed above.

For those that are interested in contributing to the project, the main design document of the project is on the wiki, here is the introduction of the design document:

> For years, new control types and UI designs for operating system and web have progressed, whilst older win32 base controls which are used by win32 assemblers \(masm32 etc\) have remained untouched. The ModernUI project has been created to help modernize these existing controls and add to them. The main goals of the project are:
>
> * Provide a modern update to existing win32 base class visual controls.
> * Add new control types to reflect newer UI/UX design influences.
> * Emulate control types from UWP, WPF, .Net & Web 2.0 platforms.



