---
type: tutorial
project: Godot Practice
tags:
  - tutorial
---

# Godot-渐进式教程

## 来源
- [渐进式教程](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/index.html)
- [创建第一个脚本](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/scripting_first_script.html)
- [监听玩家的输入](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/scripting_player_input.html)
- [使用信号](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/signals.html)
- [[2026-07-09]] 详细记录的是脚本、输入和信号阶段。

## 最终成果
- 建立了“场景结构 → 脚本行为 → 每帧输入与运动 → 信号通信”的基础模型。
- 完成一组连续小实验：输出 Hello World，让 `Sprite2D` 旋转和移动，用输入控制它，用 Button 暂停或恢复处理，用 Timer 切换可见性，并定义 `health_depleted` 自定义信号。
- 这些实验不是完整游戏，而是在为“第一个 2D 游戏”的组件协作做准备。

## 整体结构
- **节点与场景**：节点是功能单元，场景是一棵可保存和复用的节点树；运行项目时从主场景开始实例化场景树。
- **场景实例**：保存的场景可以作为蓝图反复实例化，各实例可以保留自己的属性覆盖。
- **脚本与节点**：脚本通过 `extends` 继承所附节点的属性和方法，例如 `Sprite2D` 的 `position` 和 `rotation`。
- **引擎回调**：Godot 在特定时机调用 `_init()`、`_ready()` 和 `_process(delta)`；脚本不需要自己编写主循环。
- **输入与运动**：`Input` 提供当前输入状态，脚本把输入转成方向和速度，再显式更新节点变换。
- **节点通信**：节点发出信号表示事件发生，监听者通过回调作出响应；详见 [[Godot-信号]]。

## 运行流程
1. Godot 实例化场景树，脚本随节点创建；节点就绪后执行 `_ready()`，可在这里完成代码中的信号连接。
2. 每一帧执行 `_process(delta)`：读取输入动作，计算方向与速度，再更新 `rotation` 和 `position`。
3. Button 发出 `pressed` 后，回调通过 `set_process()` 暂停或恢复每帧处理。
4. Timer 发出 `timeout` 后，回调切换节点的 `visible`。
5. 生命值归零时发出自定义 `health_depleted`；只有已经连接的监听者会执行响应。

## 关键概念
- **GDScript 类**：一个脚本代表一个类；文件顶部变量是成员变量，方法内部变量是局部变量。
- **[[Godot-节点、场景与场景树|节点、场景与场景树]]**：节点组成树，场景保存可复用结构，实例进入 SceneTree 后才参与运行。
- **[[Godot-输入动作与Input Map|输入动作与 Input Map]]**：Input Map 把键盘或手柄映射成逻辑动作，`Input` 查询动作状态。
- **`_process(delta)` 与 [[Godot-帧率无关的二维移动|二维移动]]**：`direction × speed` 得到速度，`velocity × delta` 得到本帧位移。
- **[[Godot-Timer与计时流程|Timer 与计时流程]]**：Timer 发出 `timeout` 驱动重复或一次性流程。
- **[[Godot-信号|信号]]**：内置信号和自定义信号使用同一模型——连接、发出、回调。

```gdscript
# 每帧把速度转换成位移；只修改 speed 并不会自动改变 position。
func _process(delta: float) -> void:
	var velocity := Vector2.UP.rotated(rotation) * speed
	position += velocity * delta
```

## 自己的改动
- Daily 中没有记录偏离教程的改动。

## 卡住的问题
- **改了速度没有改位置**：`speed` 和 `velocity` 只是运动数据，不会自动移动节点；还需要执行 `position += velocity * delta`。如果 `velocity` 是成员变量，也要注意松开按键后将它重置，否则会沿旧速度继续移动。
- **自定义事件需要自己连线**：是的。`signal` 只声明事件，`emit()` 只发布事件；必须在编辑器中连接，或调用 `connect()` 指定回调。
- **Daily 示例会重复发出信号**：把 `take_damage(1)` 放在 `_process()` 中会每帧扣血，并在生命值归零后持续发出信号。长期代码应只在状态第一次变为“生命耗尽”时发出一次。

## 理解检查
- [ ] 不看教程也能解释场景、脚本、输入和信号之间的关系
- [ ] 能讲清输入如何在一帧中变成位置变化
- [ ] 不理解的地方已经明确记录

## 相关笔记
- [[2026-07-09]]
- [[Godot-信号]]
- [[Godot-节点、场景与场景树]]
- [[Godot-输入动作与Input Map]]
- [[Godot-帧率无关的二维移动]]
- [[Godot-Timer与计时流程]]
- [[Godot-你的第一个2D游戏]]
