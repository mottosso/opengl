### `glGenBuffers()`

Create one or more unique ids for potential buffers.

```c
// Generate 1 buffer id
GLuint vertexBuffer;
glGenBuffers(1, &vertexBuffer)
printf("id: ", vertexBuffer)
// id: 0
```