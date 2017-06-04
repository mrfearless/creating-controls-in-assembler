# Introduction

##### Creating Controls In Assembler

There are a number of tutorials out on the internet that describe how to create a custom control, but not many that cover creating a control in assembler. Mostly these tutorials are written for C/C++ users, which acts as a basic starting point for assembly developers to port over the code to assembly language.

For many years I was content to create programs and little utilities in assembler using masm. with what I knew of win32 api from forum discussions, and reading lots of articles and tutorials covering lots of different topics. Mostly I stayed away from the painting stuff \(GDI api functions and handling the WM\_PAINT message\) as I felt it was over my head at that time.

After some time though, I gradually started to implement small bits of GDI code and functions in my WM\_PAINT message - just enough to begin to understand what was going on under the hood.

Then one day I was looking at the steam client interface I wondered how they went about creating their menus, what was required, and how it was implemented, and how could the same effect could be achieved in assembler.

I had an epiphany in realizing windows controls are just little blank 'windows' or canvases or panels \(or whatever you want to call them\) that have been painted to look like something. Sure the paint job in some controls was more advanced - with 3d effects and lines and shadows and all other kinds of fancy bits - but ultimately I realized that it was all smoke and mirrors, that 'controls' where just painted blobs that responded in specific but programmed ways to achieve their interactivity.

That led me to wonder about user interface controls in general, the ones found in windows and other sources like web 2.0 websites and programs that used .net, and i wondered how I could create a control for myself using the standard win32 API and assembly language.

The following information describes the path I took in creating custom controls in assembler using the Win32 API and masm assembler. Hopefully you will find it both useful and informative.

##### 



