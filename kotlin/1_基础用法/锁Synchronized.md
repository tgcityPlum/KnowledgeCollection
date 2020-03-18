# 锁Synchronized

## 简介
kotlin使用Synchronized来对对象进行存储

## 用法

Synchronized可以作为注解的形式放在方法上
```
class Manager {

    companion object {
        private var instance: Manager? = null

        @Synchronized
        fun instance(): Manager {
            if (instance == null) {
                instance = Manager()
            }
            return instance!!
        }
    }
}
```
