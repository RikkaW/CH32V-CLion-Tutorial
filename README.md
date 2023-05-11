# CH32V-CLion-Tutorial

作为一个被喷气脑子惯坏了的小朋友，给 CH32 RISC-V 芯片开发程序只能用 Eclipse 改的 MounRiver Studio 是非常难受的。

于是就有了文章教你如何使用 CLion 搭建 CH32 RISC-V 的开发环境。

### 1. 准备工作

- 购买和安装 CLion
- 下载和安装 [MounRiver Studio](http://www.mounriver.com/download)，对于 Linux 和 macOS 似乎需要单独下载工具链

> 以下将以 Windows 为例来操作，其他操作系统类似。

### 2. 设置环境变量

在环境变量中添加 MounRiver Studio 所附带的 RISC-V Embedded GCC（`MounRiver_Studio/toolchain/MounRiver_Studio/toolchain`）。

![设置环境变量](./images/environment-variable.png)

### 3. CLion 设置

- 设置 OpenOCD 为 `MounRiver_Studio/toolchain/OpenOCD/bin/openocd.exe`

![设置 OpenOCD](./images/setup-openocd.png)

### 4. 编译及烧录

1. 使用 MounRiver Studio 建立工程，无需进行编译，**这步之后无需再使用 MounRiver Studio**
2. 复制本项目中的 `CMakeLists.txt` 到建立的工程中
3. 如果你的芯片不是 CH32V307，需修改其中的 `"Startup/startup_ch32v30x_D8C.S"` 到你使用的芯片对应的文件
4. 使用 CLion 打开项目
5. 在 Run/Debug configuration 中新增一个 OpenOCD Download/Run configuratiuon（如果它自动建立了就修改），Board config file 选择 `MounRiver_Studio/toolchain/OpenOCD/bin/wch-riscv.cfg`
   
   ![OpenOCD Download/Run configuratiuon](./images/openocd-configuration.png)

6. 如无问题，现在就应该可以进行编译、烧录及调试了（锤子图标是编译，箭头图标是烧录，虫子图标是烧录及开启 GDB 调试）
   
   ![build-run-debug](./images/build-run-debug.png)

7. 其他操作请参考 CLion 及 CMake 的文档，例如添加新的文件需对 `CMakeLists.txt` 进行修改，修改后需 "Reload CMake project"