# Stardew 项目复刻指南 (简化版)

## 项目信息
- 引擎: Godot 4.6
- 渲染: GL Compatibility
- 分辨率: 1280x720
- 源码路径: `/Users/m4/.openclaw/workspace/Stardew-main/`

## 快速复刻步骤

### 1. 创建 Godot 4.6 项目
```
项目名称: Stardew
渲染器: GL Compatibility
分辨率: 1280x720
```

### 2. Autoload 配置 (在 project.godot 中添加)
```
Global = "res://Global/global.gd"
SceneManager = "res://Global/scene_manager.tscn"
AudioManager = "res://Global/audio_manager.gd"
MouseCursor = "res://Global/mouse_cursor.tscn"
MouseItem = "res://Global/mouse_item.gd"
TimeSystem = "res://TimeSystem/time_system.gd"
SaveManager = "res://Global/save_manager.gd"
```

### 3. 核心类

#### Item.gd (物品基类)
```gdscript
extends Resource
class_name Item

enum ItemType { None, Tool, Hoe, Axe, Water, Weapon, Consume, Placeables, Crops, Materials }

@export var name:String
@export var type:ItemType
@export var quantity:int = 1
@export var texture:Texture
```

#### InventorySystem.gd (背包)
```gdscript
extends Resource
class_name InventorySystem

@export var items:Array[Item]
@export var items_size:int = 36

func add_item(new_item:Item) -> void
func remove_item(index:int) -> void
func swap_item(target_index:int) -> void
```

#### Player.gd (玩家)
```gdscript
extends CharacterBody2D
class_name Player

@export var bag_system:InventorySystem
@export var current_item:Item

# 连接到 "move_up", "move_down", "move_left", "move_right" 输入
```

#### TimeSystem.gd (时间)
```gdscript
extends Node

const MINUTES_PER_DAY:int = 24 * 60
const GAME_MINUTE_DURATION:float = TAU / MINUTES_PER_DAY

signal time_tick(day:int, hour:int, minute:int, week)
```

#### SceneManager.gd (场景管理)
```gdscript
extends Node

var level_scenes:Dictionary = {
    "Farm": "res://Map/Farm/farm.tscn",
    "Town": "res://Map/Town/town.tscn",
    "MyHouse": "res://Map/MyHouse/my_house.tscn",
    "Store": "res://Map/Store/store.tscn",
}

func change_level(target_level:String, spawn_pos_name:String) -> void
```

### 4. 输入映射
在 project.godot 中配置:
```
move_up: W, ↑
move_down: S, ↓
move_left: A, ←
move_right: D, →
open_bag: E
drop: Q
Esc: Esc
mouse_left: 鼠标左键
mouse_right: 鼠标右键
scroll_up: 鼠标滚轮上
scroll_down: 鼠标滚轮下
```

### 5. 物理层
```
layer_1: Player
layer_2: Objects
layer_3: Tools
layer_4: Placeable
layer_5: Terrain
layer_6: Wall
layer_7: ChangeSceneArea
```

### 6. 文件夹结构
```
项目根目录/
├── Global/
│   ├── global.gd
│   ├── scene_manager.gd
│   ├── audio_manager.gd
│   ├── save_manager.gd
│   ├── mouse_cursor.gd
│   └── mouse_item.gd
├── Player/
│   └── player.gd
├── Bag/
│   └── scene/
│       ├── item.gd
│       ├── inventory_system.gd
│       └── bag_ui.tscn
├── Map/
│   ├── Farm/
│   ├── Town/
│   ├── MyHouse/
│   ├── Store/
│   └── tile_set_*.tres
├── TimeSystem/
│   └── time_system.gd
├── UI/
│   └── (各种UI组件)
└── project.godot
```

## 运行项目

1. 用 Godot 4.6 打开项目目录
2. 按 F5 或点击 Play 运行

---

详细文档见: `docs/PROJECT_DOCUMENTATION.md`
