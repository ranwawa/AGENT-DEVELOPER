# wezterm连接远程终端时自动复用tumx

## 问题描述

现状:
1. wsl
2. ssl ranwawa@ip
3. 输入密码
4. 定位到开发目录
5. 启用tumx
6. 打开opencode

以便手机,windows,mac之间得用会话历史和减少初始化动作

## 解决办法


免密登录(客户机)
```
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub | ssh ranwawa@ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

ssh远程主机别名配置
```
Host mac 100.121.201.30
  HostName 100.121.201.30
  User ranwawa
  ProxyCommand wsl sh -lc 'exec nc %h %p'
  SetEnv LANG=en_US.UTF-8
  SetEnv LC_CTYPE=en_US.UTF-8
  SetEnv LC_ALL=en_US.UTF-8
  ServerAliveInterval 30
  ServerAliveCountMax 3
```

自动复用tmux(服务端)
```
# ssh tmux
if [[ -n "$SSH_CONNECTION" ]] && [[ -z "$TMUX" ]]; then
  echo '跳转到kinkeeper项目目录'
  cd ~/Documents/rww/kinkeeper
  if command -v tmux >/dev/null 2>&1; then
    echo '复用tmux会话'
    tmux attach -t dev -d || tmux new-session -s dev
  else
    echo 'tmux不存在,降级到ssh'
  fi
fi
```

优化后的步骤:
ssh mac
