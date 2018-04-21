# markdown上下左右，跳至行尾行首，重设快捷键 #

## 概述 ##

用markdown输入代码的时候觉得下面2件事非常不方便：

(1)光标上下左右。(需要挪动手去按方向键)

(2)光标跳至行尾和行首。(需要动手去按Home和End键)

为了简化，我特地更改了ST3的快捷键，供自己开发时参考，相信对其他人也有用。

### 代码 ###

在ST3的快捷键设置中加入如下代码即可：

```
    { "keys": ["alt+a"], "command": "move_to", "args": {"to": "bol", "extend": false} },
    { "keys": ["alt+f"], "command": "move_to", "args": {"to": "eol", "extend": false} },
    { "keys": ["alt+j"], "command": "move", "args": {"by": "characters", "forward": false} },
    { "keys": ["alt+l"], "command": "move", "args": {"by": "characters", "forward": true} },
    { "keys": ["alt+i"], "command": "move", "args": {"by": "lines", "forward": false} },
    { "keys": ["alt+k"], "command": "move", "args": {"by": "lines", "forward": true} },
```

快捷键设置情况为：

上： alt + I

下： alt + K

左： alt + J

右： alt + L

跳至行首： alt + A

跳至行尾： alt + F


