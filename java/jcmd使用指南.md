# jcmd使用指南

## 概述

JDK1.7之后，新增了一个命令行工具jcmd，可用于查看java进程、导出堆／线程信息、支持JFR等。只可用于所需查看的JVM机器上，且必须保证用户和组相同。

## 使用示例

* 查看所有Java进程
  * `jcmd`: 类似于`jps -m`，列出所有Java进程、主类以及启动命令
  * `jcmd -l`: 与`jcmd`相同

* 帮助信息
  * `jcmd -h/-help`:
    ``` shell
    Usage: jcmd <pid | main class> <command ...|PerfCounter.print|-f file>
    or: jcmd -l  
    or: jcmd -h  
    command must be a valid jcmd command for the selected jvm.  
    Use the command "help" to see which commands are available.  
    If the pid is 0, commands will be sent to all Java processes.  
    The main class argument will be used to match (either partially or fully) the class used to start Java.  
    If no options are given, lists Java processes (same as -p).  
    PerfCounter.print display the counters exposed by this process  
    -f  read and execute commands from the file  
    -l  list JVM processes on the local machine  
    -h  this help  
    ```

* Java进程信息
  * `jcmd <process id/main class> help`: 查看JVM进程可用的jcmd参数，例如:
    ``` shell
    JFR.stop    // Stops a JFR recording
    JFR.start   // Starts a new JFR recording
    JFR.dump    // Copies contents of a JFR recording to file. Either the name or the recording id must be specified.
    JFR.check   // Checks running JFR recording(s)
    VM.native_memory    // Print native memory usage
    VM.check_commercial_features    // Display status of commercial features
    VM.unlock_commercial_features   // Unlock commercial features
    ManagementAgent.stop    // Stop remote management agent.
    ManagementAgent.start_local // Start local management agent.
    ManagementAgent.start   // Start remote management agent.
    GC.rotate_log   // Force the GC log file to be rotated.
    Thread.print    // Print all threads with stack traces
    GC.class_stats  // Provide statistics about Java class meta data. Requires -XX:+UnlockDiagnosticVMOptions
    GC.class_histogram  // Create a class histogram
    GC.heap_dump    // Create a heap dump (hprof dump)
    GC.run_finalization // Call java.lang.System.runFinalization().
    GC.run  // Call java.lang.System.gc().
    VM.uptime   // Print the uptime in seconds
    VM.flags    // Print all the flags used for a VM
    VM.system_properties    // Print all the system properties set for a VM
    VM.command_line //Print the command line used to start this VM instance.
    VM.version  // Print full HotSpot and JDK version ID
    help
    ```
  * `jcmd <process id/main class> help <command>`: 查看command的详细信息
* 实例
  * `jcmd <process id/main class> GC.class_histogram filename=Myheaphistogram`: 获取java堆直方图，和`jmap -histo <pid>`作用相同，JDK1.7以后推荐使用jcmd
  * `jcmd GC.heap_dump filename=Myheapdump`: 获取java heap dump文件，与`jmap -dump:file=<file> <pid>`相同，JDK1.7以后推荐使用jcmd

## 参考

* <https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html>
* <https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jcmd.html>
