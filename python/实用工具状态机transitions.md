+++
title = "tomli支持数组混合类型"
date = 2022-07-25T12:12:00+08:00
summary = "状态机是一个非常实用的理论。在涉及到复杂的场景，建立状态机模型，能带来极大的方便。比如，网络连接、模型状态、业务逻辑。"
categories = ["python"]
tags = ["python", "transitions"]
draft = false
+++

### 说明

1. 状态机是一个非常实用的理论。在涉及到复杂的场景，建立状态机模型，能带来极大的方便。比如，网络连接、模型状态、业务逻辑。

2. 状态机并不复杂, 重要的是它的思想，能够极大减轻复杂度。使用时关键在于定义好事件和动作。


### 基本概念

```
State: 状态
Event: 事件. 事件触发状态变换
Action: 动作. event发生前或后执行的动作
transition: 变换. 状态变换
```

### github

https://github.com/pytransitions/transitions

### 安装

```
pip install transitions
```

### 简单示例

```
# 连接协议状态机
from transitions.extensions import HierarchicalMachine as Machine
from transitions.extensions.nesting import NestedState

class ConnectionStateMachine:
    """Connection state machine."""

    def __init__(self, callbacks=None):
        """
        :param callbacks: callbacks for the state machine
        """
        self.callbacks = {}

        # 定义状态
        self.states = ["STATE_NOT_CONNECTED",
                       {
                            'name': "STATE_CONNECTED",   # 状态名
                            'on_enter': self._on_enter_CONNECTED,  # 进入状态触发
                            'on_exit': self._on_exit_CONNECTED,    # 退出状态触发
                            'children': [     # 状态嵌套
                                "STATE_NOT_SELECTED",
                                {
                                    'name': "STATE_SELECTED",
                                    'on_enter': self._on_enter_CONNECTED_SELECTED
                                }
                            ]
                       }]

        # transition 1
        self.machine = Machine(model=self, states=self.states, initial="STATE_NOT_CONNECTED", auto_transitions=False)

        if callbacks:
            self.callbacks = callbacks

        # 定义状态变换
        self.machine.add_transition('connect', "STATE_NOT_CONNECTED", "STATE_CONNECTED_NOT_SELECTED")  # transition 2
        self.machine.add_transition('disconnect', "STATE_CONNECTED", "STATE_NOT_CONNECTED")  # transition 3
        self.machine.add_transition('select', "STATE_CONNECTED_NOT_SELECTED", "STATE_CONNECTED_SELECTED")  # transition 4
        self.machine.add_transition('deselect', "STATE_CONNECTED_SELECTED", "STATE_CONNECTED_NOT_SELECTED")  # transition 5
        self.machine.add_transition('timeoutT7', "STATE_CONNECTED_NOT_SELECTED", "STATE_NOT_CONNECTED")  # transition 6

    # 事件触发的动作
    def _on_enter_CONNECTED(self):
        if "on_enter_CONNECTED" in self.callbacks:
            self.callbacks["on_enter_CONNECTED"]()

    def _on_exit_CONNECTED(self):
        if "on_exit_CONNECTED" in self.callbacks:
            self.callbacks["on_exit_CONNECTED"]()

    def _on_enter_CONNECTED_SELECTED(self):
        if "on_enter_CONNECTED_SELECTED" in self.callbacks:
            self.callbacks["on_enter_CONNECTED_SELECTED"]()
```

### 定义状态机

```
# 一般都是两种：不嵌套、嵌套
from transitions import Machine   #不嵌套
from transitions.extensions import HierarchicalMachine as Machine # 嵌套

# 检查状态
machine.state
machine.is_<state_name>()

```

### 定义状态

```
# 状态可以是三种类型: 对象、字符串、字典
from transitions import State
states = [
    State(name="solid"),    # 对象
    "liquid",               # 字符串
    {"name": "gas"}         # 字典
]
```

### 定义变换

```
# 定义函数
machine.add_transition(trigger, source, dest, )
trigger(str)         方法名,触发状态转换
source(str or list)  源状态
dest(str)            目标状态

# 加上变换
machine.add_transition(trigger="wake_up", source="asleep", dest="hanging out")
machine.add_transition('work_out', 'hanging out', 'hungry')
machine.add_transition('nap', '*', 'asleep')   # 从任意状态变为asleep
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)


