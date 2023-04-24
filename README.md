# glibc-2.17-optimize-for-dlopen
## 背景
* 在实际项目中使用了dlopen来加载动态库
* 在调用动态库中的函数时，有如下两种情况影响了项目在刚启动后对业务数据处理的性能
  * 在动态库没有在page cache中时会触发major-faults
  * 在动态库已经在page cache中时会触发minor-faults
* 为了提高刚启动时的性能，需要避免major-faults和minor-faults,所以对glibc中的加载器相关的代码进行了简单优化
