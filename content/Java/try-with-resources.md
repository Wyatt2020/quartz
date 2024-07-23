---
created: 2024-07-04 11:21
modified: 2024-07-04 11:21
tags:
  - Java
---

### 自动关闭资源

`try-with-resources` 是 Java 7 引入的一个特性，用于自动管理资源，确保在程序执行完毕后，无论是否发生异常，都会正确关闭或释放资源。它简化了之前需要在 `finally` 块中手动关闭资源的代码。下面通过一个具体的例子来说明 `try-with-resources` 的使用方法：

假设我们要从一个文件中读取内容，并打印出来，之后确保文件流被正确关闭。没有使用 `try-with-resources` 时，代码可能如下：

```java
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("example.txt"));
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}
```

使用 `try-with-resources` 重写上面的代码，可以简化为：

```java
try (BufferedReader reader = new BufferedReader(new FileReader("example.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

在这个例子中，`BufferedReader` 实例 `reader` 在 `try` 语句的大括号中声明。当离开 `try` 语句块的范围时（不论是正常执行结束还是因为异常退出），Java 会自动调用 `reader` 的 `close()` 方法来关闭文件流。这样就不需要显式地在 `finally` 块中关闭资源，使得代码更简洁且减少了出错的可能性。

### 与 finally 结合

`try-with-resources` 语句主要用于*自动关闭资源*，但这并不妨碍您在 `finally` 块中执行其他必要的清理或收尾操作。实际上，`finally` 块依然可以用来执行那些不能或不应该由 `try-with-resources` 管理的资源释放逻辑。下面是一个结合了 `try-with-resources` 和 `finally` 块的例子：

```java
// 假设我们有两个需要管理的资源，一个是文件读取，另一个是数据库连接
try (
    BufferedReader reader = new BufferedReader(new FileReader("example.txt"));
    Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb", "username", "password")
) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
        
        // 假设还有一些使用connection的操作...
        // Statement stmt = connection.createStatement();
        // ...
    }
} catch (IOException | SQLException e) { // 处理两种类型的异常
    e.printStackTrace();
} finally {
    // 在这里执行其他必要的清理操作，比如记录操作日志，或者执行非自动管理的资源释放
    System.out.println("执行一些最终的清理操作...");
    // 注意：由于使用了 try-with-resources，BufferedReader 和 Connection 会自动关闭，无需在此手动关闭
}
```

在这个例子中，`BufferedReader` 和 `Connection` 都会在 `try-with-resources` 结束时自动关闭。==而在 `finally` 块中，您可以添加任何需要的额外清理逻辑，比如记录操作完成的日志信息，或者关闭那些没有实现 `AutoCloseable` 接口、因而不能放在 `try-with-resources` 语句中的资源。==