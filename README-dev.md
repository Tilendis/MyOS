# MyOS 开发进度

>最后更新：2026-01-03

## 已完成

- [x] Boches 软盘启动配置 (使用 `floppy0: type=floppy`)
- [x] 引导程序修复*(`dl = 0x00`)
- [x] 本地Git初始化 + 首次提交
- [x] 推送到Github
- [x] 显示系统名称、版本、研发人员及单位等版权信息
- [x] 提供命令提示符显示
- [x] 系统时间/日期实时性
- [x] 日期/时间修改功能
- [x] 写入软盘镜像文件中，并在虚拟机平台上启动加载运行和测试验证。


## 下一步

- [ ] 通过答辩验收

## 待解决问题

- [ ] 梳理理解过程中出现的问题（Ai使用/操作系统开发）

## 关键命令备忘

```bash
# 编译
# 1. 编译引导扇区
nasm -f bin boot.asm -o boot.bin
# 2. 编译内核
nasm -f bin kernel.asm -o kernel.bin
# 3. 合并镜像
cat boot.bin kernel.bin > os.img

# 运行
bochs -q -f bochsrc.txt #我选择手动双击bochs.exe

# Git 提交
git add .
git commit -m "update"
