## imgui

#### 1.一些概念

- imgui：Immediate Mode Graphical User Interface（即时模式图形用户界面）

- 与传统GUI（Qt,MFC）区别

  - **所有控件都在每一帧的代码中重新构建**，这个写完贪吃蛇很好理解了，每一帧都将结果重新绘制一遍
  - 没有**保留状态**结构，不需要为控件维护状态对象
  - 控件状态由用户代码控制

- 本质上是一个**绘制GUI的库**，它的代码只管绘制，不管

  - 窗口从哪创建
  - 鼠标、键盘事件从哪来
  - 图形如何渲染到屏幕上

- 所以需要

  - 平台：管理窗口、鼠标|键盘的输入，比如SDL2，Win32
  - 渲染器：把imgui绘制的数据渲染出来，比如OpenGL、DirectX

- 绘制vs渲染（拿一幅画举例）

  - 绘制(draw)：构建需要有什么数据在图上，比如按钮、文本框、窗口位置，类似于将画中有什么内容用笔写下来；
  - 渲染(render)：将构建好的内容，用图形API（OpenGL）画在屏幕上，类似于根据画中的内容，完整的将画完成；

#### 2.上手使用

- 首先就是配置CMake
  - 安装SDL2后，CMake需要去找
  - 头文件和链接库都需要安排上

```cmake
cmake_minimum_required(VERSION 3.15)

set(CMAKE_TOOLCHAIN_FILE "D:/vcpkg/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
project(cpp_demo)

set(CMAKE_CXX_STANDARD 17)

# 查找 SDL2
find_package(SDL2 CONFIG REQUIRED)
find_package(OpenGL REQUIRED)

# 包含头文件
include_directories(
        ${SDL2_INCLUDE_DIRS}
        ${OPENGL_INCLUDE_DIR}
        imgui
        backends
)

# 源文件列表
file(GLOB IMGUI_SRC
        imgui/*.cpp
        backends/*.cpp
)

add_executable(imgui_test main.cpp ${IMGUI_SRC})

# 链接库
target_link_libraries(imgui_test
        SDL2::SDL2
        SDL2::SDL2main
        OpenGL::GL
)
```

- 简单应用

```cpp
```

