### CMake

#### 1.CMake基本内容

- **跨平台的构建系统生成器**，告诉编译器如何编译你的项目
  - 要编译哪些.cpp文件
  - 编译出来的是可执行文件还是库
  - 用哪个C++标准
  - 链接哪些第三方库（比如OpenCV，Boost）

- CMakeLists.txt最简单的结构

```cmake
cmake_minimum_required(VERSION 3.10)  # 声明需要的最低CMake版本

project(my_project_name)              # 项目名称（可随便起）

set(CMAKE_CXX_STANDARD 17)            # 设置 C++ 标准为 C++17
set(CMAKE_CXX_STANDARD_REQUIRED True) # 强制要求使用该标准

add_executable(my_program main.cpp)   # 添加可执行文件 target，来自 main.cpp
```

- CMake项目结构建议

```css
my_project/
├── CMakeLists.txt         ← 主配置文件
├── main.cpp
├── include/               ← 存放头文件
│   └── mylib.h
├── src/                   ← 存放源文件
│   └── mylib.cpp
```

- 链接第三方库

```cmake
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(my_program main.cpp)
target_link_libraries(my_program ${OpenCV_LIBS})
```

#### 2.如何构建一个项目

##### ①clion自动构建

- 直接使用clion自带的CMake，将需要编译的cpp文件写入到txt文件中
- 右键加载txt文件即可，期间生成的相关文件会放在`cmake-build-debug`文件夹中

![image-20250714102346260](https://raw.githubusercontent.com/sleepyDev0x/Pictures/main/a5125b6a98769cad08f44db0f8220cdf.png)	

##### ②手动构建

> 需要自己在本地下载CMake

- 手动构建需自己新建build文件夹存储构建过程中生成的文件（非必须但建议），这样构建生成的文件不会污染源代码目录
- 构建步骤

```bash
mkdir build  # 创建一个名为build的目录（不是必须的，但推荐）
cd build
cmake ..     # 使用上级目录中的CMakeLists.txt配置项目

#如果使用的是makefile
make
```

#### 3.make与CMake - 构建工具与构建系统生成器

- `make`是**构建工具**，执行编译任务
- `CMake` 是 **构建系统生成器**，可以根据你的项目结构自动生成 Makefile。

- 以上面的构建代码为例
  - `cmake ..` → 读取 CMakeLists.txt，**自动生成 Makefile**
  - `make` → 根据刚生成的 Makefile 进行真正的编译
- 与make同类的构建工具有很多种，例如Ninja，是更快、更现代的构建系统，构建工具同CMake的关系如下

```css
          [CMakeLists.txt]
                ↓
      [cmake -G <生成器名>]
                ↓
 ┌─────────────────────────────────────┐
 │          CMake 生成的构建文件         |
 └─────────────────────────────────────┘
     ↓           ↓            ↓
 [Makefile]   [build.ninja]  [*.sln/VCXPROJ]
     ↓           ↓            ↓
   make        ninja        MSBuild/VS

```



