# 🎮 裂隙旅人 - 项目知识库

> 本文件夹包含项目的完整知识文档，用于快速理解项目架构和开发新功能。

---

## 📚 文档索引

| 文档 | 说明 | 目标读者 |
|------|------|----------|
| **[PROJECT_DOCUMENTATION.md](./PROJECT_DOCUMENTATION.md)** | 完整项目文档，系统架构详解 | 新开发者 |
| **[QUICK_REFERENCE.md](./QUICK_REFERENCE.md)** | 快速参考卡片，最常用操作 | 日常开发 |
| **[ART_MODIFICATION_GUIDE.md](./ART_MODIFICATION_GUIDE.md)** | 美术资源修改手册 | 美术/策划 |

---

## 🗂️ 知识库架构

```
docs/
├── README.md                 # ← 你在这里
├── PROJECT_DOCUMENTATION.md  # 完整项目文档
├── QUICK_REFERENCE.md        # 快速参考
├── ART_MODIFICATION_GUIDE.md # 美术修改手册
├── ARCHITECTURE/            # 架构文档(待创建)
│   ├── CORE_SYSTEMS.md       # 核心系统
│   └── GAME_LOOP.md         # 游戏循环
├── MECHANICS/               # 机制文档(待创建)
│   ├── COMBAT.md            # 战斗系统
│   ├── FARMING.md           # 农耕系统
│   └── INVENTORY.md         # 背包系统
└── TUTORIALS/              # 教程(待创建)
    └── HOW_TO_ADD_WEAPON.md # 如何添加武器
```

---

## 📖 阅读路线

### 1. 快速入门 (5分钟)
```
QUICK_REFERENCE.md → 了解基本操作
```

### 2. 理解架构 (30分钟)
```
PROJECT_DOCUMENTATION.md → 完整项目概览
ARCHITECTURE/CORE_SYSTEMS.md → 核心系统详解
```

### 3. 实践开发 (按需)
```
ART_MODIFICATION_GUIDE.md → 修改美术资源
MECHANICS/COMBAT.md → 理解战斗系统
```

---

## 🔑 核心概念速查

| 概念 | 位置 | 说明 |
|------|------|------|
| 物品系统 | `Bag/scene/item.gd` | 所有物品的基类 |
| 玩家控制 | `Player/player.gd` | 移动、攻击、交互 |
| 场景管理 | `Global/scene_manager.gd` | 场景切换 |
| 时间系统 | `TimeSystem/time_system.gd` | 昼夜/季节 |
| 存档系统 | `SaveSystem/save_data.gd` | 存档/读档 |

---

## 🛠️ 常用操作

### 添加新物品
1. 在对应分类创建 `.tres` 文件
2. 配置物品属性 (名称、类型、属性)
3. 在 `player.tscn` 中添加到背包数组

### 添加新场景
1. 在 `Map/` 下创建场景
2. 在 `Global/scene_manager.gd` 注册
3. 配置传送点

### 添加新武器
详见 `ART_MODIFICATION_GUIDE.md`

---

*本知识库会随着项目发展持续更新*
*最后更新: 2026-03-22*
