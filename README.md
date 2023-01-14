# rcore-record
Rcore study record
### 第一周 11.6
开营前做完了rustlings， 这周看了一下《RISC-V手册：一本开源指令集的指南》， 根据教程在docker中配置好了环境，make test1可以正常通过，大致看了一下test1和test2教程指导，准备开始下一步学习。

### 第五周 12.4

一个月没有更新， 我有罪。

总之之前出了些事情，导致没有什么时间看。（hr演我，可恶），大概半个月前写完lab1后，这周花了两天时间写完了lab2。

主要是阅读代码比较深入地理解了一下，page_table、memory_set、frame_tracker、frame_alloc和taskcontrolblock之间的交换关系（此处应该有一张图，菜狗先TODO吧），通过sys_write理解内存通过使用current_token将用户地址翻译后再执行操作的方式。然后开写了lab2，是真的拉跨，编写+调试搞了一天多。主要调试内容包括 

1、mmap时，使用find_pte，已经分配的第三级页表时，查询临近页可能返回Some(pte)需要同is_valid()判断一下。 

2、MapPermission和port中排列不同，不要随便复制粘贴 （这个导致两个有mmap后然后write的ut，直接hang住了，感觉可以增加一点提示TODO）

3、translated_ptr推荐使用模板 

4、页表不足一页的地方如何处理，需要阅读具体代码。

### 第六周 12.13

lab3内容相比而言整体结构改动较小，主要将TaskManager功能下放到TCB，TaskManager只保留了ready_queue的功能。

整体流程比较清晰， 感觉可以比较注意的是，完成初始化的进程，通过设置trap_cx，通过trap_cx设置堆栈、a0、entry_point，__switch后以trap_return 的方式回到用户态进行。以及 fork 通过设置 新task的a0为0，使if(fork()==0)进入不同分支。

lab内容较为清晰， spawn只要是模仿fork+exec代码， stripe算法也主要集中在tcb和run task中。

### 第七周 12.21

文件系统这一章整体感觉整体复杂一点，需要理清楚inode、disk_inode、dirent和data_blocks之间的关系，已经真的操作系统抽象出来的efs如何管理操作系统磁盘空间。  相对而言， 理解整体架构会比前两章困难一些。 另外感觉这一章的代码也有挺多的可以学习的地方，比如文件操作中如何通过泛型和闭包将字符串转为类型进行操作。

lab4的主要流程时实现link操作， 因为dirent的设置关系，保留指向文件不是很方便，所以直接将dirent中的inode直接指向了目标源文件的inode（多盘link直接寄了），另外将nlink直接保存在disknode中，方便操作和读取，unlink中对disknode和stat的操作中问了一点问题，调试了很久，unlink中需要对所在文件夹的dirent进行清理，然后本实验test中，unlink自身可以对文件进行清理，需要主要，不过因为实验中对inode和block的gc没有更进一步要求，也没有做更详细的处理。

### 第xxx周 1.14

终于考完了试和糊完了大作业。

管道一章的主要内容是通过fd的前三项和类ring buffer的管道，实现重定向的功能，以及拓展了sys_exec参数，执行中可以通过字符串传参（主要需要对split后多个参数的字符串需要压内核栈进行了一些处理。

并发章节中首先引入了线程机制，将process中的trap_ctx等和任务执行相关的模块下方到线程的TaskControlBlock中（当然为了manager调度，tcb中需要放入process的weak引用），process中管理多个线程，manager中还是只保留add_task和fetch_task功能。

并发的同步互斥机制主要将mutexlock、semaphore和cond_var等放入ProcessControlBlock中，在ProcessControlBlock维护其相关结构，比较需要理清的地方在于，lock、sema_down和cond_wait的阻塞，都是通过讲自身task的引用放入wait_queue中，然后执行block_current_and_run_next来完成。在条件满足时，unlock、sema_up和cond_signal会从wait_queue中pop出task，然后通过add_task加入系统wait_queue中。

lab5的代码，个人感觉主要点在于，因为尝试获取资源然后blocking的时候只设置need，获取到资源后再配置alloc。 结合上文中讲得调度策略设置alloc、need，以及在tcb::new和exit_current_and_run_next时进行算法线程空间的分配和回收，可以比较清晰地完成lab5。（但是被copilot的假算法补全狠狠搞了一手）
