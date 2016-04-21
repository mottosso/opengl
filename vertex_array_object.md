### Vertex Array Object

> Deprecated

The VAO is an [OpenGL Object] for *client-side* caching of drawing commands. For modern OpenGL, it has been superceded by the [Vertex Buffer Object], which conversely performs *server-side* caching for greater performance.

[OpenGL Object]: opengl_object.md
[Vertex Buffer Object]: vertex_buffer_object.md

**Reference**

- [opengl.org/wiki](https://www.opengl.org/wiki/Vertex_Specification#Vertex_Array_Object)

<br>
<br>
<br>

### Examples

```cpp
#include <cstdlib>
#include <stdio.h>

#include "GL/gl3w.h"
#include "GLFW/glfw3.h"


int main(void)
{
    if (!glfwInit())
    {
        fprintf(stderr, "Failed to initialise GLFW\n");
        getchar(); // Pause terminal
        exit(EXIT_FAILURE);
    }

    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    static GLFWwindow* window {
        glfwCreateWindow(640, 480, "Tutorial 02", NULL, NULL) 
    };

    if (!window)
    {
        fprintf(stderr, "Failed to open window\n");
        getchar();
        glfwTerminate();
        exit(EXIT_FAILURE);
    }

    glfwMakeContextCurrent(window);
    gl3wInit();

    glfwSetInputMode(window, GLFW_STICKY_KEYS, GL_TRUE);

    GLuint VertexArrayID;
    glGenVertexArrays(1, &VertexArrayID);
    glBindVertexArray(VertexArrayID);

    static const GLchar* vertex_shader_source[] {
    "    #version 330 core                                          \n"
    "    layout(location = 0) in vec3 vertexPosition_modelspace;    \n"
    "                                                               \n"
    "    void main()                                                \n"
    "    {                                                          \n"
    "        gl_Position.xyz = vertexPosition_modelspace;           \n"
    "        gl_Position.w = 1.0;                                   \n"
    "    }                                                          \n"
    };

    static const GLchar* fragment_shader_source[] {
    "    #version 330 core                                          \n"
    "    out vec4 color;                                            \n"
    "                                                               \n"
    "    void main()                                                \n"
    "    {                                                          \n"
    "        color = vec4(1.0, 1.0, 1.0, 1.0);                      \n"
    "    }                                                          \n"
    };

    GLuint vertex_shader { glCreateShader(GL_VERTEX_SHADER) };
    glShaderSource(vertex_shader, 1, vertex_shader_source, NULL);
    glCompileShader(vertex_shader);

    GLuint fragment_shader { glCreateShader(GL_FRAGMENT_SHADER) };
    glShaderSource(fragment_shader, 1, fragment_shader_source, NULL);
    glCompileShader(fragment_shader);

    GLuint program { glCreateProgram() };
    glAttachShader(program, vertex_shader);
    glAttachShader(program, fragment_shader);
    glLinkProgram(program);

    glDeleteShader(vertex_shader);
    glDeleteShader(fragment_shader);

    static const GLfloat g_vertex_buffer_data[] {
        -1.0f, -1.0f, 0.0f,
         1.0f, -1.0f, 0.0f,
         0.0f,  1.0f, 0.0f
    };

    GLuint vao;
    glGenBuffers(1, &vao);
    glBindBuffer(GL_ARRAY_BUFFER, vao);
    glBufferData(GL_ARRAY_BUFFER,
                 sizeof(g_vertex_buffer_data),
                 g_vertex_buffer_data,
                 GL_STATIC_DRAW);

    glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
    while (glfwGetKey(window, GLFW_KEY_ESCAPE) != GLFW_PRESS &&
           !glfwWindowShouldClose(window))
    {
        glClear(GL_COLOR_BUFFER_BIT);
        glUseProgram(program);

        glEnableVertexAttribArray(0);
        glBindBuffer(GL_ARRAY_BUFFER, vao);
        glVertexAttribPointer(
            0,                  // Position in shader
            3,                  // Size
            GL_FLOAT,           // Type
            GL_FALSE,           // Normalized?
            0,                  // Stride
            (void*)0            // Array buffer offset
        );

        glDrawArrays(GL_TRIANGLES, 0, 3); // From index 0 to 3

        glfwPollEvents();

        glDisableVertexAttribArray(0);

        glfwSwapBuffers(window);
    }

    glfwTerminate();

    // Cleanup VBO
    glDeleteBuffers(1, &vao);
    glDeleteVertexArrays(1, &VertexArrayID);

    exit(EXIT_SUCCESS);
}

```