```
java -javaagent:myagent.jar=mode=test Test
```

-javaagent指定编写的agent的jar路径，以及要传给agent的参数

javaagent主要功能：

* 可以在加载class文件之前做拦截，对字节码做修改

* 可以在运行期对已加载类的字节码做变更，但是这种情况下会有很多的限制

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

-JVMTI\(JVM Tool Interface）

JVM暴露出来的一些供用户扩展的接口集合。

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

-JVMTIAgent

动态库，利用JVMTI暴露出来的一些接口来干一些我们想做、但是正常情况下又做不到的事情

* Agent\_OnLoad函数，如果agent是在启动时加载的，也就是在vm参数里通过-agentlib来指定的，那在启动过程中就会去执行这个agent里的Agent\_OnLoad函数。

* Agent\_OnAttach函数，如果agent不是在启动时加载的，而是我们先attach到目标进程上，然后给对应的目标进程发送load命令来加载，则在加载过程中会调用Agent\_OnAttach函数

* Agent\_OnUnload函数，在agent卸载时调用

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

-instrument agent

是一种JVMTIAgent，实现了Agent_OnLoad和Agent_\_OnAttach，启动时加载可通过javaagent：myagent.jar，运行时加载依赖JVM的attach机制，通过发送load命令来加载agent

-在启动时加载instrument agent

* 创建并初始化JPLISAgent
* 监听VMInit事件，在vm初始化完成之后做下面的事情：
  * 创建InstrumentationImpl对象
  * 监听ClassFileLoadHook事件
  * 调用InstrumentationImpl的\`loadClassAndCallPremain\`方法，在这个方法里会调用javaagent里MANIFEST.MF里指定的\`Premain-Class\`类的premain方法
* 解析javaagent里MANIFEST.MF里的参数，并根据这些参数来设置JPLISAgent里的一些内容

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

-在运行时加载instrument agent

```
VirtualMachine vm =VirtualMachine.attach(pid);
vm.loadAgent(agentPath,agentArgs);
```

上面会通过JVM的attach机制来请求目标JVM加载对应的agent，过程大致如下：

* 创建并初始化JPLISAgent
* 解析javaagent里MANIFEST.MF里的参数
* 创建InstrumentationImpl对象
* 监听ClassFileLoadHook事件
* 调用InstrumentationImpl的loadClassAndCallAgentmain方法，在这个方法里会调用javaagent里MANIFEST.MF里指定的AgentClass类的agentmain方法

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

agent类，有premain，将instrumentation注入进去，

