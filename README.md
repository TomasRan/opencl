# OpenCL使用

## OpenCL编程接口说明

### 1. clGetPlatformIDs

**描述：** 查询系统可用的OpenCL平台列表。

**函数原型：**

```cpp
cl_int clGetPlatformIDs(cl_uint        num_entries,
                        cl_platform_id *platforms,
                        cl_uint        *num_platforms);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|num_entries|cl_uint|需要初始化的OpenCL平台数量 |
|platforms|cl_platform_id|OpenCL平台ID列表|
|num_platforms|cl_uint|当前系统支持的OpenCL平台数量|

**调用示例：**

```cpp
cl_uint num_platform;
cl_platform_id *platform = NULL;

// 获取当前系统支持的OpenCL平台数量
cl_int err = clGetPlatformIDs(0, NULL, &num_platform);

// 初始化当前的OpenCL平台
err = clGetPlatformIDs(num_platform, platform, NULL);
```