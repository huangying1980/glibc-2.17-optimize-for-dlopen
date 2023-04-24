# glibc-2.17-optimize-for-dlopen
## 背景
* 在实际项目中使用了dlopen来加载动态库
* 在调用动态库中的函数时，有如下两种情况影响了项目在刚启动后对业务数据处理的性能
  * 在动态库没有在page cache中时会触发major-faults
  * 在动态库已经在page cache中时会触发minor-faults
* 为了提高刚启动时的性能，需要避免major-faults和minor-faults,所以对glibc中的加载器相关的代码进行了简单优化
## 优化原理
* 在加载elf的代码中用的是mmap实现的加载动态库，但此时并没有真正读入动态库文件的内容且又没在page cache中所以会触发major-faults，如果在page cache中，但此时并没有真正的分配物理内存所以会触发minor-faults。于是对mmap的参数进行调整加入MAP_PRIVATE和MAP_POPULATE
* 为了不影响系统其他程序的正常运行，则不能替换系统的libc库和加载器，所以用命令patchelf对目标程序进行加载器和rpath的修改
