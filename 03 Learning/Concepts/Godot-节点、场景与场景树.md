---
type: concept
project: Godot Practice
tags:
  - concept
---

# Godot-节点、场景与场景树

## 来源
- [节点与场景](https://docs.godotengine.org/zh-cn/4.x/getting_started/step_by_step/nodes_and_scenes.html)
- [节点与场景实例](https://docs.godotengine.org/zh-cn/4.x/tutorials/scripting/nodes_and_scene_instances.html)
- [PackedScene](https://docs.godotengine.org/zh-cn/4.x/classes/class_packedscene.html)

## 是什么

| 概念 | 含义 |
| --- | --- |
| Node | 运行时的功能单元，拥有属性、方法和子节点 |
| Scene | 以一个根节点为起点、可以保存和复用的节点树设计 |
| PackedScene | 场景保存在磁盘或加载进内存后的资源形式，不是活节点 |
| Scene Instance | 调用 `PackedScene.instantiate()` 后创建出来的一棵实际节点树 |
| SceneTree | 游戏当前正在运行的节点层级；节点进入其中后才会参与处理，并接收 `_enter_tree()`、`_ready()` 等树相关回调 |

核心关系是：

`场景文件 → PackedScene → instantiate() → 节点实例 → add_child() 到活动父节点 → SceneTree`

## 节点引用
- `get_node("HUD")` 按名称和 NodePath 查找节点，不按节点类型查找。
- `$HUD` 是 `get_node("HUD")` 的 GDScript 简写，路径从当前脚本所在节点开始计算。
- `@onready` 把引用初始化推迟到 `_ready()` 之前，此时依赖的子节点已经进入场景树。
- `_init()` 发生在节点进入场景树之前；`_ready()` 发生在该节点及其子节点都已进入场景树之后。

## 什么时候用
- 把 Player、Mob、HUD 等可独立复用的组件保存成场景。
- 在运行时动态创建敌人、子弹或特效。
- 缓存脚本需要反复访问的子节点引用。
- 判断一段逻辑应该属于单个节点、可复用场景，还是负责协调它们的父场景。

## 最小例子
```gdscript
extends Node

@export var mob_scene: PackedScene
@onready var mob_container: Node = $MobContainer


# 从场景模板创建 Mob，并把实例加入已在活动场景树中的容器。
func spawn_mob() -> void:
	var mob := mob_scene.instantiate()
	mob_container.add_child(mob)
```

## 常见误区
- `instantiate()` 只创建节点实例；把它 `add_child()` 到活动父节点后才会立即进入 SceneTree。若父节点尚未进入树，子节点会随父节点稍后一起进入。
- `$Child` 是相对路径，重命名或移动节点后需要同步修改路径。
- 在子节点尚未进入树时读取引用可能失败；固定依赖通常用 `@onready` 或在 `_ready()` 中获取。
- Scene、PackedScene、场景实例和 SceneTree 是四个不同层次，不应混为一谈。

## 相关笔记
- [[Godot-渐进式教程]]
- [[Godot-你的第一个2D游戏]]
- [[Godot-输入动作与Input Map]]
- [[Godot-帧率无关的二维移动]]
