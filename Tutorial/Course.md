## 物理引擎的重心与图片中心
![center_of_gravity](https://user-images.githubusercontent.com/34769581/110727279-e3c37280-8255-11eb-9a9f-c6c110f66ae3.png)

## 坐标系差异

![coordinate_system](https://user-images.githubusercontent.com/34769581/110732728-acf25a00-825f-11eb-98d0-1f9c90312c58.png)

屏幕坐标系，通常是以左上角为坐标原点(0,0)

![矫正坐标前_1](https://user-images.githubusercontent.com/34769581/110744484-b89c4b80-8274-11eb-9d2a-3ecfeb53d7cf.gif)

![混合显示](https://user-images.githubusercontent.com/34769581/110744511-c356e080-8274-11eb-946b-12c7a0723759.gif)

![矫正坐标](https://user-images.githubusercontent.com/34769581/110744519-c782fe00-8274-11eb-9693-787d0a3a6476.gif)


## 凹多边形
对于突多边形的碰撞检测，只需遍历每个多边形的边，两两进行碰撞判断即可，运算复杂度较低。但对于凹多边形，碰撞检测的运算量和复杂度迅速提升，不宜直接进行运算，因此需要把一个凹多边形，拆分成若干个突多边形的组合，然后再运用突多边形的算法进行碰撞检测。

Matter.js提供了凹多边形处理模块，我们可以传入一个凹多边形的点数组，由Matter.js进行预处理，自动拆分成若干个凸多边形。但对于一些复杂物体，我们也可以运用物理编辑器进行精细的编辑，确保拆分的凸多边形模块符合物体的实际形状描述，如PhysicsEditor。

![poly](https://user-images.githubusercontent.com/34769581/110730938-5c2d3200-825c-11eb-9b05-225b7b594dd3.png)

上图左边是一个凸多边形，凸多边形的特点就是，每个内角均不超过180度。右图是凸多边形，凸多边形有至少一个内角大于180度。右图拆分成3个部分之后，每个子部分就都是一个凸多边形。


## 选修课2:排行榜制作

排行榜是游戏中的一个重要元素，有了排行榜，玩家们参与游戏的热情就会迅速提升。这节课就讲一下如何制作一个可以承载一定访问量的高性能排行榜。

排行榜从形式上看，就是一个按分数高低进行排序的数据集，如果不考虑排行榜的性能，那么用关系型数据库，把所有游戏分数记录下来，然后用order by进行一次排序就可以了。这样的排行榜性能是很低的，如果游戏参与者增加，那么排行榜的运算量将呈指数增长，这不是我们要的排行榜。

一个成熟的排行榜，首先需要做数据缓存。假设我们有10w人在1秒钟之内同时访问排行榜，如果这么短时间内执行10w次排序操作，数据库就挂掉了，所以我们不能简单粗暴的每次查询都进行一次排序，而只需以一定时间间隔，执行一次排序并写入缓存，查询的时候从缓存取出数据即可。但是时间间隔的设置要合理，间隔太短，排序的频率又上来了，间隔太长，那么用户看到的排行榜就不够实时了。


### 缓存方案:
Mysql + Redis
排序逻辑主要用Redis的有序集合来实现。Redis有序集合，可以将score-member形式的数据写入，然后自动构建排序索引，不需要再单独的写排序逻辑。

写入数据用`redis->zAdd(key,score,member)`

```php
$redis->zAdd($key,$score,$member)
```



### 服务器方案:
CentOS/Nginx
### 实例代码:
