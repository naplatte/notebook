### vcpkg

#### tips

- 微软推出的C++包管理器
- 类似于Python的pip，JavaScript的npm
- 一条命令安装并配置好C++第三方库，无需自己下载zip包、配置inclue、lib路径、拷贝dll

#### 如何在CMake中使用

- 临时方式：推荐调试用

```cmake
cmake -DCMAKE_TOOLCHAIN_FILE=D:/vcpkg/scripts/buildsystems/vcpkg.cmake ..
```

- clion方式：文件 - 设置 - CMake - CMake options

```tex
-DCMAKE_TOOLCHAIN_FILE=D:/vcpkg/scripts/buildsystems/vcpkg.cmake
```

保存后在CMake中这样使用

```cmake
find_package(SDL2 CONFIG REQUIRED)
target_link_libraries(my_imgui_app PRIVATE SDL2::SDL2 SDL2::SDL2main)
```

#### 常用命令

- 需要先配置环境变量（系统变量path），加入`D:\vcpkg`

![image-20250626165502736](https://raw.githubusercontent.com/sleepyDev0x/Pictures/main/22bdbe3de9891668bce357e0001903c1.png)	

