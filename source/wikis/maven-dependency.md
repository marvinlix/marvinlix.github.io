---
title: maven-dependency
date: 2017-01-05 11:40:27
---

# Maven的依赖规则

## 依赖类型

Maven的依赖类型有五种，分别是：compile，test，provided，runtime，system。

* compile（默认使用）：编译依赖范围，编译、测试、运行都生效；
* test：测试依赖范围，只在测试的classpath有效，比如我们常用的JUnit；
* provided：只对编译和测试有效，对运行无效，常用于容器提供了的运行环境；
* runtime：运行时依赖范围，比如JDBC驱动，编译和测试时不需要，只需要使用JDK提供的JDBC接口；
* system：系统依赖范围，依赖Maven仓库以外的依赖。

## 依赖传递规则

平时引了新包之后会经常遇见报错缺少某个类的情况，一般就是包所依赖的jar包与之前的冲突了。这个就是由于maven的依赖传递引起的，依赖传递遵循如下规则。

### 传递性依赖范围

假设A依赖B，B依赖C，那么A对于B就是第一直接依赖，B对于C就是第二直接依赖，A对于C是传递性间接依赖，而传递性依赖的范围是由第一直接依赖和第二直接依赖的范围决定的。
如图，左一列表示第一直接依赖范围，上一列是第二直接依赖范围，中间交叉单元格表示传递性依赖范围。

|| compile	| test	| provided| 	runtime|
|--|--|--|--|--|
|compile	|compile	|-|	-	|runtime|
|test|	test|	-|	-	|test|
|provided	|provided|	-|	provided	|provided|
|runtime	| runtime	|-	|-	|runtime|

### 依赖仲裁的原则

#### 第一原则：路径短优先原则

  * 路径1是：A->B->C->D(version1.0)
  * 路径2是：A->E->D(version2.0)

由于路径2的长度小于路径1，所以结果就是A会传递性依赖D(version2.0)

#### 第二原则：先声明者优先原则 （maven版本>=2.1.0有效）

当路径长度相同时候，就要使用第二原则。

  * 路径1不变依然是：A->B->C->D(version1.0)
  * 路径2是：A->E->F->D(version2.0)

由于路径1在路径2前面声明，所以结果就是A会传递性依赖D(version1.0)
