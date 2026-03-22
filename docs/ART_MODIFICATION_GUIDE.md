# 星露谷风格游戏 - 美术资源修改手册

> 本手册记录如何修改游戏中的各种美术资源

---

## 📁 资源目录结构

```
Art/
├── character/          # 角色相关
│   └── farmer/
│       ├── farmer_base.png   # 角色身体 spritesheet
│       └── shirts.png        # 衣服 spritesheet
├── maps/              # 地图瓦片
│   ├── spring_outdoorsTileSheet.zh-CN.png  # 室外地形
│   ├── walls_and_floors.png               # 墙壁地板
│   └── townInterior.png                   # 室内地图
├── tile_sheets/       # 物品/工具/武器 spritesheet
│   ├── weapons.png    # 武器
│   ├── tools.png     # 工具
│   ├── crops.png     # 农作物
│   └── animations.png # 动画特效
├── terrain/          # 地形元素(树木/草地)
│   ├── tree1_spring.png
│   └── grass.png
├── buildings/        # 建筑
└── loose_sprite/     # 零散素材(阴影/粒子)
```

---

## ⚔️ 一、新增武器

### 步骤 1: 制作 16x16 像素图标

工具推荐: Aseprite, Paint.NET, Python PIL

### 步骤 2: 合并到 weapons.png

1. 打开 `Art/tile_sheets/weapons.png`
2. 找到空位(每行9个, 共8行)
3. 放入新图标, 记录位置坐标

**坐标计算**:
```
位置 = (列号 × 16, 行号 × 16)
例如: 第9行第2列 = (16, 128)
```

### 步骤 3: 创建武器配置

在 `Bag/items/weapon/` 下创建 `.tres` 文件:

```gdresource
[gd_resource type="Resource" load_steps=4 format=3]

[ext_resource type="Script" path="res://Bag/scene/item.gd" id="1"]
[ext_resource type="Texture2D" path="res://Art/tile_sheets/weapons.png" id="2"]

[sub_resource type="AtlasTexture" id="1"]
atlas = ExtResource("2")
region = Rect2(X坐标, Y坐标, 16, 16)  # ← 修改这里

[resource]
script = ExtResource("1")
name = "武器名"
type = 6  # Weapon类型
description = "描述"
texture = SubResource("1")
damage = 10
crit = 0.1
```

### 步骤 4: 添加到玩家背包

修改 `Player/player.tscn`:

1. 在 `[ext_resource]` 部分添加新武器引用
2. 在 `items = Array[...]` 中添加 `ExtResource("新ID")`

---

## 👤 二、修改角色外观

### 角色身体 (farmer_base.png)

- **位置**: `Art/character/farmer/farmer_base.png`
- **尺寸**: 16×16 每帧
- **结构**: 18列 × 21行 spritesheet
- **动画帧**:
  - 行0-5: 待机/移动动画
  - 行6-11: 工具使用动画
  - ...

### 衣服系统 (shirts.png)

- **位置**: `Art/character/farmer/shirts.png`
- **尺寸**: 16×16 每件
- **结构**: 32列(不同款式) × 76行(不同颜色)

### 修改流程

1. 直接编辑 `farmer_base.png` 或 `shirts.png`
2. 重启 Godot 项目刷新资源

---

## 🌲 三、新增地形/植被

### 树木资源

位置: `Art/terrain/tree*.png`

每种树有4个季节版本:
- `tree1_spring.png` - 春季
- `tree1_summer.png` - 夏季  
- `tree1_fall.png` - 秋季
- `tree1_winter.png` - 冬季

### 地形瓦片

位置: `Art/maps/spring_outdoorsTileSheet.zh-CN.png`

**修改地图**:
1. 在 Godot 中打开对应场景 (如 `Map/Farm/farm.tscn`)
2. 选择 TileMap 节点
3. 在 TileSet 编辑器中修改

### 新增植被步骤

1. 制作 16×16 像素素材
2. 放入对应季节的 `tree*.png`
3. 在 TileSet 编辑器中注册新图块
4. 设置碰撞体(可选)

---

## 🏠 四、新增建筑

### 资源位置

- 外部建筑: `Art/maps/` 下的 TileSheet
- 内部建筑: `Art/buildings/`

### 添加新建筑

1. 制作建筑spritesheet(包含多角度)
2. 在 Godot 场景中:
   - 创建 StaticBody2D 节点
   - 添加 Sprite2D 子节点
   - 添加 CollisionShape2D

---

## 🌾 五、新增农作物

### 资源位置

`Art/tile_sheets/crops.png`

### 结构

每个作物有多个生长阶段(横向排列)

### 配置步骤

1. 在 `Bag/items/crops/` 创建作物配置
2. 设置 `type = 9` (Crops)
3. 配置生长时间、产出物品等

---

## 🎨 六、通用规范

### 像素画尺寸

| 类型 | 尺寸 |
|------|------|
| 角色/武器/工具 | 16×16 |
| 建筑 | 16×16 或 32×32 |
| 地形瓦片 | 16×16 |
| 农作物 | 16×16 |

### 调色板

- 推荐使用星露谷风格: 16-32色
- 推荐网站: https://lospec.com/palette-list

### 导出设置

- 格式: PNG (带透明度)
- 过滤: Nearest Neighbor (不要模糊)

### Godot 配置

确保 project.godot 中:
```gdscript
[rendering]
textures/canvas_textures/default_texture_filter=0  # 像素风
```

---

## 🔧 常见问题

**Q: 图片不显示?**
A: 检查 `region = Rect2(x, y, 16, 16)` 坐标是否正确

**Q: 武器太大/位置不对?**
A: 确认是 16×16 尺寸, 放入正确的 spritesheet

**Q: 四季切换不生效?**
A: 确认各季节的 tree*.png 都已更新

---

*最后更新: 2026-03-22*
