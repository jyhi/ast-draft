# 安同开始程序 0.3 初步构想

以下是一些关于安同开始程序 0.3 （AST）的构想，比较零碎，留作备忘，日后整理。

## 目前可做的课题

1. GRUB2 grub-mkimage (grub-mkstandalone?) 制作可启动的 GRUB 镜像（g2ldr)
    - MBR (astg2ldr.mbr / astg2ldr)
    - GPT (astg2x86.efi / astg2x64.efi)
        - [ ] 32 位 EFI 是否存在？ 【讨论】
2. NT6 Bootmgr 引导到 GRUB2 EFI (astg2x??.efi)
3. GPT 的备份与恢复
4. GRUB2 映像文件 map 到 (loop) 的 grub.cfg 写法
5. 重启后再进入 Windows 系统后的回复系统 + 急救箱功能
    - 恢复 MBR / GPT
    - 重新部署
6. Windows 下 GUI 的设计（Windows Native）
7. 所有操作不占用 UI 线程（多线程编程）
8. 优雅地写入及读取为重启后 AST 的 Startup / Rescue Kit 准备的 ast_info.cfg
    - YAML? XML?
9. GET 到映像文件后要进行验证
    - 是否是个 Linux 发行版，是什么发行版
    - 读取以上信息需要 libisofs / libmiracle
10. libWinTF8 的集成

## AST 最初架构

### GUI 概念图

*这里本该有图。*

- frmSimpMain（标准模式）
- frmExpMain（专家模式）


### 零碎的记忆

- 标准模式下主窗体获取映像文件需要用户将映像文件拽入，所以要响应 onDrop 事件
- 多线程工作：UI，操作
    - _beginthreadex()
    - _endthreadex()
    - 参见 MSDN, How-to?
- 开始后，Start 按钮就会变成 Pause（暂停）。实现方法：
    - 跳转表（事实证明这是错误的决定）
        - 跳转表是否可以带参？
    - 一个个函数中间判断 isPausePressed
- getenv() -> GetSystemDrive()
- 物理磁盘表示法（用于 NT5 boot.ini 以及 NT6 bootmgr）
    - 此时使用盘符要加载 HAL
- int wmain (int argc, wchar **argv)
- [ ] md5sum() -> sha1sum() 【讨论】

### 草稿纸上的涂鸦

```c
int wmain (int argc, wchar **argv);

// AST 0.3 同时支持命令行和图形界面操作
int parse_cli_argument (&argc, &argv);
int show_gui ();

// Run 阶段
int run ();                  // 在 run 中进行操作的排序
                             // Nah, 本来我想着这里能用跳转表，结果发现各个函数的形参表都不是一样的，没法用。
                             // （参见以上“零碎的记忆”）

int check_image ();          // 在 show_gui 中会被不断调用（对拽入的文件的检验）
int gather_system_info ();   // 老样子
int backup ();               // 老样子
int copy_necessities ();     // 老样子
int generate_grub_cfg ();    // 注意 map (loop) 的写法（参见上面课题）
int edit_loader ();          // 重磅戏
                             //   - 这次不玩 MBR，GPT 的直接读写了，直接借助本地 Windows Loader 来引导 GRUB
int generate_ast_info ();    // 创建供 Startup 阶段使用的 ast_info.cfg（参见上面课题）
                             //   - 参见以前版本的安同开始程序，有包括 Live 位置等等在内的信息，
                             //     不需要 Startup 时再次探测

// Startup 阶段
int read_ast_info ();        // 读取 ast_info.cfg
int restore_loader ();       // 回复本地引导器（如果安装成功的话，此时应有 GRUB）
int remove_useless_files (); // 删除垃圾

// Rescue Kit（急救箱）
int redeploy ();             // 重新部署（再执行一次 run）
int restore_gpt ();          // 恢复 GPT
int restore_mbr ();          // 恢复 MBR
                             // 以上两个操作注意不要动分区表，很可能会搞爆整块硬盘

// Misc（总会用到的）
int sha1sum ();              // 使用 SHA1SUM 来校验映像文件（用户提供正确的校验码？）
int msgprint ();             // 打印执行信息，并且同时输出到不同的地方：
                             //   - GUI Memo（专家模式有一个）
                             //   - ast.log
                             //   - stdout（命令行模式）
```

以及……

```c
static uint8_t noiseLevel;   // 0: 绝对安静
                             // 1: 安静（错误还是要提示出来）
                             // 2: 正常
                             // 3: 吵闹（DEBUG）

enum MsgLevel {
    DEBUG,                   // 只在 3 级输出（见上）
    INFO,                    // 正常的提示性消息
    WARN_NBK,                // Non-BreaK: 警告但不暂停操作（一般是不太严重的警告）
    WARN_BRK,                // BReaK: 警告并暂停提示用户是否继续
    ERROR                    // 致命错误，中断操作并回滚（？）
} level;
```

未完待续

