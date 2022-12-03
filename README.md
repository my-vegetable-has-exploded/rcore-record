# rcore-record
Rcore study record
### 第一周 11.6
开营前做完了rustlings， 这周看了一下《RISC-V手册：一本开源指令集的指南》， 根据教程在docker中配置好了环境，make test1可以正常通过，大致看了一下test1和test2教程指导，准备开始下一步学习。

### 五周 12.4

一个月没有更新， 我有罪。

总之之前出了些事情，导致没有什么时间看。（hr演我，可恶），大概半个月前写完lab1后，这周花了两天时间写完了lab2。

主要是阅读代码比较深入地理解了一下，page_table、memory_set、frame_tracker、frame_alloc和taskcontrolblock之间的交换关系（此处应该有一张图，菜狗先TODO吧），通过sys_write理解内存通过使用current_token将用户地址翻译后再执行操作的方式。然后开写了lab2，是真的拉跨，编写+调试搞了一天多。主要调试内容包括 

1、mmap时，使用find_pte，已经分配的第三级页表时，查询临近页可能返回Some(pte)需要同is_valid()判断一下。 

2、MapPermission和port中排列不同，不要随便复制粘贴 

3、translated_ptr推荐使用模板 

4、页表不足一页的地方如何处理，需要阅读具体代码。
