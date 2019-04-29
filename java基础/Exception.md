### 1.try-catch-finally基础

#### 1.throw Exception

```java
   public void divide(int numberToDivide, int numberToDivideBy)
    throws BadNumberException{
        if(numberToDivideBy == 0){
            throw new BadNumberException("Cannot divide by 0");
        }
        return numberToDivide / numberToDivideBy;
    }
```

以上是一个抛出异常的的例子。抛出异常(checked exception)必须在方法标签的上声明该异常类或它的父类

当异常被抛出后，他后面的语句是不会的执行的。

#### 2.catch Exception

```java
   public void callDivide(){
        try {
            int result = divide(2,1);
            System.out.println(result);//(1)
        } catch (BadNumberException e) {
            //do something clever with the exception
            System.out.println(e.getMessage());//(2)
        }
        System.out.println("Division attempt done");//3
    }
```

由于divide()方法抛出了checked excption，所以调用者callDivide()要么捕获该异常要么继续抛出。上面是捕获抛出的异常的例子.当异常发生在divide()函数中,异常被抛出,那么(1)将不会被执行,异常会被catch块捕并执行(2),在catch块执行完成后,**接着会执行(3)**.当在divide()中没有抛出异常,那么catch块会被忽视.

这里有一点要注意的是:**当一个异常被捕获后,他后的语句依然会被执行**

如果以上异常不想捕获,那么可以直接抛出

```Java
 public void callDivide() throws BadNumberException{
        int result = divide(2,1);
        System.out.println(result);
    }
```

#### 3.finally

```java
public void openFile(){
        FileReader reader = null;
        try {
            reader = new FileReader("someFile");
            int i=0;
            while(i != -1){
                i = reader.read();
                System.out.println((char) i );
            }
        } catch (IOException e) {
            //do something clever with the exception
        } finally {
            if(reader != null){
                try {
                    reader.close();
                } catch (IOException e) {
                    //do something clever with the exception
                }
            }
            System.out.println("--- File End ---");
        }
    }
```

无论是否在try或catch块内抛出异常，都会执行finally块中的代码.如果try或者catch块中有return 语句,那么finally块的语句也会在方法返回之前被执行.

#### 4.try-with-resources

```java
try(InputStream inputStream = new FileInputStream("file")){
           int data = 0;
           while((data=inputStream.read())!= -1){
               System.out.println((char)data);
           }
       }
```

当执行离开try块时,资源自动关闭,不管是否抛出异常.

java9以后,可以直接在try**()**中使用资源变量

```java
InputStream inputStream = new FileInputStream("file");
try(inputStream){
  //...
}
```

关闭多个资源

```java
  try(  FileInputStream input = new FileInputStream("file.txt");
        BufferedInputStream bufferedInput = new BufferedInputStream(input)
     ) {
    //...
  }
```

以上资源自动关闭的原理的在于:这些类都实现了AutoCloseable接口

```java
public interface AutoCloseable{
  public void close() throws Exceptiop;
}
```

所以我们可以自定义一个类来让他具备在try(){}中有自动关闭的功能

```java
class CloseMyResources implements AutoCloseable{
    public CloseMyResources(){
        System.out.println("创建我的的资源");
    }
    @Override
    public void close() throws Exception{
        System.out.println("关闭我的资源");
    }
    public  void dosomething(){
        System.out.println("do something!");
    }
}
//测试
 try(CloseMyResources resources = new CloseMyResources()){
             resources.dosomething();
 }
/*输出
创建我的的资源
do something!
关闭我的资源
*/
```

#### 5.捕获多个异常

```java
try {

    // execute code that may throw 1 of the 3 exceptions below.

} catch(SQLException e) {
    logger.log(e);

} catch(IOException e) {
    logger.log(e);

} catch(Exception e) {
    logger.severe(e);
}
//java7以后
try {
    // execute code that may throw 1 of the 3 exceptions below.

} catch(SQLException | IOException e) {//同时捕获多个异常
    logger.log(e);

} catch(Exception e) {
    logger.severe(e);
}
```

#### 6.异常继承体系

异常是可以继承的

```java
public class MyException extends Exception{
    //constructors etc.
}
```

catch中

```java
try{
    //call some methods that throw IOException's
} catch (FileNotFoundException e){
} catch (IOException e){
}
```

IOException可以捕获到它的子类.所以要将子类放在多个catch块的前面,父类放在后面,让异常更加精确被捕获.

throws中

```java
public void doSomething（） throws IOException{
}
```

在方法体内IOException的子类的都可以被抛出

### 2.checked exception和unchecked exception

checked exception和unchecked exception的区别在与

1. checked exceptions异常必须显示捕获或者声明抛出,而unchecked exception没有这种要求.
2. checked exceptions继承自Exception ,而unchecked Exceptions继承自RuntimeException

自定义异常继承自checked exception

```java
 public class BadUrlException extends Exception {//BadUrlException为checked exception
        public BadUrlException(String s) {
            super(s);
        }
 }

//catch处理
 public void storeDataFromUrl(String url){
        try {
            String data = readDataFromUrl(url);//调用
        } catch (BadUrlException e) {
            e.printStackTrace();
        }
 }
   //抛出
   public void storeDataFromUrl(String url)
    throws BadUrlException{
        String data = readDataFromUrl(url);
    }

    public String readDataFromUrl(String url)
    throws BadUrlException{//声明
        if(isUrlBad(url)){
            throw new BadUrlException("Bad URL: " + url);//显示抛出checked exception
        }

        String data = null;
        //read lots of data over HTTP and return
        //it as a String instance.
        return data;
    }

```

再来看看继承自unchecked exception

```java
public class BadUrlException extends RuntimeException {
        public BadUrlException(String s) {
            super(s);
        }
}

public void storeDataFromUrl(String url){//不用做任何处理
        String data = readDataFromUrl(url);
    }

    public String readDataFromUrl(String url) {
        if(isUrlBad(url)){
            throw new BadUrlException("Bad URL: " + url);//显示抛出unchecked exception
        }

        String data = null;
        //read lots of data over HTTP and
        //return it as a String instance.

        return data;
    }
```

### 3.包装异常

将捕获的异常包装后再抛出

```java
    try{
        dao.readPerson();
    } catch (SQLException sqlException) {
        throw new MyException("error text", sqlException);
    }
```

### 4.可插拔异常处理处理

自定义一个异常处理接口

```java
interface ExceptionHandler{
  void handle(Exception e,String errorMessage)
}
```

实现类

```java
public class IgnoringHandler implements ExceptionHandler{
    public void handle(Exception e, String message) {
        //do nothing, just ignore the exception
    }
}

public class WrappingHandler implements ExceptionHandler{
    public void handle(Exception e, String message){
        throw new RuntimeException(message, e);
    }
}

public class CollectingHandler implements ExceptionHandler{
    List exceptions = new ArrayList();

    public List getExceptions(){ return this.exceptions; }

    public void handle(Exception e, String message){
        this.exceptions.add(e);

        //message is ignored here, but could have been
        //collected too.
    }
}
```

使用

```java
public class Component{
    protected ExceptionHandler exceptionHandler = null;

    public void setExceptionHandler(ExceptionHandler handler){
        this.exceptionHandler = handler;
    }

    public void processFile(String fileName){
        FileInputStream input = null;
        try{
            input = new FileInputStream(fileName);
            processStream(input);
        } catch (IOException e){
            this.exceptionHandler.handle(e,
                "error processing file: " + fileName);
        }
    }

    protected void processStream(InputStream input)
        throws IOException{
        //do something with the stream.
    }
}
```

### 5.异常处理模式

通常再进行IO操作时，需要进行大量的异常处理，对IO的操作和异常的处理的代码融合在一起，可读性很差。

为了是代码可读性和健壮性，抽取其中IO操作，留下操作接口，再try-catch-finally中直接调用接口的方法来处理IO,让IO操作者实现IO操作接口。

这里要用到**模板设计模式**，将异常处理和IO操作分离

定义IO操作接口

```java
interface InutStreamProcessor{
  void process(InputStream inputStream)  throws IOException;
}
```

IO处理模板

```java
public class InputStreamProcessingTemplate {

  //声明静态方法也佳，直接使用类名调用
    public void process(String fileName, InputStreamProcessor processor){
        IOException processException = null;
        InputStream input = null;
        try{
            input = new FileInputStream(fileName);
          	//调用处理器执行IO操作
            processor.process(input);
        } catch (IOException e) {
            processException = e;
        } finally {
           if(input != null){
              try {
                 input.close();
              } catch(IOException e){
                 if(processException != null){
                    throw new MyException(processException, e,
                      "Error message..." +
                      fileName;
                 } else {
                    throw new MyException(e,
                        "Error closing InputStream for file " +
                        fileName);
                 }
              }
           }
           if(processException != null){
              throw new MyException(processException,
                "Error processing InputStream for file " +
                    fileName;
        }
    }
}
```

使用

```java
new InputStreamProcessingTemplate()
        .process("someFile.txt", new InputStreamProcessor(){//匿名内部类实现处理器接口
            public void process(InputStream input) throws IOException{
                int inChar = input.read();
                while(inChar !- -1){
                    //do something with the chars...
                }
            }
        });
```