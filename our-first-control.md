# Our First Control

I'm going to use a simple button control example to help cover some of the code used in this article. The name of this control will be **SimpleButton**. I wont be covering every single function used, merely the most important or interesting ones. The code for the full project including examples is available for you to browse, read and download here:

[https://github.com/mrfearless/SimpleButton](https://github.com/mrfearless/SimpleButton)

Here is what our **SimpleButton** control will look like when its in operation:

![](/assets/SimpleButtonDemo.gif)

Very simple, but for our purposes should be fine to help cover the steps required to create the controls behaviour. We will allow the end-user to specify the background, text and border colors for seperate states such as: selected, enabled and mouse over. We will paint the background, the text and border based on those current states and handle mouse interaction, cursors and emulate a push button effect and/or a toggle state. All these options will be handled by using flags and properties that we define for use with our **SimpleButton** control.

Firstly I create two files: `SimpleButton.asm` that will contain the main code for the control and an include file called `SimpleButton.inc`. The `SimpleButton.inc` file be included from within `SimpleButton.asm` and will also be distributed along with the compiled static library of the control once we are finished `SimpleButton.lib`.

`SimpleButton.inc` will include function prototypes for users to call that will create and manipulate the **SimpleButton** control. Also included will be any structures or constants that will be required for the user to use in conjunction with the functions we create, and we will also include some custom messages for our control as well.

##### 

##### 



