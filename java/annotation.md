## annotation 注解
注解：提供一种为程序元素设置元数据的方法
基本原则：注解不能直接干扰程序代码的运行，无论增加或删除注解，代码都能够正常运行。
简单来说注解的作用就是将我们的需要的数据储存起来，在以后的某一个时刻（可能是编译时，也可能是运行时）去调用它。

## 元数据
元数据：就是关于数据的数据
元数据的作用：
编写文档：通过代码里标识的元数据生成文档；
代码分析：通过代码里标识的元数据对代码进行分析；
编译检查：通过代码里标识的元数据让编译器能实现基本的编译检查。

## 注解的功能
作为特定的标记，用于告诉编译器一些信息
编译时动态处理，如动态生成代码
运行时动态处理，作为额外信息的载体，如得到注解信息

## 注解的分类
标准注解：Override、Deprecated、SuppressWarnings

元注解：@Retention、@Target、@Inherited、@Documented

自定义注解

## 元注解

@Target
说明了Annotation所修饰的对象范围：Annotation可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）

CONSTRUCTOR:用于描述构造器
FIELD:用于描述域
LOCAL_VARIABLE:用于描述局部变量
METHOD:用于描述方法
PACKAGE:用于描述包
PARAMETER:用于描述参数
TYPE:用于描述类、接口(包括注解类型) 或enum声明

@Retntion
定义了该Annotation被保留的时间长短：某些Annotation仅出现在源代码中，而被编译器丢弃；而另一些却被编译在class文件中；编译在class文件中的Annotation可能会被虚拟机忽略，而另一些在class被装载时将被读取（请注意并不影响class的执行，因为Annotation与class在使用上是被分离的）。使用这个meta-Annotation可以对 Annotation的“生命周期”限制。

取值（RetentionPoicy）有：

SOURCE:在源文件中有效（即源文件保留）
CLASS:在class文件中有效（即class保留）
RUNTIME:在运行时有效（即运行时保留）

@Documented的源码,

@Inherited, 阐述了某个被标注的类型是被继承的


