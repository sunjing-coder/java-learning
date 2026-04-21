# Day01 - Java基础复习

ASCII字符集: 只有英文,数字,符号等,占一个字节

GBK字符集: 汉字占两个字节,英文数字占一个字节

UTF-8字符集: 汉字占3个字节,英文,数字占一个字节



对字符的编码:

​	byte[] getBytes() 使用平台默认字符集将该String编码为一系列字节,将结果存储到新的字节数组中

​	byte[] getBytes(String charsetName)使用指定的字符集将该String编码为一系列字节,将结果存储到新的字节	数组中

对字符的解码:

​	String(byte[] bytes)默认方式解码

​	String(byte[] bytes,String charsetName) ,通过指定字符集解码



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

