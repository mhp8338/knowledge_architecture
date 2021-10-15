# Java I/O的工作机制

## Java IO的基本架构

- 基于字节操作的IO接口：InputStream & OutputStream
- 基于字符操作的IO接口：Writer & Reader
- 基于磁盘操作的IO接口：File
- 基于网络操作的IO接口：Socket

### 基于字节操作的IO接口

注意两点：

- 可以组合
- 必须指定流向

