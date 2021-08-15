# Tmux使用
[toc]

# 安装：
* CentOs: yum -y install tmux
* MaxOs: brew install tmux
窗格操作：
* %  左右平分出两个窗格
* "  上下平分出两个窗格
* x  关闭当前窗格
* {  当前窗格前移
* }  当前窗格后移
* ;  选择上次使用的窗格
* o  选择下一个窗格，也可以使用上下左右方向键来选择
* space 切换窗格布局，tmux 内置了五种窗格布局，也可以通过 ⌥1 至 ⌥5来切换
* z  最大化当前窗格，再次执行可恢复原来大小
* q  显示所有窗格的序号，在序号出现期间按下对应的数字，即可跳转至对应的窗格

# 窗口操作
tmux 除了窗格以外，还有窗口（window） 的概念。依次使用以下快捷键来熟悉 tmux 的窗口操作：
* c 新建窗口，此时当前窗口会切换至新窗口，不影响原有窗口的状态
* p 切换至上一窗口
* n 切换至下一窗口
* w 窗口列表选择，注意 macOS 下使用 ⌃p 和 ⌃n 进行上下选择
* & 关闭当前窗口
* , 重命名窗口，可以使用中文，重命名后能在 tmux 状态栏更快速的识别窗口 id
* 0 切换至 0 号窗口，使用其他数字 id 切换至对应窗口
* f 根据窗口名搜索选择窗口，可模糊匹配

# 会话操作
如果运行了多次 tmux 命令则会开启多个 tmux 会话（session）。在 tmux 会话中，使用前缀快捷键 ⌃b 配合以下快捷键可操作会话：
* $ 重命名当前会话
* s 选择会话列表
* d detach 当前会话，运行后将会退出 tmux 进程，返回至 shell 主进程
* 在 shell主进程下运行以下命令可以操作 tmux 会话：
    * tmux new -s foo # 新建名称为 foo 的会话
    * tmux ls # 列出所有 tmux 会话
    * tmux a # 恢复至上一次的会话
    * tmux a -t foo # 恢复名称为 foo 的会话，会话默认名称为数字
    * tmux kill-session -t foo # 删除名称为 foo 的会话
    * tmux kill-server # 删除所有的会话
* 除以上提到的快捷键以外，tmux 还有许多其他的快捷键和命令，使用前缀快捷键 ⌃b 加 ? 可以查看所有的快捷键列表，该列表视图为 tmux copy 模式，该模式下可使用以下快捷键（无需加 ⌃b 前缀）：
    *  ⌃v 下一页
    *  Meta v 上一页 （tmux 快捷键为 Emacs 风格，这里的 Meta 键可用 Esc 模拟）
    *  ⌃s 向前搜索
    *  q 退出 copy 模式

    
```bash
tmux rename-session -t <会话名>
tmux choose-session -t <会话名>
tmux kill-session -t <会话名>
查看会话列表         tmux ls
创建一个新会话       tmux new -s [session_name] 名称 
连接会话            tmux a(attach) -t [session_name] 名称  
退出会话            ctrl+b d
会话环境查看会话列表  ctrl+b s
创建窗口            ctrl+b c
关闭窗口：           ctrl+b &
垂直分屏：           ctrl+b %
水平分屏：           ctrl+b "
关闭pane：          ctrl+b x
帮助：              ctrl+b ?
选择关闭会话，该命令会列出当前会话的登录列表  ctrl-b + shift-d
切换窗口:
     ctrl+b p (previous的首字母) 切换到上一个window。
     ctrl+b n (next的首字母) 切换到下一个window。
     ctrl+b 0 切换到0号window，依次类推，可换成任意窗口序号
    ctrl+b w (windows的首字母) 列出当前session所有window，通过上、下键切换窗口
    ctrl+b l (字母L的小写)相邻的window切换
切换pane：
    ctrl+b o 依次切换当前窗口下的各个pane。
    ctrl+b Up|Down|Left|Right 根据按箭方向选择切换到某个pane。
    ctrl+b Space (空格键) 对当前窗口下的所有pane重新排列布局，每按一次，换一种样式。
    ctrl+b z 最大化当前pane。再按一次后恢复。
```