## 创建型操作符

1.create  产生一个ObserverbalEmitter，手动发射

2.just  内部发射

3.fromArray  内部发射，数集对象

4.empty  不发射有值事件，只发射onComplete

5.range  内部发射，从 start 累加 count个

6.interval 



## 变换型符

1.map  把一种类型转为另外一种类型

2.flatmap  把一种类型转为另一种可以多次发射的类型 ObservableSource , 但是不保证顺序

3.contactmap 把一种类型转为另一种可以多次发射的类型 ObservableSource , 可保证顺序

4.groupby  把上游数据分组成一个被观察者 GroupedObservable

5.buffer 很多数据不想一次性发射出去，先缓存一部分



## 过滤型操作符

1.filter

2.take

3.

