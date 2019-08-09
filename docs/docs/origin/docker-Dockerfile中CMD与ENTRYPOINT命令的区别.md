
CMD命令设置容器启动后默认执行的命令及其参数，但CMD设置的命令能够被docker run命令后面的命令行参数替换
ENTRYPOINT配置容器启动时的执行命令（不会被忽略，一定会被执行，即使运行 docker run时指定了其他命令）

## Shell格式和Exec格式运行命令
我们可用两种方式指定 RUN、CMD 和 ENTRYPOINT 要运行的命令：Shell 格式和 Exec 格式：

Shell格式：<instruction> <command>。例如：apt-get install python3
Exec格式：<instruction> ["executable", "param1", "param2", ...]。例如： ["apt-get", "install", "python3"]

## ENTRYPOINT命令
ENTRYPOINT 的 Exec 格式用于设置容器启动时要执行的命令及其参数，同时可通过CMD命令或者命令行参数提供额外的参数。ENTRYPOINT 中的参数始终会被使用，这是与CMD命令不同的一点。下面是一个例子：

```bash
ENTRYPOINT ["/bin/echo", "Hello"]  
```

当容器通过 docker run -it [image] 启动时，输出为：

```bash
Hello
```
而如果通过 docker run -it [image] CloudMan 启动，则输出为：

```bash
Hello CloudMan
```

将Dockerfile修改为：

```bash
ENTRYPOINT ["/bin/echo", "Hello"]  
CMD ["world"]
```

当容器通过 docker run -it [image] 启动时，输出为：

```bash
Hello world
```

而如果通过 docker run -it [image] CloudMan 启动，输出依旧为：

```bash
Hello CloudMan
```

ENTRYPOINT 中的参数始终会被使用，而 CMD 的额外参数可以在容器启动时动态替换掉。

# 参考链接
https://www.jianshu.com/p/f0a0f6a43907