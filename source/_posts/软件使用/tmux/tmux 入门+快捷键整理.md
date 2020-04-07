---
title: tmux 入门 + 快捷键整理
date: 2019/5/27
categories:
  - 软件使用
  - tmux
tags:
  - tmux 入门
copyright: true
---

```
本文全部快捷键和配置文件均在以下环境成功运行：
操作系统：MacOS 10.14
tmux 版本：tmux 2.9a
```

最近进入了工作热情高涨的时期，疯狂的重构、打包、上传，每次这样搞都要重复开好多的终端窗口而且要一个一个的用鼠标点来点去的很麻烦（最近莫名的嫌弃鼠标），所以想起了之前被自己放到「待会儿再读」收藏夹的「十分钟学会 `tmux`」，貌似这个工具可以帮助我更好的管理终端窗口。

话不多说直接上手：

PS：如果你也像我一样第一次接触这类软件，那么我建议你不要观望，先把软件下下来然后根据快捷键列表一个一个的尝试一次，再决定要不要使用，整个过程不超过十分钟但是却能给你节省 N 多观望的时间。

## 安装 tmux

`tmux` 可以应用在各类终端上（`Windows` 下未测试）支持 `Vi/Emacs` 风格的键盘映射

```powershell
brew install tmux       # MacOS
pacman -S tmux          # Arch Linux
apt-get install tmux    # Ubuntu
yun install tmux        # CentOS
```

因为装了黑苹果所以不太关注 `Windows` 了，简单的搜索了一下网上是有 `Windows` 下的解决方案的，这里给出一个我看到的文章（未验证）：[OneFeed：在 Windows 上安装 tmux][1]

## 名词理解


| 名词 | 个人理解 |
| --- | --- |
| Session 会话 | 相当于一个浏览器进程 |
| Window 窗口 | 相当于一个浏览器窗口 |
| Tab 窗格 | 相当于一个浏览器页面 |


## 基本操作

```powershell
tmux new -s foo             # 新建名为 foo 的会话
tmux ls                     # 列出全部会话
tmux a                      # 恢复上一次会话
tmux a -t foo               # 恢复名为 foo 的会话
tmux kill-session -t foo    # 删除名为 foo 的会话
tumx kill-server            # 删除所有会话
```

## 常用快捷键整理

`tmux` 默认前缀为 `ctrl + b`

**注意：下面的快捷键整理中默认省略前缀**

**注意：以下仅整理了比较常用的快捷键，可以在 tmux 中通过 `ctrl + b + ?` 的形式获取全部快捷键**

### Session 会话类

```powershell
:new<Enter> # 启动新的会话
s           # 列出全部会话
$           # 重命名当前会话
```

### Window 窗口类

```powershell
c   # 创建新的窗口
w   # 列出全部窗口
n   # 跳到下一个窗口
p   # 跳到前一个窗口
f   # 查找窗口
,   # 重命名当前窗口
&   # 关闭当前窗口
swap-window -s 3 -t 1   # 交换 3 号和 1 号窗口
swap-window -t 1        # 交换当前窗口和 1 号窗口
move-window -t 1        # 移动当前窗口到 1 号窗口
```

### Tab 窗格类

```powershell
%                           # 垂直分割
"                           # 水平分割
o                           # 交换窗格
x                           # 关闭窗格
空格                          # 切换布局
q                           # 显示每个窗格序号，可以通过序号快速定位
{                           # 与上一个窗格交换位置
}                           # 与下一个窗格交换位置
z                           # 切换窗格最大化/最小化
:resize-pane -D             # 当前窗格向下扩大 1 格
:resize-pane -U             # 当前窗格向上扩大 1 格
:resize-pane -L             # 当前窗格向左扩大 1 格
:resize-pane -R             # 当前窗格向右扩大 1 格
:resize-pane -D 20          # 当前窗格向下扩大 20 格
:resize-pane -t 2 -L 20     # 编号为 2 的窗格向左扩大 20 格
```


### 杂项

```powershell
d  # 退出 tmux（tmux 仍在后台运行）
t  # 窗口中央显示一个数字时钟
?  # 列出所有快捷键
:  # 命令提示符
```

## 配置选项

```powershell
# 鼠标支持 - 设置为 on 来启用鼠标(与 2.1 之前的版本有区别，请自行查阅 man page)
* set -g mouse on

# 设置默认终端模式为 256color
set -g default-terminal "screen-256color"

# 启用活动警告
setw -g monitor-activity on
set -g visual-activity on

# 居中窗口列表
set -g status-justify centre

# 最大化/恢复窗格
unbind Up bind Up new-window -d -n tmp \; swap-pane -s tmp.1 \; select-window -t tmp
unbind Down
bind Down last-window \; swap-pane -s tmp.1 \; kill-window -t tmp
```

## 参考配置文件

**注意：具体配置方法在配置文件的备注里，我就不赘述了**

```
# 来源：Github
# 作者：ryerh
# 地址：https://gist.github.com/ryerh/14b7c24dfd623ef8edc7

# -----------------------------------------------------------------------------
# Tmux 基本配置 - 要求 Tmux >= 2.3
# 如果不想使用插件，只需要将此节的内容写入 ~/.tmux.conf 即可
# -----------------------------------------------------------------------------

# C-b 和 VIM 冲突，修改 Prefix 组合键为 Control-Z，按键距离近
set -g prefix C-z

set -g base-index         1     # 窗口编号从 1 开始计数
set -g display-panes-time 10000 # PREFIX-Q 显示编号的驻留时长，单位 ms
set -g mouse              on    # 开启鼠标
set -g pane-base-index    1     # 窗格编号从 1 开始计数
set -g renumber-windows   on    # 关掉某个窗口后，编号重排

setw -g allow-rename      off   # 禁止活动进程修改窗口名
setw -g automatic-rename  off   # 禁止自动命名新窗口
setw -g mode-keys         vi    # 进入复制模式的时候使用 vi 键位（默认是 EMACS）

# -----------------------------------------------------------------------------
# 使用插件 - via tpm
#   1. 执行 git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
#   2. 执行 bash ~/.tmux/plugins/tpm/bin/install_plugins
# -----------------------------------------------------------------------------

setenv -g TMUX_PLUGIN_MANAGER_PATH '~/.tmux/plugins'

# 推荐的插件（请去每个插件的仓库下读一读使用教程）
set -g @plugin 'seebi/tmux-colors-solarized'
set -g @plugin 'tmux-plugins/tmux-pain-control'
set -g @plugin 'tmux-plugins/tmux-prefix-highlight'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tpm'

# tmux-resurrect
set -g @resurrect-dir '~/.tmux/resurrect'

# tmux-prefix-highlight
set -g status-right '#{prefix_highlight} #H | %a %Y-%m-%d %H:%M'
set -g @prefix_highlight_show_copy_mode 'on'
set -g @prefix_highlight_copy_mode_attr 'fg=white,bg=blue'

# 初始化 TPM 插件管理器 (放在配置文件的最后)
run '~/.tmux/plugins/tpm/tpm'

# -----------------------------------------------------------------------------
# 结束
# -----------------------------------------------------------------------------
```

## 有关 Vim 快捷键

由于我对 `Vim` 的操作方式还不是很熟悉所以暂时不整理这部分内容了，等我什么时候用到了再来更新。

## 参考

[猫哥_kaiye：十分钟学会 tmux][2]
[ryerh: Tmux 快捷键 & 速查表][3]
[Hattle Land：优雅地使用命令行：Tmux 终端复用][4]

[1]: https://onefeed.xyz/posts/tmux-on-windows.html
[2]: https://www.cnblogs.com/kaiye/p/6275207.html
[3]: https://gist.github.com/ryerh/14b7c24dfd623ef8edc7
[4]: https://harttle.land/2015/11/06/tmux-startup.html