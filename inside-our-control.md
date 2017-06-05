# Inside Our Control

`_SB_WndProc` is a standard main window messaging procedure, that handles specific `WM_` messages for our **SimpleButton** control. We have complete control over what messages are handled but we also have responsibility to handle specific messages, as we are implementing the core code ourselves and have to handle painting, mouse & keyboard interaction, and other features.

We wont cover all the messages implemented in the `_SB_WndProc`, see the code for the full project if you wish to see more detail. We will cover a few important messages that the control handles.

##### Handling The First Message: WM\_CREATE vs WM\_NCCREATE



