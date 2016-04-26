### Getting Started

OpenGL is one giant state machine. Rendering with OpenGL involves the following steps, in order.

1. Create an OS-dependent window
2. Bind OpenGL "[context]" to window
3. Store vertex data in one or more "[buffer objects][buffer]"
4. Write, compile and link two or more "[shaders][Shader]"
5. Activate buffers and shaders
6. Draw currently active buffers and shaders

[Context]: context.md
[Buffer]: buffer.md
[Shader]: shader.md