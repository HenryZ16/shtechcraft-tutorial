# MCDR指南

MCDR，全称MCDReforged，是一个管理和控制MC服务器的Python工具。

> [!NOTE]
> MCDR并**不是**插件，也不是模组。它是独立于MC服务器之外的一个程序，起到控制MC服务器的作用。

MCDR有[官方中文文档](https://docs.mcdreforged.com/zh-cn/latest/index.html)，你可以通过它了解更多信息。

## 安装

目前最新的MCDR需要至少**Python 3.9版本**，使用pip安装`mcdreforged`包即可。使用时，特别是在Linux和macOS上使用时，请注意配置虚拟环境，避免将其安装到全局环境中。你可以使用`pipx`或`venv`等工具来配置虚拟环境。

此外，MCDR还提供了docker镜像，方便在容器里运行。推荐使用他们提供的OpenJDK变体镜像，里面自带运行MC服务器时所需要的Java环境。MCDR+Eclipse Termurin环境的docker镜像名为`mcdreforged/mcdreforged-temurin:latest`，其他变体和后缀请参阅[官方文档](https://docs.mcdreforged.com/zh-cn/latest/docker.html#openjdk-images)。

## 准备工作与启动服务器

在使用MCDR之前，需要先进入一个空文件夹，并用以下命令初始化运行环境：

```bash
mcdreforged init
```

MCDR 将生成一个如下所示的默认文件结构：

```plaintext
./
 ├─ config/ # 插件配置文件夹
 ├─ logs/ # 日志文件夹
 │   └─ MCDR.log
 ├─ plugins/ # MCDR插件文件夹
 ├─ server/ # MC服务器文件夹
 ├─ config.yml # MCDR主配置文件
 └─ permission.yml # MCDR权限文件
```

之后，你需要准备一个MC服务端，并把它们放入server文件夹里面。MCDR的启动命令默认会在server文件夹中执行。

然后，打开config.yml文件。你需要关注这五个字段：`language`，`start_command`，`handler`，`encoding`，`decoding`。请参照[官方文档](https://docs.mcdreforged.com/zh-cn/latest/configuration.html)配置以上各项。

以下是一个在Java 21启动Minecraft 1.21原版服务端的配置样例。**仅供参考，请不要照搬**，而是根据实际情况灵活调整配置内容。

```yaml
start_command: java -Dfile.encoding=UTF-8 -Dstdout.encoding=UTF-8 -Dstderr.encoding=UTF-8 -Xms1G -Xmx2G -jar minecraft_server.jar nogui

handler: vanilla_handler

encoding: utf8
decoding: utf8
```

一切准备就绪后，即可在此文件夹执行下方命令启动MCDR：

```bash
mcdreforged
```

## MCDR命令

MCDR命令是通过游戏内聊天文本或者控制台调用的，其统一前缀为`!!`。

MCDR内置两个命令：`!!MCDR`和`!!help`。你安装的插件也会提供其他命令。

关于内置命令的用法，请查阅[官方文档](https://docs.mcdreforged.com/zh-cn/latest/command/index.html)。

> [!IMPORTANT]
> 使用TrChat等修改玩家聊天格式的插件时，**玩家无法在游戏内调用MCDR命令**。这是因为MCDR捕获游戏内消息时只会遵循原版游戏的样式。查阅[代码](https://github.com/MCDReforged/MCDReforged/blob/d6a7516015cde1c1238901104129376ec99e3f64/mcdreforged/handler/impl/bukkit_handler.py#L25)发现，MCDR会使用正则表达式`r'<(?P<name>[^>]+)> (?P<message>.*)'`来匹配游戏后台输出中的玩家信息。这一表达式只会匹配带有“<*玩家名*>”前缀的消息。  
> 如有需要，建议添加一个频道，并把该频道内的聊天消息格式恢复为原版格式，这样该频道内发送的聊天消息便可被MCDR匹配。

## 插件简介

MCDR具有丰富的插件，能够帮助你实现各类功能，小至计算器、高亮玩家、B站弹幕姬，大至操控计分板、管理结构文件、自助备份回档等等。

MCDR现在有一个官方的[**插件仓库**](https://mcdreforged.com/zh-CN/plugins)，收录了各种常用的插件。

你可以通过`!!MCDR plg install xxx`命令来从插件仓库直接获取插件。

注意，有些插件可能需要依赖其他Python库、或其他软件才能运行，请仔细阅读插件的说明，并处理好各个依赖项。如果你使用docker运行MCDR，建议持久化保存`site-packages`相关文件，详见[官方文档](https://docs.mcdreforged.com/zh-cn/latest/docker.html#)。
