



# 概述

  高通发布的FastCV是一个针对移动平台的计算机视觉库，如今的版本支持运行在windows、Mac和Linux等大部分平台，并对于高通自家的Snapdragon做了特别优化的。FastCV提了为移动设备优化的计算机视觉（CV）库，包括移动设备广泛使用的视觉处理函数和大众市场手机。中间件开发人员可以使用FastCV构建框架开发人员所需要的计算机视觉用用，高通的AR（增强现实）SDK就是一个采用了FastCV的很好的例子。CV应用的开发者们在开发他们的应用时可以直接使用FastCV函数。





# 使用指南



初始化
```
// 启用 FastCV 时需要初始化模式和内存  
#ifdef BUILD_FASTCV  
  // @see fastcv.h -> fcvOperationMode  
 int ret = fcvSetOperationMode(FASTCV_OP_CPU_OFFLOAD); if (ret != 0) { VLOGE(TAG, "Fail to fcvSetOperationMode, return code is %d", ret); } fcvMemInit();#endif

```


反初始化
```
fcvMemDeInit();  
fcvCleanUp();
```



