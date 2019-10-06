##### 1读取一个字符文件的行数

常规做法

```java
@Test
    public void test() throws IOException {
        File file = new File("test.txt");
        int count = 0;
        try(BufferedReader bufferedReader = new BufferedReader(new FileReader(file))){
            while (bufferedReader.readLine() != null){
                count++;
            }
            System.out.println("count: "+count);
        }
    }
```

利用Java API

```java
 @Test
    public void test() throws IOException {
        File file = new File("C:\\Users\\Young\\Desktop\\test.txt");
        try(LineNumberReader lineNumberReader = new LineNumberReader(new FileReader(file))){
            lineNumberReader.skip(file.length());
            //getLineNumber()返回从0~n-1
            int count = lineNumberReader.getLineNumber() + 1;
            System.out.println("count: "+count);
        }
    }
```

性能比较:第二种较好

##### 2.遍历Map

```java
@Test
    public void test() throws IOException {
        Map<String,Object> map = new HashMap<>(10);
        final int INDEX = 10;
        for(int i = 1; i < INDEX; i ++){
            map.put(i+"",i);
        }
        //遍历Map
        for(Map.Entry<String,Object> entry : map.entrySet()){
            System.out.println("k-v: "+entry.getKey() + ": " +entry.getValue());
        }
        //遍历key
        for (String k : map.keySet()){
            System.out.println("K: "+k);
        }
    }
```

##### 3.数组转List

```java
@Test
    public void test() throws IOException {
       String[] strArray = {"1","2","3"};
       List<String> strList = Arrays.asList(strArray);
       System.out.println(strList);
    }
```

##### 4.将基本数据型、字符数组和对象转换为String类型

```java
String str  = String.valueOf(data)
```

##### 5.优先使用常量和确定值或使用Object.equals()来调用equals方法

```java
"abc".equals(str);

//Objects.equals(Object o1,Object o2);
public static boolean equals(Object a, Object b) {
        return (a == b) || (a != null && a.equals(b));
    }
```



