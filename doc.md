# 标签
标签是用来在消息的二进制格式中标识你的字段。在编码的时候，1到15的标签，字段类型加标签只需要占用一个字节，从16到2047的标签，字段类型加标签的编码需要占用两个字节 (you can find out more about this in Protocol Buffer Encoding).。所以建议把1-15的标签保留给在消息中使用频繁的字段，在1-15的范围里可以保留一些空间给将来可能需要添加的频繁使用的字段。

# 字段声明
- required
  
  一个结构良好的消息中，该字段有且只有一个
- optional
  
  一个结构良好的消息中，该字段可以可以没有，也可以有，但不能多于一个
  字段默认值可以作为消息描述的一部分
  ```cpp
  optional int32 result_per_page = 3 [default = 10];
  ```
  如果在消息描述时可选字段的默认值没有声明，默认值就是该类型的默认值：比如字符串，默认值就是空字符串。bool类型，默认值是false。数值类型，默认值是0。枚举类型，默认值就是枚举类型定义中的第一个值。因此在定义枚举类型时第一个值的设置必须小心。
- repeated

  一个结构良好的消息中，该字段可以出现多次（也可以是0次），重复字段的顺序会被保留下来 因为一些历史原因，repeated声明的标量数值字段无法高效的编码，通过使用[packed=true]选项来启用新代码对其进行高效编码。 
  ```cpp
  repeated int32 samples = 4 [packed=true];
  ```

# 枚举
枚举常量值必须是32位整形范围内的值。在一个枚举类型的定义中，可以为不同常量设置相同的值。不过需要在类型的定义中设置allow_alias 选项为true，否则编译器遇到别名会报错。
```cpp
enum EnumAllowingAlias {
    option allow_alias = true;
    UNKNOWN = 0;
    STARTED = 1;
    RUNNING = 1;
}
enum EnumNotAllowingAlias {
    UNKNOWN = 0;
    STARTED = 1;
    // RUNNING = 1;  // 如果不注释这行，编译其会发出一个警告信息
}
```

# 引入定义
通过引入其他.proto文件可以使用其他文件中定义的消息。在文件的头部添加一个引入声明即可引入其他.proto文件:
```cpp
import "myproject/other_protos.proto";
```

默认情况下，只有直接引入的.proto中的定义可以使用。然而，有时会移动.proto文件。这种情况下不需要把这个项目引入该文件的其他文件都更新，只需要在同一个地方创建一个同名文件通过import public语法将所有的引入操作转发到新文件。import public 可以传递依赖，将import public声明的文件中的内容传递给上层调用import的文件。
```cpp
// new.proto
// All definitions are moved here

// old.proto
// This is the proto that all clients are importing.
import public "new.proto";
import "other.proto";

// client.proto
import "old.proto";
// You use definitions from old.proto and new.proto, but not other.proto
```