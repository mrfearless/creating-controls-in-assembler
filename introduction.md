# Introduction

### Creating Controls In Assembler

There are a number of tutorials out on the internet that describe how to create a custom control, but not many that cover creating a control in assembler. Mostly these tutorials are written for C/C++ users, which acts as a basic starting point for assembly developers to port over the code to assembly language.

For many years I was content to create programs and little utilities in assembler using masm. with what I knew of win32 api from forum discussions, and reading lots of articles and tutorials covering lots of different topics. Mostly I stayed away from the painting stuff \(GDI api functions and handling the WM\_PAINT message\) as I felt it was over my head at that time.

After some time though, I gradually started to implement small bits of GDI code and functions in my WM\_PAINT message - just enough to begin to understand what was going on under the hood.

Then one day I was looking at the steam client interface I wondered how they went about creating their menus, what was required, and how it was implemented, and how could the same effect could be achieved in assembler.

I had an epiphany in realizing windows controls are just little blank 'windows' or canvases or panels \(or whatever you want to call them\) that have been painted to look like something. Sure the paint job in some controls was more advanced - with 3d effects and lines and shadows and all other kinds of fancy bits - but ultimately I realized that it was all smoke and mirrors, that 'controls' where just painted blobs that responded in specific but programmed ways to achieve their interactivity.

That led me to wonder about user interface controls in general, the ones found in windows and other sources like web 2.0 websites and programs that used .net, and i wondered how I could create a control for myself using the standard win32 API and assembly language.

The following information describes the path I took in creating custom controls in assembler using the Win32 API and masm assembler. Hopefully you will find it both useful and informative.

### Windows Controls And Modern UI Design

The windows controls - the basic and custom controls - are available for users to create in assembler via calls to the win32 api. The image below shows examples of some of the most commonly used ones \(a complete listing of controls can be found on the [msdn microsoft website](https://msdn.microsoft.com/en-us/library/windows/desktop/bb773169%28v=vs.85%29.aspx)\):

![](/assets/win32controls.png)

Over the years newer UI technologies and frameworks have appeared and improved on the standard controls available in windows, and in some cases have given the developers new controls, or ways to create newer UI controls with more flexibility, more customization and better ways to present information to their end-users. [GDI+](https://msdn.microsoft.com/en-us/library/ms533798%28v=vs.85%29.aspx), [Windows Forms](https://msdn.microsoft.com/en-us/library/dd30h2yb%28v=vs.110%29.aspx), [DirectX/Direct3D](https://msdn.microsoft.com/en-us/library/windows/desktop/bb153256%28v=vs.85%29.aspx), [OpenGL](https://www.opengl.org/), [Vulcan](https://www.khronos.org/vulkan/), [Windows Presentation Foundation](https://msdn.microsoft.com/en-us/library/aa663364.aspx), Microsoft Design Language & [Microsoft Fluent Design System](https://developer.microsoft.com/en-us/windows/apps/design) show us that the evolution of modern UI/UX has changed, taking inspiration from many sources including web 2.0 UI style designs.

Other companies also have followed suit with their own frameworks and design specifications: [Google's Material Design](https://material.io/guidelines/) for Android and [Apple's Design](https://developer.apple.com/design/) for iOS.

Unfortunately for assembly language developers, enthusiasts or hobbyists, we have been left behind to some extant - even though it is still possible in some cases, with some work and knowledge, to interface with some of the above mentioned technologies or frameworks.

But that does not mean all is lost. We can adapt and take inspiration from all those new UI/UX design styles and create something similar for ourselves in assembly language - with a little work.

