# 索尼 ZV-E10 II 日文菜单操作记录（macOS）

记录日期：2026-10-24

## 目标

通过 [Sony-PMCA-RE](https://github.com/ma1co/Sony-PMCA-RE) 的服务模式开启相机语言菜单，再在相机上选择“日本語”。此方法修改语言设置，并非刷入日文版固件。

## 1. 连接相机

1. 相机进入 **MENU → 设置 → USB → USB连接模式 → 海量存储器（MSC）**。
2. 如果设为“连接时选择”，插入 USB 线后在相机上选择“影像传输（MSC）”。
3. 使用可传数据的 USB 线连接 Mac。

菜单路径见[索尼帮助指南](https://helpguide.sony.net/ilc/2430/v1/zh-cn/contents/0601_usb_connection.html)。

## 2. 准备 Mac 环境

已完成的步骤无需重复。

```bash
git clone https://github.com/ma1co/Sony-PMCA-RE.git
cd Sony-PMCA-RE
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -r requirements.txt
```

如果出现 `usb.core.NoBackendError: No backend available`，安装 PyUSB 所需的 libusb：

```bash
brew install libusb
python -c "import usb.backend.libusb1; print(usb.backend.libusb1.get_backend())"
```

输出 `<usb.backend.libusb1._LibUSB ...>` 表示已找到后端；如果仍输出 `None`，需要检查 libusb 安装路径。参见 [PyUSB 文档](https://github.com/pyusb/pyusb)。

## 3. 进入服务模式

在 `Sony-PMCA-RE` 目录运行：

```bash
sudo "$PWD/.venv/bin/python" pmca-console.py serviceshell
```

看到下面的提示即已进入平台命令行：

```text
Welcome to platform shell.
Type `help` for the list of supported commands.
Type `exit` to quit.
```

## 4. 检查语言状态并退出

1. 在 `>` 提示符输入 `tweak`，按回车。
2. 找到 **Unlock all languages**。如果显示 `[ ]`，输入该项前面的编号切换；编号以屏幕为准。
3. 本次相机显示：

   ```text
   1: [X] Disable video recording limit
          13h 01m 00s

   2: [X] Unlock all languages
          35 / 35 languages activated
   ```

   **35 / 35 languages activated** 表示语言已经全部启用。此时不要再按 `2`，否则会关闭该项。第 `1` 项与切换语言无关，不要改动。

4. 在 `Enter number of tweak to toggle (0 to apply):` 提示符输入 `0` 并按回车。
5. 回到 `>` 提示符后输入 `exit` 并按回车。
6. 断开 USB，重启相机，在相机语言设置中选择“日本語”。

## 注意

- 不要将其他地区的 `BODYDATA.DAT` 当作日文版固件刷入。
- [ZV-E10 II 社区记录](https://github.com/ma1co/Sony-PMCA-RE/issues/550)中有滚动某些语言选项时相机重启的报告。如遇到重启，应停止操作并记录出现问题的选项。
- 最终以相机语言菜单能否正常选择并保持“日本語”为准。

## 参考

- [索尼 ZV-E10 II：USB 连接模式](https://helpguide.sony.net/ilc/2430/v1/zh-cn/contents/0601_usb_connection.html)
- [Sony-PMCA-RE](https://github.com/ma1co/Sony-PMCA-RE)
- [ZV-E10 II 语言菜单讨论](https://github.com/ma1co/Sony-PMCA-RE/issues/550)
- [相同 PMCA 提示界面的退出记录](https://github.com/ma1co/Sony-PMCA-RE/issues/652)
