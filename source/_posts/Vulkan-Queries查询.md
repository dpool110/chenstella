---
title: Vulkan Queries查询
tags:
  - Vulkan
cover: 'http://img.dpool.love/202312071710859.png'
abbrlink: 36e2fc60
date: 2023-09-27 14:16:08
---

# Vulkan Queries查询

{% tip info %}

本文内容翻译自Vulkan官网相关文章

{% link <span style="color:black">Vulkan Documentation Project Demo</span>, https://registry.khronos.org/vulkan/site/spec/latest/chapters/queries.html#queries-occlusion, https://registry.khronos.org/vulkan/site/_/Vulkan_White_Dec16.svg %}

{% endtip %}

---

查询提供了一种机制来返回有关处理一系列 Vulkan 命令的信息。查询操作是异步的，因此不会立即返回其结果。相反，其结果和可用性状态存储在查询池中。这些查询的状态可以在主机上读回，也可以复制到设备上的缓冲区对象。

支持的查询类型包括遮挡查询、管道统计信息查询、结果状态查询、视频编码反馈查询和时间戳查询。如果关联的扩展可用，则支持性能查询。如果关联的扩展可用，则支持转换反馈查询。如果关联的扩展可用，则支持英特尔性能查询。如果关联的扩展可用，则支持网格着色器查询。

如果支持相应的扩展，则可以使用与光线追踪关联的具有特定用途的几个其他查询，如 VkQueryType 中所述。

## Query Pools 查询池

查询使用查询池对象进行管理。每个查询池都是特定类型的特定数量的查询的集合。

查询池由句柄表示 `VkQueryPool` ：

```c++
// Provided by VK_VERSION_1_0
VK_DEFINE_NON_DISPATCHABLE_HANDLE(VkQueryPool)
```

> `VK_DEFINE_NON_DISPATCHABLE_HANDLE`是Vulkan API中的一个宏定义，用于定义非派发句柄。Vulkan中有两种类型的句柄：派发句柄和非派发句柄。
>
> 1. **派发句柄 (Dispatchable Handles)**: 这些句柄具有足够的信息（通常是一个指针）来决定对哪个实际的Vulkan对象进行操作。例如，`VkDevice` 和 `VkInstance` 是派发句柄。
>
> 2. **非派发句柄 (Non-Dispatchable Handles)**: 这些句柄不包含上述信息。它们只是对象的唯一标识符。例如，`VkSemaphore`、`VkFence` 和 `VkQueryPool` 都是非派发句柄。
>
> `VK_DEFINE_NON_DISPATCHABLE_HANDLE` 宏的作用是为非派发句柄定义一个适当的类型。具体的实现可能会根据平台和编译器选项而有所不同，但通常它定义的类型是一个整数或指针。
>
> 当你看到 `VK_DEFINE_NON_DISPATCHABLE_HANDLE(VkQueryPool)` 时，它定义了一个名为 `VkQueryPool` 的类型，该类型表示一个查询池的非派发句柄。查询池是Vulkan中用于管理查询对象的资源，这些对象可用于查询关于渲染或计算操作的信息，例如时间戳或遮挡查询。
>
> 总之，这个宏的主要目的是为Vulkan的非派发句柄提供类型定义，从而使API更加类型安全。

若要创建查询池，调用`vkCreateQueryPool`：

```c++
// Provided by VK_VERSION_1_0
VkResult vkCreateQueryPool(
    VkDevice                                    device,
    const VkQueryPoolCreateInfo*                pCreateInfo,
    const VkAllocationCallbacks*                pAllocator,
    VkQueryPool*                                pQueryPool);
```

1. **`VkDevice device`**: 这是一个派发句柄，表示要创建查询池的设备。
2. **`const VkQueryPoolCreateInfo* pCreateInfo`**: 这是一个指向 `VkQueryPoolCreateInfo` 结构的指针，该结构描述了查询池的创建信息。该结构中的字段指定了查询池的类型（例如时间戳、遮挡查询或管道统计查询）以及要在池中分配的查询数。
3. **`const VkAllocationCallbacks* pAllocator`**: 如果你想使用自定义的内存分配回调来管理Vulkan对象的内存分配和释放，你可以提供一个指向 `VkAllocationCallbacks` 结构的指针。如果你不需要自定义的内存分配策略，可以简单地传递 `nullptr`。
4. **`VkQueryPool* pQueryPool`**: 这是一个指向 `VkQueryPool` 句柄的指针。如果函数成功执行，该句柄将被填充，代表新创建的查询池。

该 `VkQueryPoolCreateInfo` 结构定义为：

{% folding blue, 使用要求 %}

VUID-vkCreateQueryPool-device-parameter
 `device` 必须是有效的 VkDevice 句柄

 VUID-vkCreateQueryPool-pCreateInfo-parameter
 `pCreateInfo` 必须是指向有效 VkQueryPoolCreateInfo 结构的有效指针

 VUID-vkCreateQueryPool-pAllocator-parameter
如果不是 `NULL` ， `pAllocator` 则必须 `pAllocator` 是指向有效 VkAllocationCallbacks 结构的有效指针

 VUID-vkCreateQueryPool-pQueryPool-parameter
 `pQueryPool` 必须是指向 VkQueryPool 句柄的有效指针

{% endfolding %}

```c++
// Provided by VK_VERSION_1_0
typedef struct VkQueryPoolCreateInfo {
    VkStructureType                  sType;
    const void*                      pNext;
    VkQueryPoolCreateFlags           flags;
    VkQueryType                      queryType;
    uint32_t                         queryCount;
    VkQueryPipelineStatisticFlags    pipelineStatistics;
} VkQueryPoolCreateInfo;
```

1. **`VkStructureType sType`**: 这个字段描述了结构体的类型。对于这个结构，它应该被设置为`VK_STRUCTURE_TYPE_QUERY_POOL_CREATE_INFO`。

2. **`const void\* pNext`**: 这是一个指向扩展或自定义结构的指针。通常，在使用标准Vulkan功能时，这个字段应该设置为`nullptr`。但是，当使用某些Vulkan扩展时，`pNext`可以用于指向一个扩展特定的结构。

3. **`VkQueryPoolCreateFlags flags`**: 这是用于查询池创建的标志。目前，Vulkan规范中此字段是为将来的用途保留的，所以它应该被设置为0。

4. **`VkQueryType queryType`**: 这个字段描述了查询池中查询的类型。其中的值包括：
   {% folding blue, VkQueryType 可用的值 %}

   `VK_QUERY_TYPE_OCCLUSION`: 用于遮挡查询。

   `VK_QUERY_TYPE_PIPELINE_STATISTICS`: 用于获取与图形或计算管道相关的统计信息。

   `VK_QUERY_TYPE_TIMESTAMP`: 用于获取命令的执行时间戳。

   `VK_QUERY_TYPE_PERFORMANCE_QUERY_KHR` 指定性能查询。

   `VK_QUERY_TYPE_TRANSFORM_FEEDBACK_STREAM_EXT` 指定转换反馈查询。

   `VK_QUERY_TYPE_PRIMITIVES_GENERATED_EXT` 指定基元生成的查询。

   `VK_QUERY_TYPE_ACCELERATION_STRUCTURE_COMPACTED_SIZE_KHR` 指定用于 vkCmdWriteAccelerationStructuresPropertiesKHR 或 vkWriteAccelerationStructuresPropertiesKHR 的加速结构大小查询。

   `VK_QUERY_TYPE_ACCELERATION_STRUCTURE_SERIALIZATION_SIZE_KHR` 指定序列化加速结构大小查询。

   `VK_QUERY_TYPE_ACCELERATION_STRUCTURE_SIZE_KHR` 指定用于 vkCmdWriteAccelerationStructuresPropertiesKHR 或 vkWriteAccelerationStructuresPropertiesKHR 的加速结构大小查询。

   `VK_QUERY_TYPE_ACCELERATION_STRUCTURE_SERIALIZATION_BOTTOM_LEVEL_POINTERS_KHR` 指定序列化加速结构指针计数查询。

   `VK_QUERY_TYPE_ACCELERATION_STRUCTURE_COMPACTED_SIZE_NV` 指定用于 vkCmdWriteAccelerationStructuresPropertiesNV 的加速结构大小查询。

   `VK_QUERY_TYPE_PERFORMANCE_QUERY_INTEL` 指定英特尔性能查询。

   `VK_QUERY_TYPE_RESULT_STATUS_ONLY_KHR` 指定结果状态查询。

   `VK_QUERY_TYPE_VIDEO_ENCODE_FEEDBACK_KHR` 指定视频编码反馈查询。

   `VK_QUERY_TYPE_MESH_PRIMITIVES_GENERATED_EXT` 指定生成的网格基元查询。
   {% endfolding %}

5. **`uint32_t queryCount`**: 这个字段指定了在查询池中需要的查询数量。

6. **`VkQueryPipelineStatisticFlags pipelineStatistics`**: 如果`queryType`被设置为`VK_QUERY_TYPE_PIPELINE_STATISTICS`，这个字段描述了哪些管道统计信息应该被收集。这是一个位掩码，其中可能的值包括输入装配顶点数、片段着色器调用数等。

`pipelineStatistics` 如果不是 `VK_QUERY_TYPE_PIPELINE_STATISTICS` ，则 `queryType` 忽略 

 	

```c++
// Provided by VK_VERSION_1_0
typedef VkFlags VkQueryPoolCreateFlags;
```

 `VkQueryPoolCreateFlags` 是用于设置掩码的位掩码类型，但当前保留供将来使用。

该 `VkQueryPoolPerformanceCreateInfoKHR` 结构定义为：

```c++
// Provided by VK_KHR_performance_query
typedef struct VkQueryPoolPerformanceCreateInfoKHR {
    VkStructureType    sType;
    const void*        pNext;
    uint32_t           queueFamilyIndex;
    uint32_t           counterIndexCount;
    const uint32_t*    pCounterIndices;
} VkQueryPoolPerformanceCreateInfoKHR;
```

{% folding blue, VkQueryPoolPerformanceCreateInfoKHR 成员 %}

1. **`VkStructureType sType`**: 这个字段描述了结构体的类型。对于这个结构，它应该被设置为`VK_STRUCTURE_TYPE_QUERY_POOL_CREATE_INFO`。
2. **`const void\* pNext`**: 这是一个指向扩展或自定义结构的指针。通常，在使用标准Vulkan功能时，这个字段应该设置为`nullptr`。但是，当使用某些Vulkan扩展时，`pNext`可以用于指向一个扩展特定的结构。
3. **`VkQueryPoolCreateFlags flags`**: 这是用于查询池创建的标志。目前，Vulkan规范中此字段是为将来的用途保留的，所以它应该被设置为0。
4. **`VkQueryType queryType`**: 这个字段描述了查询池中查询的类型。可能的值包括：
   - `VK_QUERY_TYPE_OCCLUSION`: 用于遮挡查询。
   - `VK_QUERY_TYPE_PIPELINE_STATISTICS`: 用于获取与图形或计算管道相关的统计信息。
   - `VK_QUERY_TYPE_TIMESTAMP`: 用于获取命令的执行时间戳。
5. **`uint32_t queryCount`**: 这个字段指定了在查询池中需要的查询数量。
6. **`VkQueryPipelineStatisticFlags pipelineStatistics`**: 如果`queryType`被设置为`VK_QUERY_TYPE_PIPELINE_STATISTICS`，这个字段描述了哪些管道统计信息应该被收集。这是一个位掩码，其中可能的值包括输入装配顶点数、片段着色器调用数等。

当你想创建一个新的`VkQueryPool`时，你需要填充这个结构体并传递给`vkCreateQueryPool`函数。确保正确设置每个字段，以便查询池按照你的期望进行创建。

{% endfolding %}



