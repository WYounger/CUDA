##### 1.classpath

classpath:src路径下的文件在编译后就会放在`项目名/WEB-INF/classes/`目录下,默认的classpath是在这里。

用maven构建项目时resources目录就是默认的classpath



##### 2.classpath和classpath*的区别

classapth:只会从第一个classpath加载文件

classpath:会在所有的classpath中加载(eg:jar文件)文件



**参考资料**

[java项目里classpath具体指哪儿个路径](https://blog.csdn.net/u011095110/article/details/76152952)

[Spring加载resource时classpath*:与classpath:的区别](https://blog.csdn.net/kkdelta/article/details/5507799)

[classpath:与classpath*:详细解析](https://blog.csdn.net/qq_36501591/article/details/80087984)

