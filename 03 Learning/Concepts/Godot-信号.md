---
type: concept
project: Godot Practice
tags:
  - concept
---

# Godot-信号

## 来源
- [使用信号](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/signals.html)
- [Signal 类参考](https://docs.godotengine.org/zh-cn/4.x/classes/class_signal.html)

## 是什么
信号是对象在特定事件发生时发出的具名消息。监听者把回调连接到信号后，信号发出时 Godot 会调用这些回调。

发出者只负责宣布“发生了什么”，不需要知道谁会响应；因此一个事件可以没有监听者，也可以被多个对象监听。

## 什么时候用
- 响应 Button 点击、Timer 到期、碰撞以及进入或离开区域等引擎事件。
- 表达 `health_depleted`、`hit`、`start_game` 等自定义游戏事件。
- 子节点需要通知父节点，但不应该直接依赖父节点的具体实现时。
- 动态实例化节点后，需要在代码中订阅它的事件时。

如果只是向一个已知对象发送明确命令，直接调用方法通常更清楚；信号也不适合代替每帧连续查询状态。

## 最小模型
1. 用 `signal event_name` 声明自定义信号。
2. 用 `source.event_name.connect(callback)` 连接监听者。
3. 在事件发生时调用 `event_name.emit()`。

## 最小例子
```gdscript
# Player.gd
extends Node

signal health_depleted

var health := 10


# 扣除生命值，并只在生命值第一次归零时发出事件。
func take_damage(amount: int) -> void:
	if health <= 0:
		return

	health = maxi(health - amount, 0)

	if health == 0:
		health_depleted.emit()
```

```gdscript
# Main.gd
extends Node

@onready var player = $Player


# 节点准备好后，订阅 Player 的生命耗尽事件。
func _ready() -> void:
	player.health_depleted.connect(_on_player_health_depleted)


# 收到事件后处理游戏结束，Player 不需要知道 Main 的存在。
func _on_player_health_depleted() -> void:
	print("Game Over")
```

固定的场景关系可以在编辑器的 Signals 面板连接；动态创建的节点通常在代码中连接。

## 常见误区
- 只声明或发出信号不会自动产生业务行为；必须至少有一个监听者连接回调。
- 带参数的信号发出时，参数顺序应与回调签名匹配。
- 信号名称应描述已经发生的事件，通常使用过去式或完成状态，例如 `health_depleted`。

## 相关笔记
- [[Godot-Timer与计时流程]]
- [[Godot-渐进式教程]]
- [[Godot-你的第一个2D游戏]]
