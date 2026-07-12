---
type: concept
project: Godot Practice
tags:
  - concept
---

# Godot-Timer 与计时流程

## 来源
- [Timer](https://docs.godotengine.org/zh-cn/4.x/classes/class_timer.html)
- [SceneTree.create_timer()](https://docs.godotengine.org/zh-cn/4.x/classes/class_scenetree.html)
- [SceneTreeTimer](https://docs.godotengine.org/zh-cn/4.x/classes/class_scenetreetimer.html)
- [GDScript 的 await](https://docs.godotengine.org/zh-cn/4.x/tutorials/scripting/gdscript/gdscript_basics.html)

## 是什么
`Timer` 和 `SceneTreeTimer` 都会在倒计时结束时发出 `timeout` 信号；`await` 只是等待这个信号后继续当前函数，它本身不是计时器。

| 方式 | 特点 | 适合的场景 |
| --- | --- | --- |
| `Timer` 节点 | 可在场景和 Inspector 中配置，可启动、停止、循环或设为一次性 | 刷怪、持续计分、可取消的重复节拍 |
| `get_tree().create_timer()` | 创建无需添加节点、到期后自动释放的一次性 `SceneTreeTimer` | 简短的一次性延迟 |
| `await timer.timeout` | 暂停当前函数，信号到达后从下一行继续 | 按顺序展示消息或安排一次性流程 |

## 什么时候用
- 重复执行刷怪、计分或状态检查。
- 延迟显示、隐藏 UI。
- 在多个阶段之间插入等待时间。
- 需要明确停止或重新开始倒计时时使用 Timer 节点。

## 最小例子
```gdscript
extends CanvasLayer

@onready var spawn_timer: Timer = $SpawnTimer


# 节点就绪后只连接一次重复计时器，并开始倒计时。
func _ready() -> void:
	spawn_timer.timeout.connect(_on_spawn_timer_timeout)
	spawn_timer.start()


# 每次重复计时器到期时响应 timeout。
func _on_spawn_timer_timeout() -> void:
	print("Spawn one mob")


# 显示消息，等待一次性计时器到期后再隐藏；游戏的其他部分仍会继续运行。
func show_message_temporarily() -> void:
	$Message.show()
	await get_tree().create_timer(1.0).timeout
	$Message.hide()
```

## 常见误区
- `await` 只暂停当前函数，不会冻结整个游戏。
- 重复节拍通常连接一次 `Timer.timeout`；不要在每次 `start()` 时重复连接同一个回调。
- 同一等待函数被多次调用时，会产生多个互不等待的流程。
- 简单延迟不需要在 `_process()` 中手动累加 `delta`。
- `timeout` 的连接和发射机制见 [[Godot-信号]]。

## 相关笔记
- [[Godot-信号]]
- [[Godot-渐进式教程]]
- [[Godot-你的第一个2D游戏]]
- [[Godot-帧率无关的二维移动]]
