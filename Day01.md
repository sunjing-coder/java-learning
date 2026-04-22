# Day01 - Java基础复习

#### 字符集

ASCII字符集: 只有英文,数字,符号等,占一个字节

GBK字符集: 汉字占两个字节,英文数字占一个字节

UTF-8字符集: 汉字占3个字节,英文,数字占一个字节



#### 编码和解码

对字符的编码:

​	byte[] getBytes() 使用平台默认字符集将该String编码为一系列字节,将结果存储到新的字节数组中

​	byte[] getBytes(String charsetName)使用指定的字符集将该String编码为一系列字节,将结果存储到新的字节数组中

对字符的解码:

​	String(byte[] bytes)默认方式解码

​	String(byte[] bytes,String charsetName) ,通过指定字符集解码



#### 输入流和输出流

文件输入流FileInputStream

```java
//存在截断汉字字节的可能性,utf-8中汉字由三个组成
InputStream fileInputStream = new FileInputStream("src/1");
byte[] buffer = new byte[3];
int len;
while((len = fileInputStream.read(buffer))!=-1){
	System.out.println(new String(buffer,0,len));
}

//一次性读完全部字节,避免读取汉字乱码问题,版本过低没有readAllBytes()这个方法
byte[] bytes = fileInputStream.readAllBytes();
//如果文件过大创建的字符数组也会过大,可能引起内存溢出
```

> [!CAUTION]
>
> 读取文本适合用字符流,字节流适合做数据的转移,比如文件复制
>
> **字符流** = **带翻译的智能工具**，专门处理**人能看懂的文字**，解决**乱码**问题。
>
> **字节流** = **纯搬运的苦力**，专门处理**所有文件的数据拷贝**，保证**原汁原味**。



文件输出流FileOutputStream

```java
OutputStream out = new FileOutputStream("src/1");//会覆盖原来数据
out.write("Hello World".getBytes());
out.close();
```



文件复制功能实现

//try-catch-finally实现代码臃肿

```java
public static void copyFile(String srcFile,String destFile) throws Exception{
    InputStream inputStream = null;
    OutputStream outputStream = null;
    try {
        inputStream = new FileInputStream(srcFile);
        outputStream = new FileOutputStream(destFile);
        byte[] bytes = new byte[1024];
        int len = 0;
        while((len = inputStream.read(bytes)) != -1){
            outputStream.write(bytes,0,len);
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

try-with-resource

```java
public static void copyFile(String srcFile,String destFile) throws Exception{
    try(            
            InputStream inputStream= new FileInputStream(srcFile);
            OutputStream outputStream = new FileOutputStream(destFile)
    ){
        byte[] bytes = new byte[1024];
        int len = 0;
        while((len = inputStream.read(bytes)) != -1){
            outputStream.write(bytes,0,len);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```



字符输入流FileReader

字符输出流FileWriter

字符输出流写出数据后,必须刷新(.flush()),或者关闭流(.close()),数据才能生效(原因是数据是先写在内存缓冲区,提高了书写速度)



缓冲字节输入流(BufferedInputStream),缓冲字节输出流(BufferedOutputStream),缓冲字符输入流(BufferedReader),缓冲字符输出流(BufferedWriter)

可以提高字节输入流和字节输出流的读取和写入数据的性能,原因是他们自带了8KB的缓冲池

创建方式:

```java
public BufferedInputStream(InputStream is);
public BufferedOutputStream(OutputStream out);
```

```java
public static void copyFile(String srcFile,String destFile) throws Exception{
    try(
            InputStream inputStream= new FileInputStream(srcFile);
            InputStream bufferedInputStream = new BufferedInputStream(inputStream);
            OutputStream outputStream = new FileOutputStream(destFile);
            OutputStream bufferedOutputStream = new BufferedOutputStream(outputStream);
    ){
        byte[] bytes = new byte[1024];
        int len = 0;
        while((len = bufferedInputStream.read(bytes)) != -1){
            bufferedOutputStream.write(bytes,0,len);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

```java
BufferedReader br = new BufferderReader(fr);
br.readerLine()//读取文件中的一行
    
BufferedWriter bw = new BufferderWriter(fw);
bw.newLine();//换行
```

