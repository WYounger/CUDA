##### Set

- addAll(Collection c) 并集
- retainAll(Collectioon c)  交集
- removeAll(Collection c) 差集

```java
  @Test
    public void 集合运算(){
        Integer[] a = {1,2,3,4};
        Integer[] b = {1,2,3,5};

        Set<Integer> set_a = new HashSet<>(Arrays.asList(a));
        Set<Integer> set_b = new HashSet<>(Arrays.asList(b));

        //并集
//        set_a.addAll(list_b); // 1 2 3 4 5

        //交集
//        set_a.retainAll(list_b); // 1 2 3


        //差集 A - B = A - AB
        set_a.removeAll(set_b); // 4

        set_a.forEach(System.out::println);
    }

```

