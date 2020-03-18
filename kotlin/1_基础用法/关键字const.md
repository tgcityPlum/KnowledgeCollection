# 关键字const

## 简介

在kotlin中，有没有const修饰对用调用时没有任何影响，但是java调用时是有影响的，加上const就相当于*public static final*

## 用法
```
class Person {
    companion object {
        const val sex: Int = 1
        val sex1: Int = 1
    }
}

public class TestPerson {

    public static void main(String[] args) {
        //在没有加const修饰时应该如此调用
        System.out.println(Person.Companion.getSex1());
        //加了const修饰之后的调用
        System.out.println(Person.sex);
    }
}
```
