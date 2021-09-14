# 文件操作与IO流

## 1.1 File类

类在包java.io中

用户界面和操作系统使用依赖于系统的*路径名字符串*命名文件和目录。  这个类提供了一个抽象的，独立于系统的层次化路径名的视图。

- File类可以表示一个文件，还可以表示一个目录（Directory），所以我们可以在程序中用**File 类**的对象可以表示一个**文件** 或者 **目录**
- 当创建了 File 对象之后，我们可以利用该对象来对文件或者目录进行书属性修改：例如：文件的名称，修改日期的日期等等
- File 类的对象 还不能直接对文件进行读写操作，只能修改文件的属性

构造方法：

- - | `File(File parent, String child)`  从父抽象路径名和子路径名字符串创建新的 `File`实例。 |
    | ------------------------------------------------------------ |
    | `File(String pathname)`  通过将给定的路径名字符串转换为抽象路径名来创建新的 `File`实例。 |
    | `File(String parent,  String child)`  从父路径名字符串和子路径名字符串创建新的 `File`实例。 |
    | `File(URI uri)`  通过将给定的 `file:` URI转换为抽象路径名来创建新的 `File`实例。 |


代码示例

```java
		File src=new File("C://Users//enslaver//Desktop//test.text");
        if(src.exists())
        {
            System.out.println("文件已存在");
        }
        else
        {
            try {
                src.createNewFile();
                System.out.println("文件创建成功");
            } catch (Exception e) {
                // TODO: handle exception
            }
        }
        System.out.println("文件已经存在:"+src.exists());
        System.out.println("文件的名字:"+src.getName());
        System.out.println("文件的路径:"+src.getPath());
        System.out.println("文件的绝对路径:"+src.getAbsolutePath());
        System.out.println("是目录吗:"+src.isDirectory());
        System.out.println("文件大小:"+src.length());
```



## 1.2 Stream流

### 1.2.1 Stream字节流

- 基本单位 , 只能读取英文字符，在 java.io包中，大部分操作继承InputStream（输入字节流）类和OutputStream（输出字节流）类。
- FileInputStream类 和 FileOutputStream类总是成对出现的，一个用作输入流，另一个自然是输出流
- 我们在输入流中使用 byte（字节）数组 来存储我们的数据，因此我们不必考虑数据格式，所以说这两种操作的效率会比较高

####  	1.2.1.1 FileInputStream类

- 继承于InputStream类，这是一个文件输入流，进行文件读操作的最基本的类

- 作用是将文件中的数据输入到内存中，我们可以用它来读文件操作

- 由于字节流的缘故，因此无法读取中文字符

  

- - | `FileInputStream(File file)`  通过打开与实际文件的连接创建一个 `FileInputStream` ，该文件由文件系统中的  `File`对象 `file`命名。 |
    | ------------------------------------------------------------ |
    | `FileInputStream(FileDescriptor fdObj)`  创建 `FileInputStream`通过使用文件描述符 `fdObj`  ，其表示在文件系统中的现有连接到一个实际的文件。 |
    | `FileInputStream(String name)`  通过打开与实际文件的连接来创建一个 `FileInputStream` ，该文件由文件系统中的路径名  `name`命名。 |

代码示例

```java
try {
    File file=new File("C://Users//enslaver//Desktop//test.text");
    FileInputStream f1=new FileInputStream(file);//这里需要进行抛出异常处理
    for (int i = 0; i < file.length(); i++) {
        char ch=(char)(f1.read());//循环读取字符
        System.out.print(ch+" ");
    }
    System.out.println();//换行操作
    f1.close();//关闭文件
} catch (Exception e) {
    // TODO: handle exception
    System.out.println("文件打开失败");
}
```

#### 1.2.1.2 FileOutputStream类

- FileOutputStream类称为文件输出流，继承于OutputStream类，是文件的基本读写的一个类
- 它的作用和上面读文件恰恰相反，将内存中的数据输出到文件中，所以我们可以用这个类来进行写文件的操作
- 覆盖文件原有内容

- - | FileOutputStream(File file)`  创建文件输出流以写入由指定的 `File`对象表示的文件。 |
    | ------------------------------------------------------------ |
    | `FileOutputStream(File file,  boolean append)`  创建文件输出流以写入由指定的 `File`对象表示的文件。 |
    | `FileOutputStream(FileDescriptor fdObj)`  创建文件输出流以写入指定的文件描述符，表示与文件系统中实际文件的现有连接。 |
    | `FileOutputStream(String name)`  创建文件输出流以指定的名称写入文件。 |
    | `FileOutputStream(String name,  boolean append)`  创建文件输出流以指定的名称写入文件。 |

代码示例

```java
File file = new File("C://Users//enslaver//Desktop//test.text");
FileOutputStream f1 = new FileOutputStream(file);//(file,true)，这里有true的话，代表可以在文件后面追加内容
String str = "I love coding";
byte[] buff = str.getBytes();//将字符串转换为字节数组
try {
    f1.write(buff);//把字节数组的内容写进去文件
} catch (Exception e) {
    // TODO: handle exception
} finally {
    try {
        f1.close();
    } catch (IOException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```

### 1.2.2  Stream流（字符流）

#### 1.2.2.1  FileWriter类 与 BufferedWriter类

FileWriter构造方法

- - | `FileWriter(File file)`  给一个File对象构造一个FileWriter对象。 |
    | ------------------------------------------------------------ |
    | `FileWriter(File file,  boolean append)`  给一个File对象构造一个FileWriter对象。 |
    | `FileWriter(FileDescriptor fd)`  构造与文件描述符关联的FileWriter对象。 |
    | `FileWriter(String fileName)`  构造一个给定文件名的FileWriter对象。 |
    | `FileWriter(String fileName, boolean append)`  构造一个FileWriter对象，给出一个带有布尔值的文件名，表示是否附加写入的数据。 |

BufferedWriter构造方法

- - | `BufferedWriter(Writer out)`  创建使用默认大小的输出缓冲区的缓冲字符输出流。 |
    | ------------------------------------------------------------ |
    | `BufferedWriter(Writer out,  int sz)`  创建一个新的缓冲字符输出流，使用给定大小的输出缓冲区。 |

代码示例

```java
String[] str = {"此夜曲中闻折柳", "何人不起故园情"};
FileWriter f = null;//创建文件写入对象
BufferedWriter f1 = null;//创建字符流写入对象
try {
    //这里把文件写入对象和字符流写入对象分开写了
    f = new FileWriter("C://Users//enslaver//Desktop//test.text");//创建一个名为cc.txt的文件
    f1 = new BufferedWriter(f);
    //通过循环遍历上面的String 数组中的元素
    for (int i = 0; i < str.length; i++) {
        f1.write(str[i]);//把String中的字符写入文件
        f1.newLine();//换行操作
    }
} catch (Exception e) {
    // TODO: handle exception
} finally {//如果没有catch 异常，程序最终会执行到这里
    try {
        f1.close();
        f.close();//关闭文件
    } catch (Exception e2) {
    }

}


```



#### 1.2.2.2 FileReader类 与 BufferedReader类

FileReader构造方法

- - | Constructor and Description                                  |
    | ------------------------------------------------------------ |
    | `FileReader(File file)`  创建一个新的 `FileReader` ，给出 `File`读取。 |
    | `FileReader(FileDescriptor fd)`  创建一个新的 `FileReader` ，给定 `FileDescriptor`读取。 |
    | `FileReader(String fileName)`  创建一个新的 `FileReader` ，给定要读取的文件的名称。 |

BufferedReader构造方法

- - | `BufferedReader(Reader in)`  创建使用默认大小的输入缓冲区的缓冲字符输入流。 |
    | ------------------------------------------------------------ |
    | `BufferedReader(Reader in,  int sz)`  创建使用指定大小的输入缓冲区的缓冲字符输入流。 |

代码示例

```java
File file = new File("C://Users//enslaver//Desktop//test.text");
FileReader f = null;//文件读取对象
BufferedReader f1 = null;//字符流对象
try {
    f = new FileReader(file);
    f1 = new BufferedReader(f);
    //循环打印cc文件中的每行数据
    String str = null;
    while ((str = f1.readLine()) != null) {
        System.out.println(str);
    }

} catch (Exception e) {
    // TODO: handle exception
} finally {
    try {
        f1.close();
        f.close();
    } catch (Exception e2) {
        // TODO: handle exception
    }
}
```

## 1.3序列化与反序列化

##### ObjectOutputStream

- ObjectOutputStream将Java对象的原始数据类型和图形写入OutputStream

构造方法

- - | `protected ` | `ObjectOutputStream()`  为完全重新实现ObjectOutputStream的子类提供一种方法，不必分配刚刚被ObjectOutputStream实现使用的私有数据。 |
    | ------------ | ------------------------------------------------------------ |
    | ` `          | `ObjectOutputStream(OutputStream out)`  创建一个写入指定的OutputStream的ObjectOutputStream。 |

