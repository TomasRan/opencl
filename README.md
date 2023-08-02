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

<br>

### 2. clGetPlatformInfo

**描述：** 获取平台名称、平台支持的OpenCL版本、平台供应商等等平台属性信息。

**函数原型：**

```cpp
cl_int clGetPlatformInfo(cl_platform_id   platform,
                         cl_platform_info param_name,
                         size_t           param_value_size,
                         void             *param_value,
                         size_t           *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|platform|cl_platform_id|指定查询的OpenCL平台|
|param_name|cl_platform_info|执行需要查询的OpenCL平台属性信息，取值如下：\
<li>CL_PLATFORM_PROFILE：识别平台是支持FULL_PROFILE还是EMBEDDED_PROFILE</li><li>CL_PLATFORM_VERSION：返回支持的最大OpenCL版本</li><li>CL_PLATFORM_NAME：返回OpenCL平台的名称</li><li>CL_PLATFORM_VENDOR：返回OpenCL平台开发商的名称</li><li>CL_PLATFORM_EXTENSIONS：返回OpenCL平台支持的扩展名列表</li>|
|param_value_size|size_t|指定查询结果的缓冲区字节大小|
|param_value|void*|保存平台属性信息的缓冲区|
|param_value_size_ret|size_t*|返回属性信息的实际长度|

FULL_PROFILE 和 EMBEDDED_PROFILE 的区别：
- FULL_PROFILE：OpenCL实现支持OpenCL规范的所有功能。
- EMBEDDED_PROFILE：OpenCL实现支持OpenCL嵌入式简档，是OpenCL规范的一个子集。

**调用示例：**

```cpp
// 获取OpenCL平台名称
size_t name_size = 0;
cl_int err = clGetPlatformInfo(platforms[0], CL_PLATFORM_NAME, 0, NULL, &name_size);
char *name_str = (char*)malloc(name_size);
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_NAME, name_size, name_str, NULL);
printf("CL_PLATFORM_NAME: %s\n", name_str);

// 获取OpenCL供应商名称
size_t vendor_size = 0;
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_VENDOR, 0, NULL, &vendor_size);
char *vendor_str = (char*)malloc(vendor_size);
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_VENDOR, vendor_size, vendor_str, NULL);
printf("CL_PLATFORM_VENDOR: %s\n", vendor_str);

// 获取支持的最大OpenCL版本
size_t version_size = 0;
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_VERSION, 0, NULL, &version_size);
char *version_str = (char*)malloc(version_size);
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_VERSION, version_size, version_str, NULL);
printf("CL_PLATFORM_VERSION: %s\n", version_str);

// 识别是FULL_PROFILE还是EMBEDDED_PROFILE
size_t profile_size = 0;
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_PROFILE, 0, NULL, &profile_size);
char *profile_str = (char*)malloc(profile_size);
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_PROFILE, profile_size, profile_str, NULL);
printf("CL_PLATFORM_PROFILE: %s\n", profile_str);

// 获取OpenCL平台的扩展名称
size_t extension_size = 0;
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_EXTENSIONS, 0, NULL, &extension_size);
char *extension_str = (char*)malloc(extension_size);
err = clGetPlatformInfo(platforms[0], CL_PLATFORM_EXTENSIONS, extension_size, extension_str, NULL);
printf("CL_PLATFORM_EXTENSIONS: %s\n", extension_str);
```

**示例输出：**

```cpp
CL_PLATFORM_NAME: QUALCOMM Snapdragon(TM)
CL_PLATFORM_VENDOR: QUALCOMM
CL_PLATFORM_VERSION: OpenCL 3.0 QUALCOMM build: commit #5e81ec0141 changeid #Icc5bd9b9d5 Date: 11/07/22 Mon Local Branch:  Remote Branch: 
CL_PLATFORM_PROFILE: FULL_PROFILE
CL_PLATFORM_EXTENSIONS: 
```
