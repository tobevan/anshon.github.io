# 泛型

### 1.super(下限)、extends(上限)

Class person

​		--worker

​		--student

​			--senorStudent

​			--majorStudent

void Show(T extends student)   限制上限，最高到student

void say(T super student)  限制下限，最低只能是student

