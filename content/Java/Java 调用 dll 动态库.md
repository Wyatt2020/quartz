---
created: 2022-05-10 09:20
modified: 2024-07-15 22:32
tags:
  - Java
---

> [!reference]
> 1. JNA
>    - [Java 利用 JNA 调用 dll 文件的方法](https://blog.csdn.net/juligang320/article/details/122450365)
>    - [Java 使用 JNA 调用 C/C++ 动态链接库 dll](https://www.bilibili.com/video/av60824694)
> 2. JNI
>    - [Java 调用 C++ DLL 库方法](https://juejin.cn/post/6844903656559476743)

### JNA 与 JNI 对比

JNA (Java Native Access) 和 JNI (Java Native Interface) 都是用于在 Java 应用程序中调用本地代码（通常是 C 或 C++ 编写的）的机制，但它们之间存在一些关键的区别：

#### JNI (Java Native Interface)

- **直接性**：JNI 是 Java 平台的一部分，直接内置于 JVM 中，提供了与本地代码交互的能力。
- **性能**：理论上，JNI 提供了更好的性能，因为它允许更直接地控制和访问本地资源。
- **复杂性**：使用 JNI 需要编写 C/C++ 的绑定代码，这包括手动声明 native 方法，==生成并编辑头文件，以及编译和链接本地库。这个过程相对复杂，需要熟悉 C/C++ 和本地编译工具链。==
- **移植性**：JNI 库可能需要为不同的操作系统和架构分别编译，这增加了跨平台应用的维护难度。
- **安全性**：JNI 调用可能会影响 JVM 的安全性，因为它们绕过了 Java 的沙箱模型。

#### JNA (Java Native Access)

- **易用性**：JNA 提供了一个更高级别的抽象，允许 Java 程序员以纯 Java 方式调用本地库，而==无需编写任何 C/C++ 代码。==
- **自动类型转换**：JNA 自动处理 Java 和本地类型之间的转换，简化了调用过程。
- **移植性**：JNA 动态加载本地库，并且通常会附带预编译的库，减少了跨平台的编译负担。
- **性能**：JNA 相对于 JNI 性能较低，因为它在 JNI 的基础上增加了一层抽象，涉及额外的间接性和开销。
- **安全性**：JNA 仍然使用 JNI 进行本地调用，但它在 Java 层面提供了更多的封装，有助于保持应用的安全性。

#### 总结

- 如果你关心性能并且愿意处理额外的复杂性，JNI 可能是更好的选择。
- 如果你更注重开发效率和跨平台的便利性，JNA 提供了更简单、更直接的方式来访问本地库。

### 使用 JNA 调用动态库

假设我们有一个名为 `MyLibrary.dll` 的 DLL 文件，其中包含一个函数 `helloWorld`，该函数接收一个字符串参数并打印出来。这个函数的 C 定义可能是这样的：

```c
#include <stdio.h>

void helloWorld(const char *message) {
    printf("%s\n", message);
}
```

确保你的动态链接库（DLL 或 .so 文件）位于 Java 应用程序可以找到的路径中，或者在代码中使用 System.setProperty("jna.library.path", "path/to/your/library") 设置正确的路径。

注意，为了使用 JNA，你需要在项目中添加 JNA 的依赖。如果你使用 Maven，可以在 `pom.xml` 文件中添加以下依赖：

```xml
<dependency>
    <groupId>com.sun.jna</groupId>
    <artifactId>jna</artifactId>
    <version>5.11.0</version> <!-- 使用最新的版本 -->
</dependency>
```

接下来，我们将使用 JNA 在 Java 中调用这个函数：

1. **定义接口**：

```java
import com.sun.jna.Library;
import com.sun.jna.Native;

public interface MyLibrary extends Library {
    // 加载动态链接库，这里的 "MyLibrary" 是库的名字，不包括 ".dll" 或 ".so"
    MyLibrary INSTANCE = (MyLibrary) Native.loadLibrary("MyLibrary", MyLibrary.class);

    // 声明 native 方法，参数类型和返回类型应该匹配 DLL 中的函数
    void helloWorld(String message);
}
```

2. **调用函数**：

在 Java 类中，我们可以直接通过 `MyLibrary.INSTANCE` 来调用 `helloWorld` 方法。

```java
public class Main {
    public static void main(String[] args) {
        // 调用 DLL 中的函数
        MyLibrary.INSTANCE.helloWorld("Hello from Java!");
    }
}
```

### 使用 JNI 调用动态库

1. 声明 native 方法

在 Java 中，首先需要定义一个类，其中包含你想要调用的 native 方法。

```java
public class NativeDemo {
    static {
        System.loadLibrary("nativeDemo"); // 加载动态链接库
    }

    // 声明 native 方法
    public native void printMessage(String message);

    public static void main(String[] args) {
        NativeDemo demo = new NativeDemo();
        demo.printMessage("Hello from JNI!");
    }
}
```

2. 生成 C/C++ 头文件

使用 javah 工具从 Java 类生成 C/C++ 头文件。这一步骤会生成一个 `.h` 文件，其中包含了 native 方法的 C/C++ 版本声明。

```bash
javah -jni NativeDemo
```

3. 编写 C/C++ 代码

使用生成的头文件来编写 C/C++ 代码，实现 native 方法的功能。例如，创建一个名为 `NativeDemo.c` 的文件：

```c
#include <jni.h>
#include <stdio.h>
#include "NativeDemo.h"

JNIEXPORT void JNICALL Java_NativeDemo_printMessage(JNIEnv *env, jobject obj, jstring messageObj) {
    const char *message = (*env)->GetStringUTFChars(env, messageObj, NULL);
    printf("%s\n", message);
    (*env)->ReleaseStringUTFChars(env, messageObj, message);
}
```

4. 编译 C/C++ 代码

使用 C/C++ 编译器（如 gcc 或 clang）编译你的源代码文件，生成动态链接库。

```bash
gcc -shared -o libnativeDemo.so NativeDemo.c -I/usr/lib/jvm/java-1.8.0-openjdk/include -I/usr/lib/jvm/java-1.8.0-openjdk/include/linux
```

对于 Windows，你可能需要使用 `cl.exe` 或其他 Windows 兼容的编译器，并且生成的库文件后缀为 `.dll`。

5. 在 Java 中调用 native 方法

现在，你可以在 Java 中运行你的程序。只要动态链接库在系统的库搜索路径中，或者你已经使用 System.setProperty("java.library.path", "path/to/your/library") 设置了正确的路径，Java 应用程序就可以调用 native 方法了。
