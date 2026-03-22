# 核心系统架构

## 系统架构图

```
┌─────────────────────────────────────────────────────┐
│                    Main Scene                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ Levels   │  │ UI Layer │  │ Global Scripts   │  │
│  │ (地图)    │  │ (界面)    │  │ (全局管理)        │  │
│  └──────────┘  └──────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────┘
         │              │               │
         ▼              ▼               ▼
    ┌─────────┐   ┌─────────┐    ┌─────────────┐
    │ Player  │   │  Bag    │    │ TimeSystem  │
    │ 玩家角色 │   │ 背包系统 │    │ 时间系统    │
    └─────────┘   └─────────┘    └─────────────┘
```

---

## 1. 全局系统 (Global/)

| 文件 | 功能 |
|------|------|
| `global.gd` | 常量定义、预加载资源、根节点引用 |
| `scene_manager.gd` | 场景切换管理 |
| `audio_manager.gd` | 音效管理 |
| `save_manager.gd` | 存档管理 |
| `mouse_cursor.gd` | 自定义鼠标 |
| `mouse_item.gd` | 鼠标携带物品 |

### 场景切换流程
```
player.gd → SceneManager.change_scene("Farm") 
         → scene_manager.gd 加载新场景
         → 清理旧场景资源
```

---

## 2. 玩家系统 (Player/)

### 节点结构
```
Player (CharacterBody2D)
├── Shadow (Sprite2D)
├── Body (Sprite2D)         # 身体
├── Hands (Sprite2D)        # 手
├── Clothes (Sprite2D)      # 衣服
├── Tools (Sprite2D)        # 武器/工具
├── Effects (AnimatedSprite2D) # 特效
├── CollisionShape2D
├── HitComponent           # 攻击判定
├── PlaceComponent         # 放置物品
├── Weapon                 # 武器节点
│   ├── JianQi (Line2D)   # 剑气特效
│   └── Weapon (Sprite2D)
└── StateMachine           # 状态机
    ├── Idle
    ├── Move
    ├── Axe
    ├── Hoe
    ├── Water
    └── Swing (攻击)
```

### 状态机模式
使用 Godot 的节点作为状态，每个状态是独立脚本:
- `Idle.gd` - 待机
- `Move.gd` - 移动
- `Axe.gd` - 砍树
- `Hoe.gd` - 锄地
- `Water.gd` - 浇水
- `Swing.gd` - 挥武器

---

## 3. 背包系统 (Bag/)

### 文件结构
```
Bag/
├── scene/
│   ├── inventory_system.gd  # 背包核心逻辑
│   ├── item.gd              # 物品类定义
│   ├── bag_ui.gd            # 背包界面
│   ├── tool_bar.gd          # 快捷工具栏
│   ├── slot.gd              # 物品槽
│   └── ui_manager.gd        # UI管理
└── items/
    ├── weapon/              # 武器配置
    ├── tools/              # 工具配置
    ├── crops/              # 农作物
    ├── seeds/              # 种子
    └── objects/             # 物品
```

### Item 类型枚举 (item.gd)
```gdscript
enum ItemType{
    None,
    Tool,       # 工具
    Hoe,        # 锄头
    Axe,        # 斧头
    Draft,      # 稿子
    Water,      # 水壶
    Weapon,     # 武器
    Consume,    # 消耗品
    Placeables, # 可放置
    Crops,      # 作物
    Materials,  # 材料
    Accessories,# 饰品
    Floors,     # 地板
}
```

---

## 4. 时间系统 (TimeSystem/)

### 功能
- 昼夜循环 (6:00 - 26:00)
- 四季变换 (春/夏/秋/冬)
- 时间流逝控制

### 核心变量
```gdscript
var current_time: int      # 当前时间 (分钟)
var current_day: int       # 第几天
var current_season: int    # 季节 (0-3)
```

---

## 5. 地图系统 (Map/)

### 场景结构
```
Map/
├── Farm/
│   └── farm.gd
├── Town/
│   └── town.gd
├── MyHouse/
│   └── my_house.gd
├── Store/
│   └── store.gd
└── (其他场景)
```

### TileMap 组成
- `TileMapLayer` - 地形层
- `TileMapLayer` - 建筑层
- `TileMapLayer` - 装饰层

---

## 6. 存档系统 (SaveSystem/)

### 存档结构
```gdscript
SaveData
├── player_data      # 玩家数据
│   ├── position    # 位置
│   ├── inventory   # 背包
│   └── stats       # 属性
├── world_data      # 世界数据
│   ├── time        # 时间
│   ├── crops       # 农作物状态
│   └── buildings   # 建筑状态
└── timestamp       # 时间戳
```

---

## 模块间通信

### 信号 (Signals)
```gdscript
# 玩家信号
signal get_item          # 获得物品
signal watering          # 浇水
signal change_scene      # 切换场景

# UI信号
signal update_inventory  # 更新背包
signal update_time       # 时间更新
```

### 全局访问
```gdscript
# 访问玩家
var player = get_tree().get_first_node_in_group("Player")

# 访问全局脚本
var global = Global
var time_sys = TimeSystem
```

---

*最后更新: 2026-03-22*
