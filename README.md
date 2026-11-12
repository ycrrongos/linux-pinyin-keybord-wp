# Linux Pinyin Keyboard WP

由 **ycrrongos 使用 Codex 开发**的离线中文拼音核心与 Windows Phone 风格
Denial 屏幕键盘集成。桌面仓库：[win10x-denial-desktop](https://github.com/ycrrongos/linux-winphone-desktop)。

支持全拼、简拼、末音节补全、分词组合、`xi'an` 分隔、ü/v、键盘候选横滑/展开、
中英文切换和本地词频。密码/PIN/敏感输入直接输入，不查询候选或学习。
词库和排序在 worker isolate 中运行；没有云端、语音服务或联网候选。

## 核心包

Dart 3.13+，核心不依赖 Flutter/系统 librime：

```sh
dart pub get
dart analyze lib test
dart test
```

```dart
import 'dart:io';
import 'package:winphone_pinyin/winphone_pinyin.dart';

void main() {
  final engine = PinyinEngine(
    File('assets/ime/pinyin_simp.dict.yaml').readAsStringSync(),
  );
  print(engine.lookup('nihao').map((choice) => choice.text).take(5));
}
```

`PinyinSession` 提供后台 isolate、本地持久化和过期查询丢弃。Linux 个人词频位于
`$XDG_STATE_HOME/win10x/ime/personal.json`（默认 `~/.local/state`），目录700、文件600。
界面层负责只在非敏感编辑器调用学习，并在焦点切换时取消旧组合。

## WP 键盘界面

`integration/denial/` 是 Denial 0.6.2 / revision
`3f08a93ec42b9851ebdd73039def1ff12a639858` 的适配源码。它依赖该版本的原生
文本输入代理、ShellOskPanel 和经过补丁的 Flutter 引擎，不能作为普通 Flutter
应用直接运行，也不是 IBus/Fcitx/Android 系统输入法。

使用 `tools/install-denial-adapter /path/to/denial_desktop` 安装适配文件并替换宿主
键盘构造。宿主 Flutter 应用仍须在 pubspec 中加入本包的 path/git 依赖，提供词库
assets，并使用 Denial 匹配的工具链构建。完整可运行集成和 ARM64 构建入口在桌面仓库。

## 来源与许可

本项目原创代码 GPL-3.0-only；词库来自 Apache-2.0 的 Rime pinyin-simp/AOSP，
完整许可、来源和固定版本见 [assets/ime/NOTICE.md](assets/ime/NOTICE.md)。
Sixuan IME 仅作架构研究参考，没有复制其 PolyForm Noncommercial 代码或数据。
本包不等于原生 Rime 引擎，不提供其完整 schema/plugin 兼容性。

已在 Nubia Z60 Ultra 的原生 Linux/Denial 会话中，通过屏幕键盘候选把“你好”
写入 GTK 应用输入框。其他应用、设备与独立适配部署仍需各自验收。
