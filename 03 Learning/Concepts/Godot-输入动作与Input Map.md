---
type: concept
project: Godot Practice
tags:
  - concept
---

# Godot-输入动作与 Input Map

## 来源
- [InputMap](https://docs.godotengine.org/zh-cn/4.x/classes/class_inputmap.html)
- [Input](https://docs.godotengine.org/zh-cn/4.x/classes/class_input.html)
- [监听玩家的输入](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/scripting_player_input.html)

## 是什么
Input Map 把“按下哪个物理按键或手柄按钮”转换成有业务含义的动作名，例如 `move_left`、`jump`、`pause`。一项动作可以绑定多个物理输入，代码只依赖动作名，因此以后可以改键或支持手柄而不修改游戏逻辑。

`InputMap` 管理动作配置；`Input` 单例负责在运行时查询动作状态。

`物理输入 → Input Map → 逻辑动作 → Input 查询`

## 常用查询

| API | 适合的场景 |
| --- | --- |
| `Input.is_action_pressed()` | 持续移动、瞄准等需要每帧查询的状态 |
| `Input.is_action_just_pressed()` | 暂停、互动等一次按下只触发一次的动作 |
| `Input.get_vector()` | 把四个方向动作组合成长度不超过 1 的二维向量 |

## 什么时候用
- 为键盘、手柄等不同设备提供相同的游戏动作。
- 允许玩家改键。
- 把“输入设备细节”和“角色要做什么”分开。
- 连续动作使用轮询；离散事件也可以在输入回调中处理。

## 最小例子
```gdscript
extends Node


# 读取四个逻辑动作，返回长度不超过 1 的二维输入向量。
func read_move_direction() -> Vector2:
	return Input.get_vector("move_left", "move_right", "move_up", "move_down")
```

## 常见误区
- 动作名拼写必须和 Input Map 完全一致，否则查询会始终返回未按下。
- `ui_left` 等内置动作主要面向界面导航；游戏移动更适合自定义 `move_left` 等动作。
- `Input.get_vector()` 已限制最大长度并处理圆形死区；不要再无条件调用 `normalized()`，否则会丢失摇杆力度。
- Button 等 UI 控件通常使用 `pressed` 信号，不需要每帧轮询鼠标点击。

## 相关笔记
- [[Godot-帧率无关的二维移动]]
- [[Godot-信号]]
- [[Godot-渐进式教程]]
- [[Godot-你的第一个2D游戏]]
