| | 图例 | 语义 | 含义 |
|--|--|--|--|
|依赖(Dependency)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Dependency.png">|A use B|1. B作为A的函数参数 <br> 2. B的成员作为A的函数参数 <br> 3. A的函数中创建了B <br> 4. A的函数返回B|
|关联(Association)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Association.png">|A has B的指针/引用/副本|A和B是两个相对独立的对象，有一定的对应关系|
|聚合(Aggregation)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Aggregation.png">|A has B的指针|B是A的一部分，B可以独立于A单独存在，整体部分不负责局部对象的销毁，二者的生命周期没有关联|
|组合(Composition)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Composition.png">|A has B的副本|B是A的一部分，B不能独立于A单独存在，整体部分负责局部对象的销毁|
|继承(Generalization)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Generalization.png">|B is A|B继承于A，B是A的派生类 |
|接口实现(Realization)|<img src="https://maopeilin1986.github.io/assets/images/20201010/Realization.png">|B is A|B实现A的接口|
