**JSP总结**

##### **1.什么是jsp**

　　JSP全称是Java Server Pages，它和servle技术一样，都是SUN公司定义的一种用于开发动态web资源的技术。

　　JSP这门技术的最大的特点在于，写jsp就像在写html，但它相比html而言，html只能为用户提供静态数据，而Jsp技术允许在页面中嵌套java代码，为用户提供动态数据。

##### **2.jsp原理**

1. **Web服务器是如何调用并执行一个jsp页面的？**

　　浏览器向服务器发请求，不管访问的是什么资源，其实都是在访问Servlet，所以当访问一个jsp页面时，其实也是在访问一个Servlet，服务器在执行jsp的时候，首先把jsp翻译成一个Servlet，所以我们访问jsp时，其实不是在访问jsp，而是在访问jsp翻译过后的那个Servlet，当我们通过浏览器访问index.jsp时，服务器首先将index.jsp翻译成一个index_jsp.class，在Tomcat服务器的work\Catalina\localhost\项目名\org\apache\jsp目录下可以看到index_jsp.class的源代码文件index_jsp.java，我们可以看到，index_jsp这个类是继承 org.apache.jasper.runtime.HttpJspBase这个类的，通过查看Tomcat服务器的源代码，可以知道在 apache-tomcat-6.0.20-src\java\org\apache\jasper\runtime目录下存HttpJspBase这个类的源代码文件.

　　HttpJspBase类是继承HttpServlet的，所以HttpJspBase类是一个Servlet，而index_jsp又是继承HttpJspBase类的，所以index_jsp类也是一个Servlet，所以当浏览器访问服务器上的index.jsp页面时，其实就是在访问index_jsp这个Servlet，index_jsp这个Servlet使用_jspService这个方法处理请求。

2. **Jsp页面中的html排版标签是如何被发送到客户端的？**

　　浏览器接收到的这些数据在jsp中编写的java代码和html代码都会被翻译到_jspService方法中去，在jsp中编写的java代码会原封不动地翻译成java代码，如<%out.print("Hello Jsp");%>直接翻译成out.print("Hello Jsp");，而HTML代码则会翻译成使用out.write("<html标签>\r\n");的形式输出到浏览器。在jsp页面中编写的html排版标签都是以out.write("<html标签>\r\n");的形式输出到浏览器，浏览器拿到html代码后才能够解析执行html代码。

3. **Jsp页面中的java代码服务器是如何执行的？**

　　在jsp中编写的java代码会被翻译到_jspService方法中去，当执行_jspService方法处理请求时，就会执行在jsp编写的java代码了，所以Jsp页面中的java代码服务器是通过调用_jspService方法处理请求时执行的。

4. **Web服务器在调用jsp时，会给jsp提供一些什么java对象**

   **1.request对象**

   　　客户端的请求信息被封装在request对象中，通过它才能了解到客户的需求，然后做出响应。它是HttpServletRequest类的实例。

   **2.response对象**

   　　response对象包含了响应客户请求的有关信息，但在JSP中很少直接用到它。它是HttpServletResponse类的实例。

   **3.session对象**

   　　session对象指的是客户端与服务器的一次会话，从客户连到服务器的一个WebApplication开始，直到客户端与服务器断开连接为止。它是HttpSession类的实例.

   **4.out对象**

   　　out对象是JspWriter类的实例,是向客户端输出内容常用的对象.

   **5.page对象**

   page对象就是指向当前JSP页面本身，有点象类中的this指针，它是java.lang.Object类的实例.

   **6.application对象**

   　　application对象实现了用户间数据的共享，可存放全局变量。它开始于服务器的启动，直到服务器的关闭，在此期间，此对象将一直存在；这样在用户的前后连接或不同用户之间的连接中，可以对此对象的同一属性进行操作；在任何地方对此对象属性的操作，都将影响到其他用户对此的访问。服务器的启动和关闭决定了application对象的生命。它是ServletContext类的实例。

   **7.exception对象**

   　　exception对象是一个例外对象，当一个页面在运行过程中发生了例外，就产生这个对象。如果一个JSP页面要应用此对象，就必须把isErrorPage设为true，否则无法编译。他实际上是java.lang.Throwable的对象

   **8.pageContext对象**

   　　pageContext对象提供了对JSP页面内所有的对象及名字空间的访问，也就是说他可以访问到本页所在的SESSION，也可以取本页面所在的application的某一属性值，他相当于页面中所有功能的集大成者，它的本类名也叫pageContext。

   **9.config对象**

   　　config对象是在一个Servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数（通过属性名和属性值构成）以及服务器的有关信息（通过传递一个ServletContext对象）

5. **Jsp+Servlet开发**

不管是JSP还是Servlet，虽然都可以用于开发动态web资源。但由于这2门技术各自的特点，在长期的软件实践中，人们逐渐把servlet作为web应用中的控制器组件来使用，而把JSP技术作为数据显示模板来使用。其原因为，程序的数据通常要美化后再输出：让jsp既用java代码产生动态数据，又做美化会导致页面难以维护。让servlet既产生数据，又在里面嵌套html代码美化数据，同样也会导致程序可读性差，难以维护。因此最好的办法就是根据这两门技术的特点，让它们各自负责各的，servlet只负责响应请求产生数据，并把数据通过转发技术带给jsp，数据的显示jsp来做。

**2.6、Tomcat服务器的执行流程**

![tomcat服务器的执行流程](master/images/tomcat服务器的执行流程.png)

第一次执行：

1.客户端通过电脑连接服务器，因为是请求是动态的，所以所有的请求交给WEB容器来处理

2.在容器中找到需要执行的*.jsp文件

3.之后*.jsp文件通过转换变为*.java文件

4.*.java文件经过编译后，形成*.class文件

5.最终服务器要执行形成的*.class文件

第二次执行：

1.因为已经存在了*.class文件，所以不在需要转换和编译的过程

修改后执行：因为这里源文件已经被修改过了，所以需要重新转换，重新编译。

**3.jsp工作原理**

![jsp工作原理](C:\Users\Young\Desktop\Temp\web资料\javaWeb笔记\images\jsp工作原理.png)

当客户端浏览器向服务器请求一个 JSP 页面时，服务器收到该请求后，首先检查所请求的这个JSP 文件内容 ( 代码) 是否已经被更新，或者是否是 JSP 文件创建后的第一次被访问，如果是，那么，这个 JSP 文件就会在服务器端的JSP 引擎作用下转化为一个 Servlet 类的 Java 源代码文件。紧接着，这个 Servlet 类会在 Java 编译器的作用下被编译成一个字节码文件，并装载到 jvm 解释执行。剩下的就等同于 Servlet 的处理过程了。如果被请求的 JSP 文件内容 ( 代码 ) 没有被修改，那么它的处理过程也等同于一个 Servlet 的处理过程。即直接由服务器检索出与之对应的Servlet 实例来处理。

需要注意的是，JSP 文件不是在服务器启动的时候转换成 Servlet 类的。而是在被客户端访问的时候才可能发生转换的 ( 如 JSP 文件内容没有被更新等，就不再发生 Servlet 转换 )。

就 Tomcat 而言，打开目录 %Tomcat%/work/%您的工程文件目录%，然后会看到里面有 3个子目录：org/apache/jsp，若没有这3个目录，说明项目的 JSP 文件还没有被访问过，打开进到 jsp 目录下，会看到一些*_jsp.java 和 *_jsp.class 文件，这就是 JSP 文件被转换成Servlet 类的源文件和字节码文件了。

**Servlet总结**

Servlet 没有 main 方法，不能够独立的运行，它的运行需要容器的支持，Tomcat 是最常用的 JSP/Servlet 容器。Servlet 运行在 Servlet 容器中，并由容器管理从创建到销毁的整个过程。

**1.Servlet 的生命周期**

(1) 加载和实例化

Servlet 容器装载和实例化一个 Servlet。创建出该 Servlet 类的一个实例。

(2) 初始化

在 Servlet 实例化完成之后，容器负责调用该 Servlet 实例的 init() 方法，在处理用户请求之前，来做一些额外的初始化工作。

(3) 处理请求

当 Servlet 容器接收到一个 Servlet 请求时，便运行与之对应的 Servlet 实例的 service() 方法，service() 方法再派遣运行与请求相对应的 doXX(doGet，doPost) 方法来处理用户请求。

(4) 销毁

当 Servlet 容器决定将一个 Servlet 从服务器中移除时 ( 如 Servlet 文件被更新 )，便调用该 Servlet 实例的destroy() 方法，在销毁该 Servlet 实例之前，来做一些其他的工作。其中，(1)(2)(4) 在 Servlet 的整个生命周期中只会被执行一次。

**2.Servlet 的工作原理**

![servlet工作原理](C:\Users\Young\Desktop\Temp\web资料\javaWeb笔记\images\servlet工作原理.png)

　　当客户端浏览器向服务器请求一个 Servlet 时，服务器收到该请求后，首先到容器中检索与请求匹配的 Servlet实例是否已经存在。

　　若不存在，则 Servlet 容器负责加载并实例化出该类 Servlet的一个实例对象，接着容器框架负责调用该实例的init() 方法来对实例做一些初始化工作，然后Servlet 容器运行该实例的 service() 方法。

　　若 Servlet 实例已经存在，则容器框架直接调用该实例的 service() 方法。service() 方法在运行时，自动派遣运行与用户请求相对应的 doXX() 方法来响应用户发起的请求。

通常，每个 Servlet 类在容器中只存在一个实例，每当请求到来时，则分配一条线程来处理该请求。

**Servlet与JSP**

　　JSP 本质是一个 Servlet，它的运行也需要容器的支持。

　　在 JSP 和 Servlet 文件中都可以编写 Java 和 HTML 代码，不同的是，Servlet 虽然也可以动态的生成页面内容，但更加偏向于逻辑的控制。JSP 最终被转换成 Servlet 在 jvm 中解释执行，在 JSP 中虽然也可以编写 Java 代码，但它更加偏向于页面视图的展现。在 MVC 架构模式中，就 JSP 和 Servlet 而言，C 通常由 Servlet 充当，V通常由 JSP 来充当。

**JSP是由Servlet演变过来的**