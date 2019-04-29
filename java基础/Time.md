#### 1.System.currentTimeMillis()

```java
long System.currentTimeMillis();//since January 1st 1970 in milliseconds//
```

例子:

```java
class Timer{
    private long startTime;
    private long endTime;
    public void start(){
        startTime = System.currentTimeMillis();
    }
    public void end(){
        endTime = System.currentTimeMillis();
    }
    public long getEndTime(){
        return endTime;
    }
    public long getStartTime(){
        return startTime;
    }
    public long getTotalTime(){
        return endTime - startTime;
    }
}
```

#### 2.java.util.Date

##### 1.创建

```java
Date date = new Date();//当前时间
long getTime();//返回时间
java.util.Date date = new java.util.Date(System.currentTimeMillis());
//其他创建形式不推荐使用
```

##### 2.时间比较

```java
int compareTo(Date anotherDate);
boolean before(Date when);
boolean after(Date when);
```

**java.sql.Date继承自java.util.Date**

**java.sql.TimeStamp继承自java.util.Date**

#### 3.Calendar

##### 1.创建

```java
Calendar calendar = Calendar.getInstance();
Calendar calendar1 = new GregorianCalendar();
```

##### 2.时间的获取和修改

```java
int get(int field);//获取field对应值
void add(int field,int amount)//增加field对应值
void set(int field,int value0;//设置field对应的值
field:public static final int
Calendar.YEAR
Calendar.MONTH //月份从0开始
Calendar.DAY_OF_MONTH
Calendar.DAY_OF_WEEK
Calendar.WEEK_OF_YEAR
Calendar.WEEK_OF_MONTH
Calendar.HOUR   // 12 hour clock
Calendar.HOUR_OF_DAY // 24 hour clock
Calendar.MINUTE
Calendar.SECOND
Calendar.MILLISECOND
```

#### 4.SimpleDateFormat

```java
 SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateStr = format.format(new Date());
        System.out.println(dateStr);
        Date date = format.parse("2020-10-01 20:59:00");
```

