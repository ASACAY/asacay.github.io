---
title: Godot开发指南_吸血鬼幸存者篇
date: 2025-06-13 12:35:55
tags: notes
---

# Godot开发指南

## 关于幸存者框架

### 一.预先准备

> 资产(源)管理assets,利用资源管理所需的素材
>
> 编辑器简化,关闭资产库和3d场景构建(限制引擎的使用场景)
>
> 网络上所需的相关游戏素材(前期建议可以使用公开免费游戏素材)



### 二.人物设计

> 节点:CharacterBody2D(Player) // Sprite2D/CollisionShape2D
>
> 1. 像素人物的模糊原因(项目>项目设置>渲染>纹理>Nearest)
> 2. 参数折叠取消(编辑>编辑器设置>Disable Folding)

- #### 脚本设计

1.在CharacterBody2D(Player)下选择创建脚本,选择Node模板

```GDS
extends Node2D


# Called when the node enters the scene tree for the first time.
# 当节点第一次进入场景树时，会调用_ready()函数!
func _ready() -> void:
	pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
# 称为每一帧。'delta'是自上一帧以来经过的时间。
func _process(delta: float) -> void:
	pass

# 称为物理帧,在主循环的物理处理步骤中调用。物理处理的帧率与物理同步，即 delta 参数通常不变（例外见下文）。delta 的单位为秒。启用物理处理后才会调用该方法
func _physics_process(delta: float) -> void:
    pass
```

> 项目>项目设置>按键映射(单例) 在其中自定义按键的映射属性(项目中有其本身的项目映射)
>
> 创建关于人物移动的映射属性(上/下/左/右)

2.创建人物的位置矢量

```GDS
extends CharacterBody2D

const MAX_SPEED=200
# Called when the node enters the scene tree for the first time.
func _ready() -> void:
	pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	var movement=palyer_movement().normalized() #矢量单位化

	
func palyer_movement():
	var x_movement=Input.get_action_strength("ui_right") - Input.get_action_strength("ui_left")
	var y_movement=Input.get_action_strength("ui_down") - Input.get_action_strength("ui_up")
    #根据刚才的映射找到人物运动的每个方向上的速度
	return Vector2(x_movement , y_movement)

```

3.修改窗口的长宽高,并**更改Stretch(拉伸)下的Mode(模式)属性更改为"viewport"**

{% gallery %}

![]([window_setting.png (1204×747)](https://raw.githubusercontent.com/ASACAY/asacay.github.io/refs/heads/main/photos/window_setting.png)

![image-20250615155756347](C:\Users\luhaoye\Yezzi_Godot\photos\编辑器和资产器关闭.png)

{% endgallery %}

4.设置一个Node节点,定义为场景主节点,纳入到场景树.并实例化场景Player

**效果如下(点击main节点下的F6进行预览)**

![image-20250615160758597](C:\Users\luhaoye\Yezzi_Godot\photos\场景预览一.png)

### 三.瓦片地图设计

> 场景树Main中添加TileMap节点,如下图所示,然后将该节点移到Player上方,场景在节点树上的位置就是其绘制顺序,越靠近父节点则越先一步进行绘制.

![image-20250615161107321](C:\Users\luhaoye\Yezzi_Godot\photos\瓦片地图场景树.png)

1. 创建一个resource文件夹,右键选择新>资源,搜索TileSet,保存为tres文件.

2. 抓取瓦片地图集体,选择tilemap_packed的文件

3. 为此创建一个文件夹,名为environment,将刚才的tilemap_packed文件放入到tileset.tres中进行预览,选择不自动创建瓦片.

4. 选择右侧检查器中的Terrain Sets(自动平铺)属性,新建一个地图集命名为floor,选择想选择的地块.

5. 选择资源管理器中的绘制属性,绘制子属性选择地形,选择刚才创建的地图集,地图集掩码相关(表示其中可以生成的位置,此后详谈).绘制属性重新选择为概率,将选择的地块分配概率.

6. 将创建好的tileset.tres放入到tilemap节点中.

7. 选择tilemap节点的绘制功能,自动绘制地形如下所示

   ![image-20250615163418782](C:\Users\luhaoye\Yezzi_Godot\photos\瓦片地图效果一览.png)

### 四.摄像机设置

{% gallery %}

![](C:\Users\luhaoye\Desktop\fee2593039010da42532cd75ba753a94.jpg)

{% endgallery %}
