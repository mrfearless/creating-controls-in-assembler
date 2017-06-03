# Guidelines {#guidelines}

The design and coding process, I used to create controls, went through lots of evolutions. Different techniques and/or better code implementations were used once I discovered them or figured out via trial and error the best way to accomplish some tasks. I hope to show some of these techniques here.

Obviously this is my way of creating controls, and there are bound to be many ways to achieve the same results and alternate techniques to use that are all valid, like [subclassing](https://msdn.microsoft.com/en-us/library/windows/desktop/bb773183%28v=vs.85%29.aspx) existing win32 controls for example, or utilizing [custom draw](https://msdn.microsoft.com/en-us/library/windows/desktop/ff919569%28v=vs.85%29.aspx) or [ownerdrawn](https://msdn.microsoft.com/en-us/library/windows/desktop/dd373487%28v=vs.85%29.aspx) features. 

So consider all this a guideline, code in your own style and take what you want from it and adapt any code and techniques in your own fashion to your own projects.

I use RadASM as my editor of choice and I create templates for my own use that helps speed up development. Feel free to use whatever environment suits for you.

I use the [Masm32 SDK](http://masm32.com/download.htm) for developing x86 assembler and [JWasm/HJWasm/UASM](http://www.terraspace.co.uk/uasm.html) for x64 assembler. The assembler code samples shown in the examples that follow all use masm assembler syntax.

Note: Error checking code is omitted for brevity. In some places I use the word 'variable' or 'variables', 'property' or 'properties'. The terms should be considered as interchangeable, meaning the same thing. Similarly function names, structures and parameters referring to or including the word 'property' or 'properties' means the same as 'variables' in that context as well.

