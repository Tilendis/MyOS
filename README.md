# MyOS - 16位实模式极简操作系统

A minimal 16-bit real-mode operating system for x86 architecture, implemented entirely in NASM assembly. Focus on BIOS interrupt usage, disk loading, time/date management, and command-line interaction.

## 🌟 核心功能 | Core Features

- 🖥️ 自定义引导加载程序（Boot Loader）：加载内核到指定内存地址，支持磁盘错误处理与重启
- ⌚ 实时时间/日期管理：显示系统时间/日期，支持手动设置（BCD码与十进制互转）
- ⌨️ 命令行交互：支持清屏、系统信息、帮助、重启等8+核心命令
- 🧩 纯汇编实现：无第三方依赖，基于BIOS中断完成所有硬件交互
- 🧪 Bochs全兼容：提供完整的配置文件，一键运行

## 🚀 快速开始 | Quick Start

### 环境准备 | Environment

- 编译器：NASM（推荐版本 ≥ 2.15）
- 模拟器：Bochs（推荐版本 2.6.11，兼容性最佳）
- 系统：Windows/Linux/macOS（以下以Windows为例）

### 编译与运行 | Compile & Run

#### 1. 编译汇编代码

```bash
# 编译引导程序（512字节，引导扇区）
nasm -f bin boot.asm -o boot.bin

# 编译内核（4096字节，8个扇区）
nasm -f bin kernel.asm -o kernel.bin
```

#### 2.制作软盘镜像

```bash
# Windows CMD
copy /b boot.bin+kernel.bin os.img

# Linux/macOS
cat boot.bin kernel.bin > os.img
```

#### 3.运行MyOS

```bash
bochs -f bochsrc.txt
```

📋 支持命令 | Supported Commands

- 命令 功能 Command Function

- t 显示当前时间 t Display current time
- d 显示当前日期 d Display current date
- s 设置系统时间 s Set system time
- a 设置系统日期 a Set system date
- c 清屏 c Clear screen
- i 显示系统信息 i Show system info
- h 查看帮助 h Show help
- r/q 重启系统 r/q Reboot system

🛠️ 技术架构 | Technical Architecture

1. 引导程序（boot.asm）
内存布局：加载到 0x7c00 地址，栈指针设为 0x7c00
核心逻辑：
初始化段寄存器（DS/ES/SS=0）
调用 BIOS 0x13 中断读取内核到 0x8000 地址
错误处理：磁盘读取失败时提示并重启
跳转执行：加载成功后跳转到 0x8000 执行内核
2. 内核（kernel.asm）
内存布局：加载到 0x8000 地址，栈指针设为 0x7000
核心模块：
输入输出：基于 BIOS 0x10 中断实现字符串 / 字符输出
时间 / 日期：BIOS 0x1A 中断读取 / 设置 CMOS 时钟，BCD↔ASCII 转换
命令解析：循环读取按键，匹配命令并执行对应逻辑
数值处理：实现十进制→BCD 转换，输入合法性校验（范围 / 格式）

3. Bochs 配置（bochsrc.txt）

```txt
floppya: 1_44=os.img, status=inserted
boot: a
display_library: win32
log: bochs.log
megs: 1
cpu: ips=1000000
```

📖 开发历程 | Development Notes
核心问题与解决方案 | Key Issues & Solutions
内核加载失败：修正段地址对齐（org 0x8000）与 BIOS 0x13 中断参数（扇区数 / 地址）
时间设置越界报错：重构 read_num 函数，避免寄存器冲突，增加数值范围校验
年份显示错误（10 开头）：新增 century 变量（0x20=20），分离世纪与年份存储
Bochs 镜像大小警告：使用 bximage 创建标准 1.44MB 镜像，dd 精准写入扇区

📁 项目结构 | Project Structure

```plainttxt
MyOS/
├── boot.asm        # 引导加载程序（512字节）
├── kernel.asm      # 内核程序（4096字节）
├── bochsrc.txt     # Bochs配置文件
├── README.md       # 项目说明文档
└── docs/           # 可选：开发笔记/截图（建议新增）
    ├── demo.png    # 运行截图
    └── dev_notes.md# 开发细节/难点解析
```
