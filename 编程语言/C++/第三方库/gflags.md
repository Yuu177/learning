[TOC]

# gflags

Gflags 是一种命令行解析工具，主要用于解析用命令行执行可执行文件时传入的参数。

## 支持的参数类型

gflags 支持的类型有 `bool`，`int32`，`int64`，`uint64`，`double` 和 `string`。

分别对应 `DEFINE_bool`、`DEFINE_int32`、`DEFINE_int64`、`DEFINE_uint64`、`DEFINE_double`、`DEFINE_string`。

举个例子：

```c++
DEFINE_int32(port, 9090, "program listen port");
```

第一个参数 `port` 就是命令行里要输入的参数名，比如 `–port=8080`。gflags 需要参数以特定的格式才能解析。这里如果写为 `port=8080` 就不能解析到正确的值。

第二个参数 `9090`，表示默认值就是 9090。

第三个参数 `program listen port`，就是这个参数的帮助说明信息，当用户输入 `–help` 的时候，会显示出来。

- 代码中使用这个变量

`DEFINE_int32` 等代码就相当于定义了变量，只不过变量名多了个前缀 `FLAGS_`。即我们可以在代码里面直接操作 `FLAGS_port` 这个变量

## 解析命令行参数

gflags 是使用 `ParseCommandLineFlags` 这个方法来完成命令行参数的解析的。具体如下：

```c++
gflags::ParseCommandLineFlags(&argc, &argv, true);
```

这里需要注意的是第三个参数，说明如下：

| 变量值 | 说明                                                     |
| ------ | -------------------------------------------------------- |
| true   | 函数处理完成后，argv 中只保留 argv[0]，argc 会被设置为 1 |
| false  | argv 和 argc 会被保留，但是注意函数会调整 argv 中的顺序  |

- argc 和 argv

**argc **为 argument count  的缩写，代表参数的个数。

**argv **为 argument vector 的缩写，可以理解成参数序列。`argv[0]` 为执行程序执行路径。

- 代码实例

```C++
#include "gflags/gflags.h"
#include <iostream>

using namespace std;

DEFINE_int32(port, 9090, "program listen port"); // 定义输入的参数类型

int main(int argc, char *argv[]) {
    for (int i = 0; i < argc; i++) {
      printf("argv[%d] = %s\n", i, argv[i]);
    }
    cout << endl;
    GFLAGS_NAMESPACE::ParseCommandLineFlags(&argc, &argv, true);  // use of gflags
    cout << FLAGS_port << endl;  // 打印传入的 port 参数，需要添加前缀 FLAGS_
    return 0;
}
```

执行 `./bin/a.out -port=8080`，结果输出：

```
argv[0] = ./bin/a.out
argv[1] = -port=8080

8080
```

