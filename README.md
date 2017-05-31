# Creating Controls in Assembler

There are a number of tutorials out on the internet that describe how to create a custom control, but not many that cover creating one in assembler. For many years I was content to create programs and little utilities with what I knew of win32 api from forum discussions, and reading lots of articles and tutorials covering lots of different topics. Mostly I stayed away from the GDI api functions and WM\_PAINT stuff as I felt it was over my head at that time.



After some time, I gradually started to implement small bits of GDI functions in my WM\_PAINT code - just enough to begin to understand what was going on. Then one day I was looking at the steam client interface I wondered how they went about creating their menus, what was required, and how it was implemented, and how could the same effect could be achieved in assembler.



I had a sort of epiphany in realizing windows controls are just little blank 'windows' or canvases or panels \(or whatever you want to call them\) that have been painted to look like something. Sure the paint job in some controls was more advanced, with 3d effects and lines and shadows and all other kinds of fancy bits, but ultimately I realized it was all smoke and mirrors, that 'controls' where just painted blobs that responded in specific but programmed ways to achieve their interactivity.



The following information describes the path I took in creating custom controls in assembler using the Win32 API and masm assembler. Hopefully you will find it both useful and informative.

