# Inside Our Control

`_SB_WndProc` is a standard main window messaging procedure, that handles specific `WM_` messages for our **SimpleButton** control. We have complete control over what messages are handled but we also have responsibility to handle specific messages, as we are implementing the core code ourselves and have to handle painting, mouse & keyboard interaction, and other features.

We wont cover all the messages implemented in the `_SB_WndProc`, see the code for the full **SimpleButton** project on [github](https://github.com/mrfearless/SimpleButton) if you wish to see more detail. We will cover a few important messages that the control handles.

Note: windows messages that we don't specify or include wont be handled. This is different from subclassing controls, where you can safely call the [DefWindowProc](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633572%28v=vs.85%29.aspx) for windows to handle messages that you have no interest in, and override only the ones you are interested in. In our control we do not have that luxury of letting windows handle the default messaging, instead it is expected that our control will do all the work.

For example if you wish to handle keyboard interaction for your control you will need to provide for that, and possibly handle some of the following messages: `WM_CHAR`, `WM_DEADCHAR`, `WM_SYSCHAR`, `WM_SYSDEADCHAR`, `WM_UNICHAR`, `WM_HOTKEY`, `WM_SYSCOMMAND`, `WM_KILLFOCUS`, `WM_SETFOCUS`, `WM_ACTIVATE`, `WM_KEYDOWN`, `WM_KEYUP`, `WM_SYSKEYDOWN`, `WM_SYSKEYUP`. See the Microsoft documentation [About Keyboard Input](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646267%28v=vs.85%29.aspx) for more details.

Similary if you wish to handle mouse interaction for your control you will need to possibly handle some of the following message:  `WM_LBUTTONDBLCLK`, `WM_LBUTTONDOWN`, `WM_LBUTTONUP`,  `WM_MBUTTONDBLCLK`, `WM_MBUTTONDOWN`, `WM_MBUTTONUP`, `WM_RBUTTONDBLCLK`, `WM_RBUTTONDOWN`, `WM_RBUTTONUP`, `WM_XBUTTONDBLCLK`, `WM_XBUTTONDOWN`, `WM_XBUTTONUP`, `WM_MOUSEHOVER`, `WM_MOUSELEAVE`, `WM_MOUSEMOVE`, `WM_NCMOUSEMOVE`, `WM_NCLBUTTONDOWN`, `WM_NCHITTEST`, `WM_MOUSEWHEEL`, `WM_MOUSEACTIVATE`, `WM_CONTEXTMENU`. See the Microsoft documentation [About Mouse Input](https://msdn.microsoft.com/en-us/library/windows/desktop/ms645601%28v=vs.85%29.aspx) for more details.

The Microsoft documentation also has a list of [System Defined Messages](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644927%28v=vs.85%29.aspx#system_defined) that may prove useful to refer to.

##### 



