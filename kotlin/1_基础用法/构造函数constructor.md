# 构造函数constructor

## 简介
constructor是用来创建构造函数，常见的方式有*primary constructor*和*secondary constructor*

## 使用方式
### 1 primary constructor
* 通用写法```class A constructor(val s: String)```
* 当没有注解时使用缩写```class A(val s: String)```  
文件中可以直接使用s,也可以在init{}进行赋值

### 2 primary constructor
* 内部结构体的创建  
```
class A(val s:String){

    var s: String

    constructor(s:String):this(s){

    }
}
```
