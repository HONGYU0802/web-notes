# vscode 使用教程

## 概念

| IDE | VSCode |
| :---: | :---: |
| Integrated Development Environment | Visual Studio Code Editor |
| 集成开发环境 | 文本编辑器 |
| 代码编写功能、分析功能、编译功能、调试功能 | 各类插件 |
| 专注于某一门语言 | 使用插件可以支持多种语言 |
| 后端适合 | 全栈和前端适合|

它内置了对 JS、TS、NodeJS 的支持，还有 Git

## 优点

1. 轻量
2. 智能的代码补全 intelliSense
3. 高效的调试 streamlined Debug
4. 代码导航 Code navigator
5. 高效的编辑 Fast editing

## 版本

Commit: c3511e6c69bb39013c4a4b7b9566ec1ca73fc4d5
Date: 2022-05-17T18:20:57.384Z
Electron: 17.4.1
Chromium: 98.0.4758.141
Node.js: 16.13.0
V8: 9.8.177.13-electron.0
OS: Darwin x64 21.5.0 -->

## 结构

### command palette 命令面板

Code 设置软件
File 文件操作
Edit 编辑区操作
Selection 编辑区选中操作
View 视口设置
Go 快速跳转
Run 调试代码
Terminal 终端操作
Window 窗口操作

### Activity Bar 活动栏

#### explorer 探索

open editors 工作区里打开的文件
project 打开的项目文件夹
outline 正在编辑的文件大纲
timeline 文件保存时间记录

#### search
#### source control
#### debug
#### extensions

@builtin
@installed
@category:"theme"

### Side Bar 侧边栏

展示 Activity Bar 活动栏选中的内容，里面的每一个 item 都有自己的 toolbar

### Status Bar 状态栏

source control
problem 包含 error,info,warn
cursor 所在的行和列
space indentation 空格缩进个数
encoding 文本使用的编码
language mode 文本使用的语言
spell checked 文本拼写检查
notification

### Editor 编辑器 （workspace）

修改布局：上下分栏、左右分栏

### panel 仪表盘

problems
output
debug console
terminal

## Plugin 插件


## Shortcut 快捷方式

控制屏幕 View

```
cmd - 缩小
cmd + 放大
cmd sft w 关闭当前窗口
```

控制编辑区 Editor

```
cmd p 创建新文件
cmd d 选取一段连续的内容
cmd sft k 删除一整行

opt 上下移动行文本，左右跳过一段连续的内容
opt sft 上下拷贝文本，左右选取一段连续的内容

opt cmd 上下添加光标
opt click 创建多个光标

opt sft f 格式化当前文件的全部代码
cmd k cmd f 格式化当前行代码

cmd sft a 块注释
cmd / 行注释

opt cmd [ 收起当前光标所在的父代码块
opt cmd ] 展开当前光标所在的父代码块

f8 快速跳转到错误提示行
```

## Snippets 片段

通过简写提供完整的代码块，加速构建文件

## 相关术语

SCM Software configuration management 软件配置管理

intelliSense 当你打出 . 时，会运行一个 JS 语言服务

keyboard shortcut 查看和编辑全部的快捷方式

editor background 快速熟悉 vscode 技巧