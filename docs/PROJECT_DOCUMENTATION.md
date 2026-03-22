# 星露谷物语风格游戏项目文档

## 项目概述

- **项目名称**: Stardew (基于星露谷物语)
- **引擎**: Godot 4.6
- **渲染方式**: GL Compatibility (2D)
- **分辨率**: 1280x720
- **项目路径**: `/Users/m4/.openclaw/workspace/Stardew-main`

---

## 目录结构

```
Stardew-main/
├── Global/                    # 全局系统 (Autoload)
│   ├── global.gd              # 全局常量和预加载
│   ├── scene_manager.gd       # 场景管理器
│   ├── audio_manager.gd       # 音频管理器
│   ├── save_manager.gd        # 存档管理器
│   ├── mouse_cursor.gd        # 鼠标光标
│   └── mouse_item.gd          # 鼠标携带物品
├── Player/                    # 玩家系统
│   ├── player.gd              # 玩家主脚本
│   ├── state_machine/         # 状态机
│   └── components/            # 玩家组件
├── Bag/                      # 物品系统
│   └── scene/
│       ├── item.gd            # 物品类定义
│       ├── inventory_system.gd # 背包系统
│       ├── bag_ui.gd          # 背包UI
│       ├── slot.gd            # 物品槽
│       └── tool_bar.gd        # 快捷工具栏
│   ├── items/
│   │   ├── tools/             # 工具 (锄头、斧头、水壶等)
│   │   ├── crops/             # 作物
│   │   ├── seeds/             # 种子
│   │   ├── materials/         # 材料
│   │   ├── weapon/            # 武器
│   │   └── objects/           # 可放置物品
│   └── projectiles/           # 投射物
├── Map/                      # 地图场景
│   ├── Farm/                 # 农场
│   │   ├── farm.gd
│   │   └── farm.tscn
│   ├── Town/                 # 城镇
│   ├── MyHouse/              # 玩家房屋
│   ├── Store/                # 商店
│   ├── tile_set_outdoor.tres # 室外图块集
│   ├── tile_set_indoor.tres  # 室内图块集
│   └── change_scene_area.gd  # 场景切换区域
├── NPC/                      # NPC系统
│   ├── emily.gd              # NPC示例
│   ├── pig.gd                # 动物示例
│   └── dialogue/              # 对话数据
├── Terrain/                  # 地形系统
│   ├── Grass/                # 草地
│   ├── Trees/                 # 树木
│   └── Rocks/                 # 岩石
├── Placeables/               # 可放置物品
│   ├── placeable.gd          # 可放置基类
│   ├── place_component.gd     # 放置组件
│   └── Crops/                # 作物系统
├── UI/                      # 用户界面
│   ├── game_start_menu.gd    # 开始菜单
│   ├── ui_manager.gd         # UI管理器
│   ├── tool_tip.gd           # 物品提示
│   └── UI_theme.tres          # UI主题
├── TimeSystem/              # 时间系统
│   └── time_system.gd        # 游戏时间 (Autoload)
├── AudioSystem/             # 音频系统
├── SaveSystem/              # 存档系统
├── Component/               # 通用组件
│   ├── hit_component.gd      # 碰撞组件
│   ├── hurt_component.gd    # 伤害组件
│   └── tilemap_component.gd # 地图组件
├── Weapons/                 # 武器
│   └── jian_qi.gd            # 剑武器
├── FallObjects/             # 掉落物
│   └── fall_object_component.gd
├── Shader/                  # 着色器
├── Main/                    # 主场景
│   └── MainScene.tscn
├── img/                     # 图片资源
├── Art/                     # 美术资源
├── project.godot            # 项目配置
└── export_presets.cfg       # 导出配置
```

---

## 核心系统详解

### 1. 全局系统 (Autoload)

#### Global (global.gd)
- 预加载常用场景
- 存储全局常量
- 提供场景节点路径访问

```gdscript
const TOOL_TIP = preload("res://UI/tool_tip.tscn")
const FALL_OBJECT_COMPONENT = preload("res://FallObjects/fall_object_component.tscn")

var root_scene:Dictionary = {
    "main_scene": "/root/MainScene",
    "main_canvas_layer": "/root/MainScene/MainCanvasLayer",
    "pop_up": "/root/MainScene/MainCanvasLayer/PopUp",
    "levels": "/root/MainScene/Levels",
    "drops": "/root/MainScene/Levels/Drops",
    "ui_manager": "/root/MainScene/MainCanvasLayer/UIManager",
}
```

#### SceneManager (scene_manager.gd)
- 场景加载和切换
- 管理多场景 (Farm, Town, MyHouse, Store)
- 玩家出生点设置

```gdscript
var level_scenes:Dictionary = {
    "Farm": "res://Map/Farm/farm.tscn",
    "MyHouse": "res://Map/MyHouse/my_house.tscn",
    "Town": "res://Map/Town/town.tscn",
    "Store": "res://Map/Store/store.tscn",
}

func change_level(target_level:String, spawn_pos_name:String) -> void
func set_spawn_position(spawn_pos_name:String) -> void
```

#### TimeSystem (time_system.gd)
- 游戏内时间系统
- 现实1秒 = 游戏1分钟
- 现实24分 = 游戏1天

```gdscript
const MINUTES_PER_DAY: int = 24 * 60
const GAME_MINUTE_DURATION: float = TAU / MINUTES_PER_DAY

signal time_tick(day:int, hour:int, minute:int, week)
signal time_tick_day(day:int)
```

#### AudioManager
- 背景音乐播放
- 音效管理

#### SaveManager
- 游戏存档管理

---

### 2. 玩家系统 (Player)

#### Player (player.gd)
- 继承自 `CharacterBody2D`
- 组件化设计

**主要组件:**
- `HitComponent` - 碰撞检测
- `PlaceComponent` - 物品放置
- `StateMachine` - 状态机
- `Weapon` - 武器精灵
- `Effects` - 特效动画

**属性:**
```gdscript
@export var bag_system:InventorySystem
@export var current_item:Item
@export var current_item_type:Item.ItemType
```

**输入:**
- WASD / 方向键: 移动
- E: 打开背包
- Q: 丢弃物品
- 鼠标左键: 使用物品/攻击
- 鼠标滚轮: 切换物品
- Esc: 菜单

---

### 3. 物品系统

#### Item (item.gd)
- 继承自 `Resource`
- 定义所有物品类型

```gdscript
enum ItemType {
    None,
    Tool,        # 工具
    Hoe,         # 锄头
    Axe,         # 斧头
    Draft,       # 稿子
    Water,       # 水壶
    Weapon,      # 武器
    Consume,     # 消耗品
    Placeables,  # 可放置物品
    Crops,       # 作物
    Materials,   # 材料
    Accessories, # 饰品
    Floors,      # 地板
}

enum UseType {
    None,
    Swing,  # 挥舞
    Hold,   # 手持
}

enum DamageType {
    None,
    Melee,   # 近战
    Magic,   # 魔法
    Summon,  # 召唤
    Shoot,   # 远程
}
```

**物品属性:**
```gdscript
@export var name:String
@export var type:ItemType
@export var quantity:int = 1
@export var max_quantity:int = 999
@export var texture:Texture
@export var price:int = 1
@export var damage:int
@export var collision_size:Vector2
@export var placeable_scene_path:String
```

#### InventorySystem (inventory_system.gd)
- 背包数据管理
- 物品堆叠、交换、添加、移除

```gdscript
@export var items:Array[Item]
@export var items_size:int = 36

func swap_item(target_index:int) -> void
func add_item(new_item:Item) -> void
func remove_item(index:int) -> void
```

---

### 4. 地图系统

#### 场景结构
- **Farm (农场)**: 主场景，包含农田、建筑物
- **Town (城镇)**: 村庄区域
- **MyHouse (玩家房屋)**: 室内场景
- **Store (商店)**: 购买物品

#### TileMap系统
- 使用 Godot TileMapLayer
- 室外图块集: `tile_set_outdoor.tres`
- 室内图块集: `tile_set_indoor.tres`
- 图块大小: 16x16 像素

#### 场景切换
- `ChangeSceneArea` - 区域触发器
- 玩家进入区域自动切换场景

---

### 5. NPC系统

#### NPC基类
- 对话系统
- 好感度 (预留)
- 动画状态

#### 示例NPC
- `Emily` - 村民NPC
- `Pig` - 动物NPC

---

### 6. 作物系统 (Placeables/Crops)

#### 生长系统
- 种植 → 发芽 → 生长 → 成熟
- 季节影响
- 浇水系统

---

### 7. UI系统

#### 界面组件
- **开始菜单** (game_start_menu.tscn)
- **背包UI** (bag_ui.tscn)
- **工具栏** (tool_bar.tscn)
- **物品提示** (tool_tip.tscn)
- **商店面板** (store_panel.tscn)

---

## 物理层 (Physics Layers)

```gdscript
2d_physics/layer_1 = "Player"
2d_physics/layer_2 = "Objects"
2d_physics/layer_3 = "Tools"
2d_physics/layer_4 = "Placeable"
2d_physics/layer_5 = "Terrain"
2d_physics/layer_6 = "Wall"
2d_physics/layer_7 = "ChangeSceneArea"
```

---

## 输入映射

| 输入名称 | 按键 |
|---------|------|
| move_up | W / ↑ |
| move_down | S / ↓ |
| move_left | A / ← |
| move_right | D / → |
| open_bag | E |
| drop | Q |
| Esc | Esc |
| mouse_left | 鼠标左键 |
| mouse_right | 鼠标右键 |
| scroll_up | 鼠标滚轮上 |
| scroll_down | 鼠标滚轮下 |

---

## 游戏循环

1. **启动** → 加载主场景 (MainScene.tscn)
2. **主循环**:
   - 处理输入
   - 更新时间系统
   - 处理玩家移动和交互
   - 更新UI
   - 渲染画面

---

## 存档系统

- 自动存档
- 存储玩家物品、位置、时间

---

## 快速开始 (复刻指南)

### 1. 创建项目
```bash
# 使用 Godot 4.6 创建新项目
# 选择 "GL Compatibility" 渲染器
# 设置分辨率为 1280x720
```

### 2. 核心文件结构
创建以下文件夹:
- Global/
- Player/
- Bag/scene/
- Map/Farm/
- Map/Town/
- Map/MyHouse/
- Map/Store/
- NPC/
- Terrain/
- Placeables/
- UI/
- TimeSystem/
- AudioSystem/
- SaveSystem/
- Component/
- Weapons/
- FallObjects/
- Shader/
- Main/

### 3. 配置 Autoload
在 project.godot 中设置:
```
[autoload]
Global="*res://Global/global.gd"
SceneManager="*res://Global/scene_manager.tscn"
AudioManager="*res://Global/audio_manager.gd"
TimeSystem="*res://TimeSystem/time_system.gd"
SaveManager="*res://Global/save_manager.gd"
MouseCursor="*res://Global/mouse_cursor.tscn"
MouseItem="*res://Global/mouse_item.gd"
```

### 4. 实现步骤

**步骤1: 基础玩家**
1. 创建 Player 场景 (CharacterBody2D)
2. 添加碰撞形状 (CollisionShape2D)
3. 添加 Sprite2D
4. 实现移动逻辑
5. 连接到输入映射

**步骤2: 物品系统**
1. 创建 Item.gd (Resource)
2. 创建 InventorySystem.gd
3. 创建 Bag 场景和 UI

**步骤3: 地图系统**
1. 创建 TileSet (16x16 像素图块)
2. 创建 Farm 场景
3. 添加 TileMapLayer
4. 实现场景切换

**步骤4: 时间系统**
1. 创建 TimeSystem.gd
2. 实现时间计算逻辑
3. 连接信号到其他系统

**步骤5: NPC和互动**
1. 创建 NPC 场景
2. 添加对话系统
3. 实现交互逻辑

---

## 注意事项

1. **GDScript 2.0**: 使用 Godot 4.x 语法 (Typed, @export, @onready)
2. **图块大小**: 16x16 像素
3. **组件化**: 使用组件模式扩展功能
4. **信号系统**: 大量使用 signals 进行解耦

---

## 项目依赖

- Godot 4.6+
- Godot 4.6 GL Compatibility 模板

---

*本文档由AI生成，基于 Stardew 项目源代码分析*
