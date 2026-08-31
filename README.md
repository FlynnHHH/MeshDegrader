# MeshDegrader

MeshDegrader 是一个基于 Easy3D 开发的交互式网格退化工具，面向网格结构破坏、网格补全算法评估和相关研究。它继承了 Easy3D 的 3D 模型加载、可视化和几何处理能力，并重点提供可控的网格局部删除功能。

> A tool derived from Easy3D for deliberately degrading mesh structures, enabling further evaluation and investigation of existing mesh completion algorithms.

## 本版本新增功能

### 交互式网格局部退化

在 MeshDegrader 中打开曲面网格后，可以通过 `Select` 菜单执行以下操作：

- `Delete selected primitives`：删除当前选中的网格面；对点云则删除选中的点。
- `Delete within Sphere`：以当前选中网格面的重心为中心，输入半径，删除半径范围内包含相关顶点的网格面。
- `Delete the N-neighbor of the vertex`：以当前选中的网格面为起点，输入邻域层数 `N`，通过面之间的顶点连接关系删除 N 阶拓扑邻域内的网格面。

操作流程：

1. 启动 Mapple 并打开曲面网格，例如 `resources/data/bunny.obj`。
2. 在 `Select` 菜单中选择 `Select Click`、`Select Rect` 或 `Select Lasso`。
3. 选中一个网格面。
4. 执行 `Delete within Sphere` 或 `Delete the N-neighbor of the vertex`，在弹窗中输入半径或邻域层数。

局部删除完成后，程序会清理无效元素并刷新模型显示。该操作会直接修改当前模型，建议先保存原始模型副本。对于点云，局部删除菜单当前按照选中点删除；N 邻域操作仅对曲面网格生效。

### 新增示例模型

仓库新增了 Stanford Bunny OBJ 示例模型：

```text
resources/data/bunny.obj
```

它可以用于测试模型加载、面选择和局部区域删除功能。

## 主要能力

- 点云、曲面网格、多面体网格和图等 3D 数据结构。
- 灵活的顶点、边、面和模型属性系统。
- 点云法向估计、Poisson 重建、RANSAC、网格简化、细分、平滑、参数化、重网格化、孔洞填充等几何处理算法。
- 点、线、三角形、标量场和向量场的实时渲染。
- 点精灵、曲线精灵、SSAO、硬阴影、软阴影、透明度和点云眼睛光照等渲染技术。
- 基于现代 OpenGL/GLSL 的高层渲染接口。
- GLFW、Qt 等窗口与交互支持。
- C++ API、Python 绑定、示例程序和测试程序。
- Mapple 3D 数据查看与处理工具，以及面向曲面网格和点云的交互式选择、编辑功能。

## 目录结构

| 目录 | 内容 |
| --- | --- |
| `easy3d` | Easy3D C++ 库源码 |
| `applications` | Mapple、FigureMaker 等应用程序 |
| `tutorials` | C++ 教程 |
| `python` | Python 绑定、教程和测试 |
| `resources` | 示例模型、纹理、字体和着色器 |
| `tests` | 自动化测试和交互相关测试 |
| `3rd_party` | 项目使用的第三方库 |
| `cmake` | CMake 模块和查找脚本 |
| `docs` | Doxygen 文档配置 |

## 构建要求

- CMake 3.12 或更高版本
- 支持 C++11 或更高版本的编译器
- Windows：建议使用 64 位 MSVC
- macOS：Xcode 8 或更高版本
- Linux：GCC 4.8 或更高版本，或 Clang 3.3 或更高版本

核心库和默认查看器所需的大部分依赖已包含在 `3rd_party` 中。以下依赖按需启用：

- Qt 5.6+ 或 Qt 6：构建 Mapple 和 Qt 示例，需要打开 `Easy3D_ENABLE_QT`。
- CGAL 5.1+：启用高级曲面网格处理，需要打开 `Easy3D_ENABLE_CGAL`。
- FFmpeg 3.4+：启用视频编码，需要打开 `Easy3D_ENABLE_FFMPEG`。

## 构建

### Linux / macOS

```bash
mkdir Release
cd Release
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build . --config Release -j
```

### Windows

使用 Visual Studio 的 x64 开发者命令提示符，或直接用 Visual Studio、Qt Creator、CLion 打开根目录的 `CMakeLists.txt`：

```powershell
mkdir Release
cd Release
cmake -S .. -B . -A x64
cmake --build . --config Release
```

如果需要构建 Mapple，请在配置时启用 Qt：

```bash
cmake -S . -B Release -DEasy3D_ENABLE_QT=ON
cmake --build Release --config Release --target Mapple
```

Qt 未安装或未被 CMake 找到时，Mapple 不会参与构建。此时可通过 `Qt5_DIR` 或 `Qt6_DIR` 指定 Qt 的 CMake 配置目录。

## 使用 MeshDegrader

构建完成后运行生成的 `Mapple` 可执行文件。当前应用程序目标仍命名为 `Mapple`，但其网格退化功能以 MeshDegrader 项目定位进行维护。打开模型后，推荐使用以下方式测试新增功能：

1. 加载 `resources/data/bunny.obj`。
2. 选择 `Select > Select Click`。
3. 点击模型上的一个面。
4. 选择 `Select > Delete within Sphere`，输入半径并确认；或选择 `Select > Delete the N-neighbor of the vertex`，输入邻域层数并确认。

其中，邻域层数为 `0` 时只删除起始面；层数越大，删除的拓扑邻域越大。删除后可使用 Mapple 的保存功能导出编辑后的模型。

## Python 绑定

Python 绑定的构建、安装和示例请参阅 [`python/README.md`](python/README.md)。最小示例：

```python
import easy3d

easy3d.initialize()
viewer = easy3d.Viewer("Easy3D Viewer")
viewer.add_model("resources/data/bunny.obj")
viewer.run()
```

曲面网格教程位于 [`python/tutorials`](python/tutorials)，其中 [`tutorial_103_surface_mesh.py`](python/tutorials/tutorial_103_surface_mesh.py) 会加载并显示网格，同时显示边和顶点。

## 测试

启用测试后重新配置项目：

```bash
cmake -S . -B Release -DEasy3D_BUILD_TESTS=ON
cmake --build Release --config Release --target tests
```

测试程序通常位于构建目录的 `bin` 目录中。Python 测试和教程位于 `python/test` 与 `python/tutorials`。

## 在其他 CMake 项目中使用

构建或安装 Easy3D 后，可以通过 `Easy3D_DIR` 查找库：

```cmake
cmake_minimum_required(VERSION 3.12)
project(MyProject)

set(CMAKE_CXX_STANDARD 11)
find_package(Easy3D COMPONENTS viewer REQUIRED)

add_executable(MyProject main.cpp)
target_link_libraries(MyProject easy3d::viewer)
```

```cpp
#include <easy3d/util/initializer.h>
#include <easy3d/viewer/viewer.h>

int main(int argc, char** argv) {
    easy3d::initialize();
    easy3d::Viewer viewer("MyProject");
    return viewer.run();
}
```

## 文档与贡献

- [Easy3D C++ 文档](https://3d.bk.tudelft.nl/liangliang/software/easy3d_doc/html/index.html)
- [Easy3D Python 文档](https://3d.bk.tudelft.nl/liangliang/software/easy3d_doc/python/index.html)
- [Python 教程文档](https://3d.bk.tudelft.nl/liangliang/software/easy3d_doc/python/auto_tutorials/index.html)
- [构建指南](HowToBuild.md)
- [贡献指南](CONTRIBUTING.md)

## 许可证

Easy3D 使用 GNU General Public License（GPL）授权，具体条款请阅读 [`LICENSE`](LICENSE)。项目中的第三方组件可能使用各自的许可证，请以对应目录中的许可证文件为准。

## 致谢

Easy3D 的实现受 PMP、libQGLViewer、Surface Mesh 和 Graphite 等开源项目启发，并使用或改编了其中部分算法。感谢所有项目作者、贡献者和测试用户对 3D 几何处理与可视化社区的贡献。
