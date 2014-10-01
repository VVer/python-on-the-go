python下使用protobuf
====
1.下载protobuf编译器protoc<br/>
<p>如果是windows平台，可以直接到http://code.google.com/p/protobuf/downloads/ 下载编译好的版本,如protoc-2.6.0-win32.zip，将protoc解压到目的目录，然后将该目录加到环境变量中去。</p>

2.python安装protobuf库
```Bash
    easy_install protobuf
```
3.定义一个protobuf文件，如下所示
```c
message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phone = 4;
}

message AddressBook {
  repeated Person person = 1;
}
```

