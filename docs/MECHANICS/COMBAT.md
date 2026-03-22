# 战斗系统

## 武器系统

### 武器类型
在 `Bag/scene/item.gd` 中定义:
```gdscript
enum WeaponType {
    Melee,   # 近战
    Magic,   # 魔法
    Summon,  # 召唤
    Shoot    # 远程
}
```

### 武器属性
```gdscript
damage      # 伤害
crit        # 暴击率 (0.0-1.0)
knockback   # 击退力
open_trail  # 是否显示武器轨迹
open_texture# 是否显示武器贴图
```

### 攻击流程
```
1. 玩家按下鼠标左键
2. player.gd 检测当前手持武器类型
3. 切换到 Swing 状态
4. 播放挥武器动画
5. HitComponent 检测碰撞
6. 造成伤害
```

---

## 伤害系统

### HitComponent
位置: `Component/hit_component.gd`

功能: 武器攻击判定区域

### HurtComponent  
位置: `Component/hurt_component.gd`

功能: 承受伤害

### 伤害计算
```gdscript
func take_damage(damage: int):
    var actual_damage = damage
    # 暴击判定
    if randf() < crit:
        actual_damage *= 2
    health -= actual_damage
```

---

## 攻击动画

### 动画定义
在 `Player/player.tscn` 中:
- `swing_down` - 向下挥
- `swing_left` - 向左挥
- `swing_right` - 向右挥
- `swing_up` - 向上挥

### 动画播放
```gdscript
# 根据方向播放
var anim_name = "swing_" + direction
anim.play(anim_name)
```

---

## 武器特效

### 剑气 (JianQi)
位置: `Weapons/jian_qi.gd`

使用 Line2D 实现拖尾效果:
```gdscript
var jian_qi: Line2D
jian_qi.add_point(start_position)
jian_qi.add_point(end_position)
```

### 粒子特效
使用 AnimatedSprite2D:
- 位置: `Player/Effects`
- 动画: tool_down, tool_up, water 等

---

## 添加新武器步骤

1. 制作 16×16 图标
2. 合并到 `Art/tile_sheets/weapons.png`
3. 创建 `Bag/items/weapon/新武器.tres`
4. 配置属性 (damage, crit, knockback)
5. 在 `Player/player.tscn` 添加到背包数组

---

*最后更新: 2026-03-22*
