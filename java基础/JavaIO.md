### 1.File

文件和文件夹都是用File表示

创建文件对象

```java
File file = new File("绝对路径|相对路径");
File file = new File(parentFile,"fileName");//将parentFile作为父目录创建文件对象
```

常用方法

```java
file.exists();
file.isDirectory();
file.isFile();
file.getName();//返回文件名(包括扩展名)
long length = file.length();//字节数
boolean success = file.renameTo(newFile);//改文件名
File[] fs = file.listFiles();//返回文件夹下所有文件和文件夹,子文件和子文件夹不包括
file.mkdir();//创建文件夹
file.mkdirs();//file目录不存在则先创建父目录，然后创建子目录
file.createNewFile();
file.delete();
```

### 2.Reader、Writer

#### 1.**Reader**

```java
Reader reader = new Filereader("path");//new FileReader(file);
int read();//读取一个字符，返回字符的ASCII值，故要进行强制类型转换；结尾返回-1
//中文可以直接用char转换
int read(char[] cbuf);//将读取的字符存入cbuf中,返回读取的字符的个数；结尾返回-1
```

InputStreamReader

#### 2.**将字节输入流转换为字符输入流**

```java
Reader reader = new InputStreamReader(inputStream);
```

#### 3.Writers

字节输出流转换为字符输出流

```java
Writer writer = OutputStreamWriter(outputStream);
write(char c);
write(char c[]);
write(char c[],int off,int len);//c[off]-->c[off+len-1]
write(String str);
Writer append(char c);
Writer append(CharSequence csq);//CharSequence的实现类有String、StringBuilder、StringBuffer
```

**使用更加高效的缓冲**

```java
Reader reader = new BufferedReader(new FileReader(file));
Writer writer = new BufferedWriter(new FileWriter(file));
```

### 3.InputStream/OutputStream

#### 1.**inputStream**

```java
int read();//返回数据的字节。中文不能使用char强转
int read(byte[] b);//返回读取的字节数
```

#### **2.outputStream**

```java
void write(byte[] b);
void write(byte[] b,int off,int len);//b[off]-->b[off+len-1]
void write(int b);
```

**FileInputStream FileOutputStream**分别继承自inputStream和outputStream

将文件与流之间转换

图片复制实例

```java
	InputStream in = new FileInputStream(new File("i-4.jpg"));
        OutputStream out = new  FileOutputStream("i-5.jpg");
        byte[] b = new byte[100];
        int len = 0;
        while((len=in.read(b)) != -1){
            out.write(b,0,len);//读取多少，写入多少，以免字节丢失或多余
        }
        out.close();
        in.close();
```

#### 3.**ByteArrayInputStream ByteArrayOutputStream**

将字节数组转与流之间转换

```java
InputStream in = new ByteArrayInputStream(bytes);//字节数组--》流
OutputStream out = new ByteArrayOutputStream();//内部有一个byte[]
//向out中写东西
byte[] bytes = outputStream.toByteArray();//复制一份到数组中
```

#### 4.BufferedInputStream BufferdOutputStream

内部维持一个数组，通常为1024*n，整块的存取数据，加速存取速度

```java
OutputStream out = new BufferedOutputStream(new FileOutputStream(file));
InputStream in = new BufferedInputStream(new FileInputStream(file));
```

#### 5.ObjectOutputStream ObjectInputStream

```java
Object obj = (Object)objectInputStream.readObject();
objectOutputStream.writeObject(obj);
```

用处：实现对象深度拷贝
