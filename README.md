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

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

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
|param_name|cl_platform_info|执行需要查询的OpenCL平台属性信息，取值范围详见下文|
|param_value_size|size_t|指定查询结果的缓冲区字节大小|
|param_value|void*|保存平台属性信息的缓冲区|
|param_value_size_ret|size_t*|返回属性信息的实际长度|

cl_platform_info取值范围：

- CL_PLATFORM_PROFILE：识别平台是支持 FULL_PROFILE 还是 EMBEDDED_PROFILE
- CL_PLATFORM_VERSION：返回支持的最大OpenCL版本
- CL_PLATFORM_NAME：返回OpenCL平台的名称
- CL_PLATFORM_VENDOR：返回OpenCL平台开发商的名称
- CL_PLATFORM_EXTENSIONS：返回OpenCL平台支持的扩展名列表

**FULL_PROFILE** 和 **EMBEDDED_PROFILE** 的区别：

- FULL_PROFILE：OpenCL实现支持OpenCL规范的所有功能。
- EMBEDDED_PROFILE：OpenCL实现支持OpenCL嵌入式简档，是OpenCL规范的一个子集。

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

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

<br>

### 3. clGetDeviceIDs

**描述：** 获取指定OpenCL平台支持的OpenCL设备列表（一个平台可能关联不同的设备）。

**函数原型：**

```cpp
cl_int clGetDeviceIDs(cl_platform_id  platform,
                      cl_device_type  device_type,
                      cl_uint         num_entries,
                      cl_device_id    *devices,
                      cl_uint         *num_devices);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|platform|cl_platform_id|指定的OpenCL平台|
|device_type|cl_device_type|OpenCL设备类型，支持类型详见下文|
|num_entries|cl_uint|指定devices指向的设备列表中最多可以存储的设备对象|
|devices|cl_device_id*|设备列表|
|num_devices|cl_uint*|输出实际给出的设备对象个数|

cl_device_type 取值范围：

- CL_DEVICE_TYPE_CPU：CPU作为OpenCL设备
- CL_DEVICE_TYPE_GPU：GPU作为OpenCL设备
- CL_DEVICE_TYPE_ACCELERATOR：计算加速器作为OpenCL设备，设备通过PCIe与主机通信
- CL_DEVICE_TYPE_CUSTOM：不支持OpenCL C编程的计算设备
- CL_DEVICE_TYPE_DEFAULT：系统默认OpenCL设备，不能是CL_DEVICE_TYPE_CUSTOM
- CL_DEVICE_TYPE_ALL：除了CL_DEVICE_TYPE_CUSTOM之外的所有OpenCL设备

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：**

```cpp
cl_device_id *devices = NULL;
cl_uint num_device = 0;

// 获取指定平台连接的OpenCL设备数量
err = clGetDeviceIDs(platforms[0], CL_DEVICE_TYPE_GPU, 0, NULL, &num_device);

// 获取指定平台连接的OpenCL设备列表
devices = (cl_device_id*)malloc(sizeof(cl_device_id) * num_device);
err = clGetDeviceIDs(platforms[0], CL_DEVICE_TYPE_GPU, num_device, devices, NULL);
```

<br>

### 4. clGetDeviceInfo

**描述：** 获取指定OpenCL设备的属性信息。

**函数原型：**

```cpp
cl_int clGetDeviceInfo(cl_device_id     device,
                       cl_device_info   param_name,
                       size_t           param_value_size,
                       void             *param_value,
                       size_t           *param_value_size_ret)
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|device|cl_device_id|指定的OpenCL设备|
|param_name|cl_device_info|指定查询的OpenCL设备属性信息|
|param_value_size|size_t|指定param_value指向的内存空间大小|
|param_value|void*|保存平台属性信息的指针|
|param_value_size_ret|size_t|属性信息的实际长度|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充

<br>

### 5. clCreateContext

**描述：** 根据给定的设备创建上下文。

**函数原型：**

```cpp
cl_context clCreateContext(const cl_context_properties *properties,
                           cl_uint num_devices,
                           const cl_device_id *devices,
                           void (CL_CALLBACK *pfn_notify)(const char *, const void *, size_t, void *),
                           void *user_data,
                           cl_int *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|properties|const cl_context_properties*|指定上下文属性名称和属性相应的值列表|
|num_devices|cl_uint|OpenCL设备数量|
|devices|const cl_device_id*|OpenCL设备列表|
|pfn_notify|void (*pfn_notify)(const char*, const void*, size_t, void*)|回调函数，报告上下文生命周期中出现错误的有关信息|
|user_data|void*|待补充|
|errcode_ret|cl_int*|函数的状态返回码|

**返回值：** 创建完成的上下文环境。

**调用示例：**

```cpp
cl_context_properties properties[] = {CL_CONTEXT_PLATFORM, (cl_context_properties)platforms[0], 0};
cl_context context = clCreateContext(properties, num_device, devices, NULL, NULL, &err);
```

<br>

### 6. clCreateContextFromType

**描述：** 根据给定的设备类型创建上下文。

**函数原型：**

```cpp
cl_context clCreateContextFromType(const cl_context_properties *properties,
                                   cl_device_type device_type,
                                   void (CL_CALLBACK *pfn_notify)(const char *, const void *, size_t, void *),
                                   void *user_data,
                                   cl_int *errcode_ret);
```

待补充

<br>

### 7. clGetContextInfo

**描述：** 查询上下文各个属性信息。

**函数原型：**

```cpp
cl_int clGetContextInfo(cl_context      context,
                            cl_context_info param_name,
                            size_t          param_value_size,
                            void            *param_value,
                            size_t          *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|
|context|cl_context|上下文环境|
|param_name|cl_context_info|查询的属性名称|
|param_value_size|size_t|指定param_value指向的内存空间字节大小|
|param_value|void*|存储上下文属性信息的指针|
|param_value_size_ret|size_t*|实际属性信息的字节大小|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 8. clRetainContext

**描述：** 增加上下文对象的引用计数。

**函数原型：**

```cpp
cl_int clRetainContext(cl_context context);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 9. clReleaseContext

**描述：** 减少上下文对象的引用计数。

**函数原型：**

```cpp
cl_int clReleaseContext(cl_context context);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 10. clCreateCommandQueueWithProperties

**描述：** 创建命令队列。

**函数原型：**

```cpp
cl_command_queue clCreateCommandQueueWithProperties(
                                cl_context                  context,
                                cl_device_id                device,
                                const cl_queue_properties   *properties,
                                cl_int                      *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 返回创建的命令队列。

**调用示例：** 待补充。

<br>

### 11. clGetCommandQueueInfo

**描述：** 获取命令队列属性信息。

**函数原型：**

```cpp
cl_int clGetCommandQueueInfo(cl_command_queue       command_queue,
                             cl_command_queue_info  param_name,
                             size_t                 param_value_size,
                             void                   *param_value,
                             size_t                 *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 12. clRetainCommandQueue

**描述：** 增加命令队列引用计数（引用计数 +1）。

**函数原型：**

```cpp
cl_int clRetainCommandQueue(cl_command_queue command_queue);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 13. clReleaseCommandQueue

**描述：** 减少命令队列引用计数（引用计数 -1）。

**函数原型：**

```cpp
cl_int clReleaseCommandQueue(cl_command_queue command_queue);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 14. clCreateProgramWithSource

**描述：** 从源代码中创建程序对象。

**函数原型：**

```cpp
cl_program clCreateProgramWithSource(cl_context     context,
                                     cl_uint        count,
                                     const char     **strings,
                                     const size_t   *lengths,
                                     cl_int         *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 返回程序对象。

**调用示例：** 待补充。

<br>

### 15. clCreateProgramWithBinary

**描述：** 利用程序二进制来创建程序对象。

**函数原型：**

```cpp
cl_program clCreateProgramWithBinary(cl_context             context,
                                     cl_uint                num_devices,
                                     const cl_device_id     *device_list,
                                     const size_t           *lengths,
                                     const unsigned char    **binaries,
                                     cl_int                 *binary_status,
                                     cl_int                 *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 返回程序对象。

**调用示例：** 待补充。

<br>

### 16. clBuildProgram

**描述：** 构建程序对象。

**函数原型：**

```cpp
cl_int clBuildProgram(cl_program            program,
                      cl_uint               num_devices,
                      const cl_device_id    *device_list,
                      const char            *options,
                      void(CL_CALLBACK)(*pfn_notify)(cl_program program, void *user_data),
                      void                  *user_data);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 17. clCompileProgram

**描述：** 编译程序对象。

**函数原型：**

```cpp
cl_int clCompileProgram(cl_program            program,
                        cl_uint               num_devices,
                        const cl_device_id    *device_list,
                        const char            *options,
                        const cl_program      *input_header,
                        const char            **header_include_name,
                        void(CL_CALLBACK)(*pfn_notify)(cl_program program, void *user_data),
                        void                  *user_data);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 18. clLinkProgram

**描述：** 链接编译好的程序对象。

**函数原型：**

```cpp
cl_program clLinkProgram(cl_context            context,
                         cl_uint               num_devices,
                         const cl_device_id    *device_list,
                         const char            *options,
                         cl_uint               num_input_programs,
                         const cl_program      * input_programs,
                         void(CL_CALLBACK)(*pfn_notify)(cl_program program, void *user_data),
                         void                  *user_data,
                         cl_int                *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功编译好的程序对象。

**调用示例：** 待补充。

<br>

### 19. clGetProgramInfo

**描述：** 查询程序对象的属性信息。

**函数原型：**

```cpp
cl_int clGetProgramInfo(cl_program      program,
                        cl_program_info param_name,
                        size_t          param_value_size,
                        void            *param_value,
                        size_t          *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 20. clGetProgramBuildInfo

**描述：** 查询程序对象的编译信息。

**函数原型：**

```cpp
cl_int clGetProgramBuildInfo(cl_program             program,
                             cl_device_id           device,
                             cl_program_build_info  param_name,
                             size_t                 param_value_size,
                             void                   *param_value,
                             size_t                 *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 21. clRetainProgram

**描述：** 增加程序对象的引用计数（引用计数 +1）。

**函数原型：**

```cpp
cl_int clRetainProgram(cl_program program);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 22. clReleaseProgram

**描述：** 减少程序对象的引用计数（引用计数 -1）。

**函数原型：**

```cpp
cl_int clReleaseProgram(cl_program program);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 23. clUnloadPlatformCompiler

**描述：** 通知OpenCL实现编译器工作完成，释放编译器分配的资源。

**函数原型：**

```cpp
cl_int clUnloadPlatformCompiler(cl_platform_id platform);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 24. clCreateKernel

**描述：** 创建内核对象。

**函数原型：**

```cpp
cl_kernel clCreateKernel(cl_program program,
                         const char *kernel_name,
                         cl_int     *errcode_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 返回kernel对象。

**调用示例：** 待补充。

<br>

### 25. clCreateKernelsInProgram

**描述：** 创建内核对象。

**函数原型：**

```cpp
cl_kernel clCreateKernelsInProgram(cl_program   program,
                                   cl_uint      num_kernels,
                                   cl_kernel    *kernels,
                                   cl_uint      *num_kernels_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 返回kernel对象。

**调用示例：** 待补充。

<br>

### 26. clGetKernelInfo

<br>

### 27. clSetKernelArg

**描述：** 设置内核参数。

**函数原型：**

```cpp
cl_int clSetKernelArg(cl_kernel     kernel,
                      cl_uint       arg_index,
                      size_t        arg_size,
                      const void    *arg_value);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 28. clSetKernelArgSVMPointer

**描述：** 使用共享虚拟内存（SVM）的内核参数设置。

**函数原型：**

```cpp
cl_int clSetKernelArgSVMPointer(cl_kernel     kernel,
                                cl_uint       arg_index,
                                const void    *arg_value);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 29. clGetKernelInfo

**描述：** 查询内核对象的属性信息。

**函数原型：**

```cpp
cl_int clGetKernelInfo(cl_kernel        kernel,
                       cl_kernel_info   param_name,
                       size_t           param_value_size,
                       void             *param_value,
                       size_t           *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 30. clGetKernelWorkGroupInfo

**描述：** 查询特定于某个设备的内核对象属性信息。

**函数原型：**

```cpp
cl_int clGetKernelWorkGroupInfo(cl_kernel                   kernel,
                                cl_device_id                device,
                                cl_kernel_work_group_info   param_name,
                                size_t                      param_value_size,
                                void                        *param_value,
                                size_t                      *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>


### 31. clGetKernelArgInfo

**描述：** 查询内核函数参数的属性信息。

**函数原型：**

```cpp
cl_int clGetKernelArgInfo(cl_kernel             kernel,
                          cl_uint               arg_index,
                          cl_kernel_arg_info    param_name,
                          size_t                param_value_size,
                          void                  *param_value,
                          size_t                *param_value_size_ret);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 32. clRetainKernel

**描述：** 增加kernle的引用计数（引用计数 +1）。

**函数原型：**

```cpp
cl_int clRetainKernel(cl_kernel kernel);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 33. clReleaseKernel

**描述：** 减少kernle的引用计数（引用计数 -1）。

**函数原型：**

```cpp
cl_int clReleaseKernel(cl_kernel kernel);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>

### 34. clEnqueueNDRangeKernel

**描述：** 将内核函数提交到命令队列并执行。

**函数原型：**

```cpp
cl_int clEnqueueNDRangeKernel(cl_command_queue  command_queue,
                              cl_kernel         kernel,
                              cl_uint           work_dim,
                              const size_t      *global_work_offset,
                              const size_t      *global_work_size,
                              const size_t      *local_work_size,
                              cl_uint           num_events_in_wait_list,
                              const cl_event    *event_wait_list,
                              cl_event          *event);
```

**参数说明：**

|参数名|参数类型|说明|
|:--|:--|:--|

**返回值：** 成功返回 `CL_SUCCESS`，其他为异常。

**调用示例：** 待补充。

<br>