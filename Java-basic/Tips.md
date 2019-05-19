##### 1.用奇判断不用偶判断

```java
num % 2 == 0 ? "偶数" : "奇数";
```

`%`运算等价于`num - num/d*d`,所以会出现结果为负数的情况

##### 2.边界检查

当一个数字超过他的最大值，那么会溢出变为负数

所以要检查`0`,`负最小`,`正最大`

##### 3.包装类型要检查`null`

Integer源码分析

```java
    //装箱过程,推荐这个这种方式创建Integer对象
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }

    @Deprecated(since="9")//不推荐直接使用该构造函数来创建Integer对象
    public Integer(int value) {
        this.value = value;
    }

    /**
     * The value of the {@code Integer}.
     *
     * @serial
     */
    private final int value;

    //拆箱过程
    public int intValue() {
        return value;
    }
```

```java
Integer i = 10;
//等价于
Integer i = Integer.valueOf(10);//装箱

int ii = i ;
//等价于
int ii = i.intValue();//拆箱
```

由上可知，当`i`为`null`时，拆箱失败，抛出`NullPointerException`

所以包装类型拆箱之前要检查是否为`null`

