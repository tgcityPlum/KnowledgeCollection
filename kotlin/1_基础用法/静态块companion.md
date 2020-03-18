# 静态块companion

## 1 简介
在kotlin中，同样是有静态块，较之java的不同，kotlin的写法比较独立

## 2 用法
常见的写法如下
```
companion object{
  //静态变量
  private var s : String

  /**
  *静态方法
  */
  fun getInstance(){
    //todo ...
  }
}
```
