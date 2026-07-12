---
type: concept
project: Godot Practice
tags:
  - concept
---

# Godot-帧率无关的二维移动

## 来源
- [空闲处理与物理处理](https://docs.godotengine.org/zh-cn/4.x/tutorials/scripting/idle_and_physics_processing.html)
- [Vector2](https://docs.godotengine.org/zh-cn/4.x/classes/class_vector2.html)
- [2D 运动概述](https://docs.godotengine.org/zh-cn/4.x/tutorials/2d/2d_movement.html)
- [Player 移动与归一化](https://docs.godotengine.org/zh-cn/4.x/getting_started/first_2d_game/03.coding_the_player.html)

## 是什么
帧率无关移动把“每秒移动多少”转换成“这一帧应该移动多少”，使不同帧率下的速度尽量一致。

`方向 × 速率 = 速度`  
`速度 × delta = 本帧位移`  
`位置 + 本帧位移 = 新位置`

| 量 | 含义 | 常用单位 |
| --- | --- | --- |
| `direction` | 移动方向和输入力度 | 无单位 |
| `speed` | 标量速率 | 像素/秒 |
| `velocity` | 带方向的速度 | 像素/秒 |
| `delta` | 距离上次处理经过的时间 | 秒 |
| `position` | 节点当前位置 | 像素 |

## 什么时候用
- 手动更新 `Node2D` 或 `Area2D` 的位置。
- 在 `_process(delta)` 中执行与渲染帧同步的移动或动画。
- 涉及物理引擎和碰撞响应时，改用固定频率的 `_physics_process()`。

## 最小例子
```gdscript
extends Area2D

@export var speed := 400.0


# 每帧把二维输入方向转换为按秒计速率的位移。
func _process(delta: float) -> void:
	var direction := Input.get_vector("move_left", "move_right", "move_up", "move_down")
	var velocity := direction * speed
	position += velocity * delta
```

`Input.get_vector()` 返回的向量长度不会超过 1。手动把四个按键累加成 `(±1, ±1)` 时，斜向长度会是 `√2`，这时才需要在非零时调用 `normalized()`。

## 常见误区
- 只修改 `speed` 或 `velocity` 不会移动节点，必须把位移应用到 `position`。
- 把 `velocity` 存成成员变量却不在松键时重置，会沿旧方向继续移动。
- 手动更新 `position` 时忘记乘 `delta`，会导致速度随帧率变化。
- `CharacterBody2D.move_and_slide()` 会按物理步处理 `velocity`；不要再把该速度额外乘一次 `delta`。
- `delta` 适合帧间运动，不适合精确测量现实世界时间；计时流程使用 [[Godot-Timer与计时流程|Timer]]。

## 相关笔记
- [[Godot-输入动作与Input Map]]
- [[Godot-节点、场景与场景树]]
- [[Godot-渐进式教程]]
- [[Godot-你的第一个2D游戏]]
