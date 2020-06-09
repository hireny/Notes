# Java IO

- 一、概览
- 二、磁盘操作
- 三、字节操作
- 四、字符操作
- 五、对象操作
- 六、网络操作

## 一、概览

Java 的 I/O 大概可以分成以下几类：

- 磁盘操作：FIle
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket

## 二、磁盘操作

`File` 类可以用于表示文件和目录的信息，但是它不表示文件的内容。

递归地列出一个目录下所有文件：

```java
public static void listFiles(File dir) {
	if (dir == null || !dir.exists()) {
		return;
	}
	if (dir.isFile()) {
		System.out.println(dir.getName());
		return;
	}
	for (File file : dir.listFiles()) {
		listAllFiles(file);
	}
}
```

从 Java 7 开始，可以使用 Paths 和 Files 代替 FIle。

## 三、字节操作