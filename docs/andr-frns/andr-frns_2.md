# 第三章

# Android 软件开发工具包和 Android 调试桥

### 本章信息

• Android 平台

• 软件开发工具包（SDK）

• Android 安全模型

• 取证与 SDK

## 引言

Android 软件开发工具包（SDK）不仅提供了在 Android 平台上运行的应用程序创建工具，还提供了文档和实用程序，这些在设备的法医或安全分析中可以提供很大帮助。尽管第二章中提到的 Android 硬件在设备能力中扮演了主要角色，但软件则利用这些功能最终创造了消费者寻求的体验和功能性。对 Android SDK 的彻底了解将为我们提供许多关于数据和设备以及我们将在调查中利用的重要实用程序的知识。

## Android 平台

Android 在 2007 年 11 月正式发布，但从 2005 年起就一直在进行重大开发。结合大量多样的硬件，利用 Android，创造了一个多元化的生态系统，为法医分析师或安全工程师增加了显著的复杂性。

Android 的一个有趣特点是 Android 平台本身的版本。平台是决定设备可以支持哪些功能的重要因素。官方的 Android 平台每个都分配有一个应用程序编程接口（API）级别，所有较新的版本都会有一个代号。截至 2011 年 1 月，当前的发布版本是代号为 Gingerbread 的 Android 2.3。下一个主要版本有一个代号为 Honeycomb，似乎针对的是预期增长的平板设备。表 3.1 列出了包括 API 级别、代号和发布日期在内的所有 Android 平台（Android 时间线，未标明日期）。

表 3.1 Android 平台

![图像](img/T100032tabT0010.jpg)

尽管存在许多 Android 版本，但每个版本在当前设备中的分布情况对法医分析师和安全工程师都有很大影响。图 3.1 展示了基于两周内访问 Android Market 的设备调查的 Google 关于 Android 版本分布的报告（平台版本，未标明日期）。

![图像](img/F100032f03-01-9781597496513.jpg)

图 3.1 2011 年 1 月 Android 设备平台分布情况。

为了让大家有一个直观的了解，表 3.2 展示了截至 2011 年 11 月，美国流通的各 Android 版本设备总数。这些数据基于大约 1599 万美国 Android 设备人口统计数据（comScore 报告，未标明日期）。

表 3.2 美国按平台划分的 Android 设备数量（近似值）

| Android 版本 | 总设备数 |
| --- | --- |
| Android 2.3 | 63,960 台 |
| Android 2.2 | 8,282,820 台 |
| Android 2.1 | 5,628,480 台 |
| Android 1.6 | 1,263,210 台 |
| Android 1.5 | 751,530 台 |

谷歌还发布了一张图表，显示了 2010 年 8 月至 2011 年 2 月 2 日七个月间 Android 版本的历史分布情况。数据同样基于访问 Android 市场的设备，并清晰地展示了 Android 更新随时间推移的进展，如图图 3.2 所示（Platform Versions, n.d.）。

![图像](img/F100032f03-02-9781597496513.jpg)

图 3.2 2001 年 8 月至 2011 年 2 月 2 日 Android 版本的历史分布情况。

尽管有些设备永远不会支持 Android 的最新版本，但许多设备最终会收到更新。未来的设备可能能够快速支持和升级到最新版本。然而，从法医和安全的角度来看，不能忽视这些较旧的异常值。

### 通过 2.3.3（姜饼）的 Android 平台亮点

Android 是一个复杂且高度发展的平台，任何尝试完全记录所有功能的努力都将占据本书的很大一部分。然而，简要概述每个主要版本可能对法医分析师有所帮助，使他们了解设备可能支持的功能。一般来说，功能是相互构建的，因此在 Android 1.5 中可用的功能在 Android 2.3.3 中可能得到改进并提供。

#### Android 1.5

Android 1.5 于 2009 年 4 月发布，其特点和更新内容在表 3.3 中列出（Android 1.5, n.d.）。

表 3.3 Android 1.5 功能和亮点

![图像](img/T100032tabT0020.jpg)

#### Android 1.6

Android 1.6 于 2009 年 9 月发布，其特点和更新内容在表 3.4 中列出（Android 1.6, n.d.）。

表 3.4 Android 1.6 功能和亮点

![图像](img/T100032tabT0025.jpg)

#### Androids 2.0 和 2.1

Android 2.0 和 2.1 分别于 2009 年 10 月和 2010 年 1 月发布，其特点和更新内容在表 3.5 中列出（Android 2.1, n.d.）。

表 3.5 Android 2.0/2.1 功能和亮点

![图像](img/T100032tabT0030.jpg)

#### Android 2.2

Android 2.2 于 2010 年 5 月发布，其特点和更新内容在表 3.6 中列出。

表 3.6 Android 2.2 功能和亮点

![图像](img/T100032tabT0035.jpg)

#### Android 2.3

Android 2.3 于 2010 年 12 月发布，其特点和更新内容在表 3.7 中列出。

表 3.7 Android 2.3 功能和亮点

![图像](img/T100032tabT0040.jpg)

#### Android 2.3.3

Android 2.3.3 于 2011 年 2 月发布，其特点和更新内容在表 3.8 中列出。

表 3.8 Android 2.3.3 功能和亮点

![图像](img/T100032tabT0045.jpg)

## 软件开发工具包（SDK）

Android 软件开发工具包（SDK）是开发 Android 应用程序所需的开发资源。它包括软件库和 API、参考资料、模拟器和其他工具。SDK 支持许多环境，包括 Linux、Windows 和 OS X，并且可以从[`developer.android.com`](http://developer.android.com)免费下载。

SDK 还是一个强大的取证工具，分析师在许多情况下使用它来帮助调查 Android 设备。

### SDK 发布历史

尽管 Android 平台标志着官方支持的 Android 发布版本，但 SDK 更新更为频繁。表 3.9 提供了完整的 SDK 发布历史，可以在这些情况下提供帮助（SDK Archives, n.d.）。

表 3.9 归档的 Android 平台发布

| 平台 | API 级别 | 发布日期 |
| --- | --- | --- |
| Android 1.6 r1 | 4 | 2009 年 9 月 |
| Android 1.5 r3 | 3 | 2009 年 7 月 |
| Android 1.1 r1 | 2 | 2009 年 2 月 |
| Android 1.0 r2 | 1 | 2008 年 11 月 |

### 安装 SDK

由于 SDK 在调查 Android 设备时至关重要，检查员应该有一个正常工作的安装环境。以下部分提供了在支持平台上安装 SDK 的分步指南。

#### Linux SDK 安装

这些步骤基于在第一章中用于下载和编译 Android 开源项目 (AOSP) 的 Ubuntu VM，该 VM 已经包括大多数先决条件，包括 Java 开发工具包。从终端窗口安装所需的 32 位库：

注意

### 32 位库

由于在第一章中构建的 Ubuntu VM 使用了 Ubuntu 的 64 位版本，因此我们必须安装 32 位库来安装 SDK。但是，如果你使用的是 32 位 Linux 工作站，则无需完成此步骤。虽然 32 位工作站可以运行 SDK，但它无法构建版本 2.2 之后的 AOSP。

![image](img/F100032u03-01-9781597496513.jpg)

然后，启动 Firefox 并导航至[`developer.android.com/sdk`](http://developer.android.com/sdk)，下载 Linux i386 平台版本（截至 2011 年 1 月为 android-sdk_r08-linux_86.tgz）。默认操作将在归档管理器中打开归档，如图 3.3 所示。

![image](img/F100032f03-03-9781597496513.jpg)

图 3.3 下载 Linux 版本的 Android SDK。

然后右键点击并将归档提取到你的主目录中，如图 3.4 所示。

![image](img/F100032f03-04-9781597496513.jpg)

图 3.4 为 Linux 提取 Android SDK。

接下来，在终端窗口中：

![image](img/F100032u03-02-9781597496513.jpg)

这将运行 Android SDK 和 Android 虚拟设备 (AVD) 管理器，允许你下载和管理如图 3.5 所示的额外必要组件。

![image](img/F100032f03-05-9781597496513.jpg)

图 3.5 Linux 中的 Android SDK 和 AVD 管理器。

为了充分利用 Android SDK，需要安装额外的组件。至少，我们希望安装特定平台的 SDK 工具和至少一个 SDK 平台（在本例中为 Android 2.3），这样我们就可以运行模拟器。要完成安装，请从左侧导航窗格中选择可用包，然后选择如图 3.6 所示的两个附加包。

![image](img/F100032f03-06-9781597496513.jpg)

图 3.6 选择附加的 Android SDK 包。

然后选择安装选定内容。你将被提示批准所有软件包的许可，如图 3.7 所示。

![image](img/F100032f03-07-9781597496513.jpg)

图 3.7 接受并安装 Android SDK 软件包。

选择接受所有（如果你同意）然后安装。Android SDK 和 AVD 管理器将下载并安装组件。

可选地，你可能想要将二进制目录添加到你的操作系统（OS）执行路径中，这样每次就无需指定程序的完整路径。在 Linux 中，执行以下操作：

![image](img/F100032u03-03-9781597496513.jpg)

保存，退出，然后重新打开（Ctrl-O）一个新的 shell。

在 Ubuntu 中，你必须采取的最后一步是创建系统配置中每个 Android 设备制造商的 USB 配置文件，特别是 udev 规则。以 root 用户从终端会话中编辑/创建 udev 规则：

![image](img/F100032u03-04-9781597496513.jpg)

复制以下内容（供应商 ID 可以在[`developer.android.com/guide/developing/device.html#VendorIds`](http://developer.android.com/guide/developing/device.html%23VendorIds)找到）：

![image](img/F100032u03-05-9781597496513.jpg)

然后保存文件。最后，使文件对所有用户可读：

![image](img/F100032u03-06-9781597496513.jpg)

你可以选择重启 udev 守护进程，或者直接重启。

#### Windows SDK 安装

Windows 的最新版 Android SDK，如图 3.8 所示，现在被打包成一个可执行的安装程序，该程序将检测你是否已经正确安装了必要的 Java 依赖项，如果没有，它将为你下载并安装它们。然而，安装程序只检测 32 位 JDK 的安装，并不会在 Windows 7 64 位安装上自动安装 JDK。如果你运行的是 32 位版本的 Windows（例如 Windows XP），那么安装程序可能是一个好的选择，你可以直接从[`developer.android.com/sdk/index.html`](http://developer.android.com/sdk/index.html)下载安装包并运行安装程序。

![image](img/F100032f03-08-9781597496513.jpg)

图 3.8 Windows 的 Android SDK 安装程序。

然而，许多分析师和工程师已经转向 64 位操作系统。要在 Windows 上安装 Android SDK，首先通过下载[`java.sun.com/javase/downloads/`](http://java.sun.com/javase/downloads/)来安装 Java SE SDK。确保你安装了完整的 SDK。

安装完 SDK 后，在[`developer.android.com/sdk/index.html`](http://developer.android.com/sdk/index.html)下载 Windows 版 Android SDK 的压缩版本，并将其解压到你的硬盘上。在我们的示例中，我们将直接解压到 C:\，这将创建文件夹 C:\android-sdk-windows。

打开该目录，双击 SDK Manager.exe 开始更新过程。确保你至少选择 Android SDK Platform-tools，如图 3.9 所示，以及一个发布平台（在此示例中为 2.3）。

![image](img/F100032f03-09-9781597496513.jpg)

图 3.9 Windows 系统的 Android SDK 管理器。

在 Windows 中使用安卓设备时，你需要指定 USB 驱动程序。Android SDK 最近更新了 USB 驱动程序的安装方式。首先，确保你正在运行 SDK 管理器并选择可用包。展开第三方插件 → Google Inc.插件，最后选择如图 3.10 所示的 Google USB 驱动程序包。

![image](img/F100032f03-10-9781597496513.jpg)

图 3.10 Windows 系统的 Google USB 驱动程序包。

然后，接受许可协议并按照图 3.11 所示进行安装。

![image](img/F100032f03-11-9781597496513.jpg)

图 3.11 接受并安装许可协议。

安装完 USB 驱动后，你应该拥有所有必要的组件。然而，为了简化从 Android SDK 运行工具，你应该更新你的工作站的环境变量，特别是可执行文件的 PATH。为此，请进入你的控制面板并打开系统应用程序。然后选择可以更新环境变量的标签，其位置会根据你的 Windows 版本有所不同，如图 3.12 所示。最后，找到 Path 系统变量，选择编辑，并添加到你的 Android SDK platform-tools 目录的完整路径，在我们的示例中是`;C:\android-sdk-windows\platform-tools`。

![image](img/F100032f03-12-9781597496513.jpg)

图 3.12 更新 PATH 环境变量（Windows 7 64 位）。

“;”很重要，因为它是路径位置的分隔符。完成此更新后，确保退出并等待命令提示符指示新设置已生效。

#### OS X SDK

在 OS X 上安装 Android SDK，首先从[`developer.android.com/sdk/index.html`](http://developer.android.com/sdk/index.html)下载存档文件，OS X 会自动提取。

导航至图 3.13 所示的工具子目录，然后双击 Android 运行 Android SDK 和 AVD 管理器，如图 3.14 所示。

![image](img/F100032f03-13-9781597496513.jpg)

图 3.13 OS X 的提取 Android SDK。

![image](img/F100032f03-14-9781597496513.jpg)

图 3.14 在 OS X 上打开 Android。

当管理器运行时，选择可用包，展开安卓仓库，然后选择 Android SDK platform-tools 以及至少一个安卓平台，如图 3.15 所示。

![image](img/F100032f03-15-9781597496513.jpg)

图 3.15 在 OS X 上安装 Android SDK 组件。

接受许可协议并完成安装。最后，为了简化从 Android SDK 运行工具，你应该更新你的可执行文件 PATH。在 OS X 10.6 上，运行终端（应用程序 → 实用工具）并执行以下操作：

![image](img/F100032u03-07-9781597496513.jpg)

确保完全退出终端应用程序然后重新启动。在终端中，输入：

![image](img/F100032u03-08-9781597496513.jpg)

这应该会返回你的可执行路径，并附加了 platform-tools。

### 安卓虚拟设备（模拟器）

一旦你的工作站上安装了 Android SDK 并且至少下载了一个发布平台，你就可以创建一个 AVD，即虚拟移动设备或模拟器，它在你的电脑上运行。模拟器特别有助于开发者为创建定制应用程序。然而，对于取证分析师和安全工程师来说也具有很高的价值，因为你可以在设备上分析应用程序的执行情况。这在对调查结果进行验证或测试取证工具对 Android 设备的影响时可能很重要。

模拟器需要相当多的资源，因此理想的工作站应该配备较新的 CPU 和足够的 RAM。检查员可能还需要一些耐心。要创建 AVD，首先运行 Android SDK 和 AVD 管理应用程序，如图图 3.16 所示。如果你更新了操作系统的路径，使其包含 SDK 中的工具目录，你应该可以从 shell、终端或命令提示符运行 Android。

![image](img/F100032f03-16-9781597496513.jpg)

图 3.16 启动 Android SDK 和 AVD 管理器。

在左侧窗格中，选择虚拟设备，然后选择新建，如图图 3.17 所示。

### 确保填写以下字段

• 名称：为虚拟设备提供一个名称，例如，af23（Android Forensics 2.3）。

• 目标：选择目标平台，在本例中为 Android 2.3—API 级别 9。

• [可选] SD 卡：可选地为虚拟设备创建一个 SD 卡。

![image](img/F100032f03-17-9781597496513.jpg)

图 3.17 创建新的 AVD。

你可以设置其他属性。然而，现在我们将创建最基本的 AVD。另外，如果你遇到运行在较旧平台上的 Android 设备，你可以通过简单地使用 Android SDK 和 AVD 管理器下载 Android 平台来创建运行较旧版本的虚拟设备。当你点击创建 AVD 时，设备将被创建，你会看到一个类似于图 3.18 所示的确认屏幕。

![image](img/F100032f03-18-9781597496513.jpg)

图 3.18 AVD 创建确认。

确保新的 AVD 被选中，然后点击开始，此时你会看到如图图 3.19 所示的启动选项提示。

![image](img/F100032f03-19-9781597496513.jpg)

图 3.19 AVD 启动选项。

选择你希望启动的任何选项并点击启动。此时，AVD 将开始启动过程，可能需要几分钟或更长时间。在这段时间里，你会看到 Android 正在启动。这如图图 3.20 所示。

![image](img/F100032f03-20-9781597496513.jpg)

图 3.20 AVD 启动中。

最后，你会看到一个完全功能的 AVD，如图图 3.21 所示。

![image](img/F100032f03-21-9781597496513.jpg)

图 3.21 运行的 AVD。

AVD 非常强大且功能齐全。例如，你可以轻松地上网，如图 3.22 所示，浏览网站。你可以配置电子邮件账户，向其他 AVD 发送测试短信，如果你是开发者，当然还可以部署和测试你的应用程序。

![image](img/F100032f03-22-9781597496513.jpg)

图 3.22 运行浏览器的 AVD。

创建并启动 AVD 后，生成数据对于取证和安全研究非常有价值。这些文件在你的主目录中创建，根据平台不同而有所变化，在一个名为.android（注意文件名前的点前缀）的文件夹中。表 3.10 提供了特定的操作系统路径。

表 3.10 AVD 存储目录

| 工作站操作系统 | AVD 存储目录 | 示例 |
| --- | --- | --- |
| Ubuntu Linux | /home/<username>/.android | /home/ahoog/.android |
| Mac OS X | /Users/<username>/.android | /Users/ahoog/.android |
| Windows 7 | C:\Users\<username>\.android | C:\Users\ahoog\.android |

在 AVD 的.android 目录中，你可以找到运行 AVD 所需的配置和数据文件。

![image](img/F100032u03-09-9781597496513.jpg)

### 特别值得注意的是以下具有取证和安全意义的文件

• cache.img：/cache 分区的磁盘映像

• sdcard.img：SD 卡的磁盘映像（如果在 AVD 设置过程中创建）

• userdata-qemu.img：/data 分区的磁盘映像

cache.img 和 userdata-qemu.img 是 YAFFS2 文件系统，目前取证软件不支持，我们将在第四章中介绍。但是，标准的取证工具在 sdcard.img 上工作得很好，因为它是 FAT32 文件系统。

![image](img/F100032u03-10-9781597496513.jpg)

取证分析师和安全工程师可以通过利用模拟器并检查网络、文件系统和数据痕迹来深入了解 Android 及其运行方式。

### 安卓操作系统架构

了解 Android 的高级架构非常重要，特别是对于安全程序和超越逻辑取证分析。

Android 基于 Linux 2.6 内核，提供了启动和管理硬件及 Android 应用程序所需的基本软件。虽然内核提供的功能非常广泛，但我们将关注图 3.23 中强调的核心区域。

![image](img/F100032f03-23-9781597496513.jpg)

图 3.23 安卓架构。

如图 3.23 所示，低级功能包括电源管理、[Wi-Fi.com](http://Wi-Fi.com)、显示、音频驱动等。从取证的角度来看，最重要的可能是闪存驱动，我们将在第四章中详细探讨。

在内核之后，一套库可供使用，这些库为开发者和设备所有者提供了所需的核心功能。这些包括用于在捆绑的浏览器和第三方应用中渲染 HTML 的 WebKit 库。其他库处理字体、显示、各种媒体，以及使用安全套接字层（SSLs）的安全通信。最后，SQLite 库为 Android 上的结构化数据存储提供了一种方法，这也是法医分析师和安全工程师关注的重点。

核心库随后与自定义的 Java 虚拟机（VM）捆绑在一起，以提供 Android 运行时环境，应用程序就在这里运行。

最后，SDK 通过 API 和一个应用程序框架提供对这些资源的访问。该框架是第三方开发者交互的主要层，它为他们提供了对应用程序所需关键资源的抽象访问。在我们探索逻辑取证技术时，应用程序框架的一个重要方面——内容提供者——将详细解释，因为它们是我们从 Android 设备提取数据的主要机制。

### Dalvik VM

Dalvik 虚拟机（Dalvik VM）是由谷歌开发的，旨在创建一个高效且安全的移动应用环境。

为了实现所需的安全性，每个应用程序都在其自己的 Dalvik VM 上运行。因此，Dalvik VM 被编写成可以在 Android 设备上同时运行多个 VM。Dalvik VM 在很大程度上依赖于 Linux 操作系统，提供诸如访问核心库和硬件、威胁和安全管理、内存管理等低级功能。

为了提高效率，在 Dalvik VM 中运行的应用程序有一种特殊的格式，称为 Dalvik 可执行文件（.dex）。开发者使用 Sun 的 Java 开发工具包编写和编译他们的程序，然后生成的字节码被转换成.dex 文件，该文件提供了高效的存储，并针对在 Dalvik VM 中的执行进行了优化。由知名 Android 黑客 JesusFreke 开发的一个有趣的项目叫做 smali/baksmali。这个项目允许用户反编译.dex 文件，以确定应用程序的功能（smali, n.d.）。

Dalvik 是 Android 的一个独特方面，也是设备法医和安全分析的一个关键组件。

### 本地代码开发

尽管大多数 Android 应用程序是使用 SDK 用 Java 编写的，但谷歌提供了一个更底层的开发平台，即他们的本地开发工具包（NDK）。NDK 首次发布于 2009 年 6 月，并经历了五次修订，最新版本在 2010 年 11 月发布。

NDK 允许开发者在 C/C++中编写代码，并直接为 CPU 编译。虽然这增加了开发过程的复杂性，但一些开发者可以通过重用现有的 C/C++代码库或实现可以在 Dalvik VM 之外优化的某些功能来从中受益。NDK 不允许开发者创建完全在 Dalvik VM 之外运行的应用程序；相反，C/C++组件被打包在应用程序的.apk 文件中，并在 VM 内由应用程序调用。

目前，NDK 支持 ARMv5TE 和 ARMv7-A CPU，将来还会支持英特尔的 x86 CPU 架构。当开发者在一种平台（例如，Mac OS X）上编写代码，但为另一种 CPU 编译时，这种技术被称为交叉编译应用程序。NDK 极大地简化了这一过程，并提供了一套开发者可以使用的库。

从取证和安全的角度来看，交叉编译是研究和开发新技术和漏洞利用的重要部分。尽管大多数取证分析师和安全工程师不需要编译代码，但了解这个过程如何工作以及它在其中扮演的角色是很重要的。例如，最初的 Android 1.5 获取根权限的漏洞利用了 Linux 内核的一个错误（CVE-2009-2692）来获取权限。最初分发的代码是源代码，需要交叉编译。这种方法的一个显著优势是，检查员可以详细描述设备是如何被利用的，并在必要时提供源代码。

随着 Android 的成熟，预计会在 NDK 和本地编译代码方面看到更多的发展。

## Android 安全模型

Android 平台通过一系列旨在保护用户的控制措施来实现安全性。

当一个应用程序首次安装时，Android 会检查.apk 文件，以确保它有一个有效的数字签名来识别开发者。与 SSL 不同，数字证书不需要由证书授权中心签署。然而，开发者必须妥善保管密钥；否则，有人可能会签署一个恶意应用程序并以该开发者的身份分发。例如，如果一个金融机构的数字签名被泄露，一个恶意的开发者可以发布一个银行应用程序的更新，窃取关键数据。

在验证了.apk 文件之后，Android 会检查开发者创建的特殊文件，该文件指定了应用程序需要系统权限的各个方面，例如，应用程序可能请求访问用户的联系人、短信和网络/互联网。如果这个应用程序为短信系统增加了功能，这些权限似乎是合理的。然而，如果应用程序只是更改你的背景图片，那么用户应该质疑这个权限，并可以选择不安装该应用程序。实际上，用户通常会快速同意所有权限和应用程序的请求，从而可能允许恶意应用程序安装。

在应用程序经过验证并且用户授予了请求的权限之后，应用程序现在可以在系统上安装。Android 安全模型的一个关键部分是，每个应用程序都会分配一个唯一的 Linux 用户和组 ID，并在自己的进程和 Dalvik VM 中运行。在安装过程中，系统会在设备上创建一个特定的目录来存储应用程序数据，并且只允许该应用程序通过 Linux 用户 ID 和组 ID 权限访问这些数据。此外，应用程序的 Dalvik VM 也会以特定的用户 ID 在其自己的进程中运行。这些关键机制在操作系统级别强制执行数据安全，因为应用程序不共享内存、权限或磁盘存储。应用程序只能访问其 Dalvik VM 内的内存和数据。

当然，这个过程有几个例外。首先，开发者可以使用相同的数字证书为多个应用程序签名，并指定它可以与他们的其他应用程序共享相同的用户 ID、进程、内存和数据存储。这种情况是例外的，通常用于开发者同时拥有免费和付费版本的情况。如果用户升级到付费版本，他们可以利用使用免费版本时积累的数据，因此不会丢失任何数据。

同时，大多数 Android 用户可以选择允许应用程序从非市场位置安装，并跳过数字签名检查。这个选项可以从设备设置中的应用程序菜单中访问，当选择时，会向用户显示一个警告，如图图 3.24 所示。

![image](img/F100032f03-24-9781597496513.jpg)（图片无需翻译，保留英文描述即可）

图 3.24 Android 设置允许从未知来源安装应用程序。

最常见的情况是用户现在可以直接从网站下载.apk 文件来安装应用程序，安装过程也会跳过数字签名检查。近期一款 AT&T 手机（摩托罗拉 Backflip）从 Android 中移除了这一选项，令许多用户感到不满（Android On Lockdown, n.d.）。然而，使用 Android SDK 的一个变通方法确实存在，我们将在第六章中进行讨论。

由于 Android 内置的安全架构，取证检查员无法简单从设备中提取核心用户数据。除了漏洞利用之外，安全架构在隔离和保护应用程序之间的数据方面是有效的。

## 取证与软件开发工具包（SDK）

那么 SDK 在取证中有多重要呢？SDK 不仅提供了一套工具和驱动程序，以便分析 Android 设备，而且对于应用程序剖析和其他取证研究也很有用。

### 将 Android 设备连接到工作站

重要的是要注意 Android 设备实际是如何连接到虚拟机的。截至目前，Android 设备有一个物理 USB 接口，允许它们连接、共享数据和资源，并且通常可以从计算机或工作站充电。如果你只运行一个操作系统，USB 设备应该能被检测到并可以访问。然而，如果运行虚拟机，你可能需要额外的配置或驱动程序。例如，如果你的宿主操作系统是 OS X 并且你正在运行 VMWare fusion，你可以选择菜单“虚拟机 → USB”，然后连接设备（在本例中是高 Android 手机），如图 3.25 所示。

![image](img/F100032f03-25-9781597496513.jpg)

图 3.25 在 VMWare Fusion 中连接 USB 设备到 Ubuntu VM。

同样，如果你的宿主操作系统是 Linux，并且你正在使用 Oracle 的 VirtualBox 运行虚拟机，首先需要确保你是 usbusers 组的成员。因此，从终端会话中执行以下命令：

![image](img/F100032u03-11-9781597496513.jpg)

接下来，进入虚拟机的设置，为设备添加 USB 过滤器，如图 3.26 所示。

![image](img/F100032f03-26-9781597496513.jpg)

图 3.26 在运行 Oracle VirtualBox 的 Linux 主机上添加 USB 过滤器。

最后，你可以像图 3.27 所示那样连接 USB 设备。

![image](img/F100032f03-27-9781597496513.jpg)

图 3.27 在运行 Oracle VirtualBox 的 Linux 主机上连接 USB 设备。

最后，如果你以无头模式运行虚拟机（如第一章所述的 VirtualBox 3.2.10），以下是你要执行的步骤。首先，需要安装 VBox 附加组件，这将启用共享文件夹、更好的视频、USB 支持（如果你下载/购买了 PUEL 版本）和其他功能。从宿主工作站开始：

![image](img/F100032u03-12-9781597496513.jpg)

现在 Ubuntu 虚拟机应该可以访问 DVD 了。再次远程桌面进入虚拟机（有关必要步骤请参阅第一章），然后双击桌面上的 VBOXADDITIONS_3.2.0_61806 DVD 以打开 DVD。接着双击 autorun.sh 并选择运行选项。输入密码后，安装将继续进行。图 3.28 展示了这一步骤。

![image](img/F100032f03-28-9781597496513.jpg)

图 3.28 通过 Ubuntu VM 远程桌面协议安装 VBox 附加组件。

既然你已经安装了 VBox Additions，那么你可以将 USB 设备连接到你的客户操作系统。但首先，你必须关闭虚拟机。然后，按照以下步骤操作：

![image](img/F100032u03-13-9781597496513.jpg)

使用此示例，USB 设备现在应该已经传递到虚拟机。

### USB 接口

当你通过单个 USB 端口将 Android 设备连接到工作站或虚拟机时，硬件和 Android 本身通常会公开多个虚拟 USB 接口。例如，当你通过 USB 连接 HTC Incredible 时，你会看到一个包含四个选项的菜单：

1. 仅充电—通过 USB 为手机充电

2. HTC 同步—同步联系人和日历

3. 磁盘驱动器—作为磁盘驱动器挂载

4. 移动宽带连接—将智能手机的移动网络与 PC 连接

默认选择，如图 3.29 所示，是仅充电选项。HTC 同步和移动宽带连接选项是 HTC 以及有时无线运营商为设备提供的自定义选项和程序。

![image](img/F100032f03-29-9781597496513.jpg)

图 3.29 HTC Incredible 连接到 PC 的选项。

#### CD-ROM 接口

磁盘驱动器选项更通用。此选项将 Android 设备作为磁盘驱动器连接到工作站。这是设备向工作站公开多个 USB 设备的关键区域。当你首次将 HTC Incredible 插入计算机时，实际上注册了三种不同类型的驱动器：一个 CD-ROM 和两个 USB 大容量存储设备。以下列表是从 Linux 工作站的内核消息中获取的，使用了 dmesg 命令：

![image](img/F100032u03-14-9781597496513.jpg)

如你所见，在 4:0:0:0 和 4:0:0:1 处找到了两个直接访问驱动器，以及在 4:0:0:2 处找到了一个 CD-ROM。CD-ROM 包含 HTC 与设备捆绑在一起的自定义程序和驱动，以实现同步和宽带连接功能。显然，没有物理 CD-ROM。然而，设备的存储空间有一部分专用于 CD-ROM，并格式化为 ISO9660。宿主操作系统可以将其作为 CD-ROM 挂载，并且在 Windows 中，甚至可能支持自动运行功能。利用 TSK 的 fsstat 程序，我们可以看到有关分区的更多详细信息：

![image](img/F100032u03-15a-9781597496513.jpg)

![image](img/F100032u03-15b-9781597496513.jpg)

从卷名称可以看出，CD-ROM 包含由 Verizon 提供的软件，用于使用设备的附加功能。

#### SD 卡（可移动和虚拟）

从法医的角度来看，更重要的是通过设备可访问的 SD 卡。在 Android 中，将用户文件，尤其是如多媒体这样的大文件放置在 SD 卡上是关键策略。大多数 Android 设备有一个可拆卸的媒体插槽，可接受 micro-SD 卡。核心应用程序数据保留在设备上（位于 /data/data 下），但在调查中可能重要的文件也可能存在于 SD 卡上。

在上一节中，当通过 USB 连接 Android 设备时，Linux 工作站的内核信息显示了可用的各种 USB 设备。列出的两个 SCSI 可移动磁盘 sdb 和 sdc，代表 HTC Incredible 上的 SD 卡。如果您在“连接到电脑”下选择“作为磁盘驱动器挂载”选项，内核信息将显示以下附加消息：

![image](img/F100032u03-16-9781597496513.jpg)

现在您将看到有关 SD 卡的更多信息。驱动器 sdc 有一个分区 sdc1，其大小为 2 GB。我们可以通过运行 TSK 的 mmls 来查看附加的分区信息：

![image](img/F100032u03-17-9781597496513.jpg)

您会看到，SD 卡被格式化为 FAT16 文件系统，但通常您会遇到 FAT32，或者可能会遇到像 FAT32 和本地 Linux 文件系统 ext3 和 ext4 这样的多种文件系统。

近年来，设备还拥有一个模拟或虚拟的 SD 卡功能，该功能使用设备的 NAND 闪存创建一个不可拆卸的 SD 卡。这更接近于 iPhone 的模型，用户数据分区直接位于 NAND 闪存上，且无法移除。在上述示例中，sdb 设备提供了对模拟 SD 卡的访问。与物理 SD 卡不同，sdc 没有分区表，文件系统直接从开头开始。要查看重要信息，请运行 TSK 的 fsstat：

![image](img/F100032u03-18a-9781597496513.jpg)

![image](img/F100032u03-18b-9781597496513.jpg)

在这个特定情况下，文件系统实际上是 FAT32，您会注意到尽管卷没有标签，但 OEM 名称设置为 BSD 4.4。

警告

### 自动挂载 USB 设备

在第一章的 Ubuntu 虚拟机配置部分，禁用了自动挂载功能，以防止操作系统自动检测并挂载 USB 存储设备。取证分析师应采取极端谨慎的措施，防止在调查中的设备发生这种情况。除了禁用自动挂载外，通常应通过 USB 写保护器连接设备。

在 Ubuntu 中，如果您没有禁用 USB 设备的自动挂载（在几乎所有情况下都应该禁用），则 SD 卡会自动为您挂载。如果设备连接到硬件写保护器，以只读方式挂载，或者在不需要写保护的情况下（例如，研发情况），您可以在 Linux 中运行 df 命令来查看它们被挂载的位置：

![image](img/F100032u03-19-9781597496513.jpg)

物理 SD 卡被挂载在/media/E0FD-1813 上，而模拟 SD 卡被挂载在/media/C7F8-0810 上。

在 Android 设备本身上，两个 SD 卡以下列方式挂载：

![image](img/F100032u03-20-9781597496513.jpg)

#### USB 调试

最后一个重要的 USB 接口是 Android 调试桥（ADB），它允许开发人员、取证分析师或安全工程师通过 USB 与 Android 设备进行通信和控制。默认情况下，AVD（在模拟器中运行）将启用 USB 调试。然而，非模拟器设备必须明确启用 USB 调试。要启用，请从设备的设置中选择应用程序→开发，如图 3.30 所示。最后，勾选 USB 调试。

![image](img/F100032f03-30-9781597496513.jpg)

图 3.30 启用 USB 调试。

设置后，设备将在后台运行 adb 守护进程（adbd）并等待 USB 连接。守护进程将在非特权 shell 用户账户下运行，以限制其对数据的访问。启用了 root 访问权限的 AVD 和物理设备将作为 root 运行 adbd，从而提供对系统的完全访问。关于此主题的更多详细信息将在第六章中介绍。

在 Android 的新版本中，任何时候启用 USB 调试的设备通过 USB 连接，它都会显示一个安全警告，如图 3.31 所示。

![image](img/F100032f03-31-9781597496513.jpg)

图 3.31 USB 调试警告。

对于当前所有的逻辑 Android 取证工具，必须启用 USB 调试。如果设备未锁定，实现这一点是微不足道的，但如果设备有密码，则要困难得多。有一些技术可以绕过密码，这在第六章中有所讨论。然而，它们并不在所有平台上都有效。

### Android 调试桥简介

在本书的其余部分，我们将广泛使用 adb，因此现在掌握基础知识很重要。在使用 adb 时，涉及到三个主要组件：

1. 运行在 Android 设备上的 adbd

2. 运行在工作站上的 adbd

3. 运行在工作站上的 adb 客户端程序

如前所述，当你在 Android 设备上启用 USB 调试时，守护进程将会运行并监听连接。设备上的 adbd 与工作站上的 adbd 之间的通信是通过 USB 连接之上的虚拟网络进行的。守护进程通过本地主机的端口 5555 至 5585 进行通信。当工作站的 adbd 检测到新的模拟器或设备时，它会创建两个连续的端口连接。偶数端口与设备的控制台通信，而奇数端口用于 adb 连接。本地 adb 客户端程序使用端口 5037 与本地 adbd 通信。

你可以发出的最基本的 adb 命令是 adb devices 命令，它提供了已连接设备的列表。

![image](img/F100032u03-21-9781597496513.jpg)

另一个重要的命令提供了杀死本地 adb 服务的能力。为此，请输入以下内容：

![image](img/F100032u03-22-9781597496513.jpg)

如你所见，如果工作站上的 adbd 没有运行，它将被自动启动。在 Ubuntu 上，如果你收到以下响应：

![image](img/F100032u03-23-9781597496513.jpg)

连接的 Android 设备很可能有一个新的供应商 ID，必须通过（sudo lsusb -v）识别并将其添加到“SDK 安装”部分讨论的 udev 规则中。在 Microsoft Windows 中，如果 Android 设备未被识别，你会收到警告，并且必须从 Google 或制造商处安装适当的 USB 驱动程序。

所有分析师和工程师都应该知道的一个强大的 adb 命令是“adb shell”，它允许你打开 Android 设备上的 shell 并与系统交互。这对于任何探索 Android 的人来说都是一个重要的功能。例如，启动一个 AVD 并按照以下步骤查看设备上的应用程序数据目录：

![image](img/F100032u03-24a-9781597496513.jpg)

![image](img/F100032u03-24b-9781597496513.jpg)

随着每个新 SDK 的发布，adb 的功能不断增强，是一个非常强大的工具。在第六章中，将详细探讨一些功能，包括：

1. 在设备上运行 shell 命令

2. 使用命令行安装应用程序

3. 在你的工作站和设备之间转发端口

4. 递归地将文件和文件夹复制到设备或从设备中复制出来

5. 查看设备日志文件

关于 adb 命令的完整文档可以在 Android 开发者网站上找到：[`developer.android.com/guide/developing/tools/adb.html#commandsummary`](http://developer.android.com/guide/developing/tools/adb.html%23commandsummary)。

在 Android 模拟器上测试各种命令是理解这个工具并在调查中利用它的一个很好的方法。

## 概述

Android SDK 不仅提供了对 Android 平台的深入洞察，而且还提供了强大的工具来从取证和安全的角度调查设备。一旦 SDK 安装在取证工作站上，检查员就可以通过 USB 连接与 Android 设备交互，前提是启用了 USB 调试功能。不仅可以从设备查询信息，还可以安装、运行应用程序，并最终从设备提取数据。Android SDK 是用于取证和安全分析的重要工具。

## 参考文献

1. *Android 时间线*（n.d.）。Android 教程、新闻、观点和论坛，Android 学院。2011 年 3 月 12 日检索自：[`www.androidacademy.com/1-android-timeline`](http://www.androidacademy.com/1-android-timeline)。

2. *平台版本*（n.d.）。Android 开发者。2011 年 3 月 12 日检索自：[`developer.android.com/resources/dashboard/platform-versions.html`](http://developer.android.com/resources/dashboard/platform-versions.html)。

3\. *comScore 报告 2010 年 11 月美国移动用户市场份额——comScore, Inc*（未注明日期）。comScore, Inc.——衡量数字世界。2011 年 3 月 12 日检索自 [`www.comscore.com/Press_Events/Press_Releases/2011/1/comScore_Reports_November_2010_`](http://www.comscore.com/Press_Events/Press_Releases/2011/1/comScore_Reports_November_2010_)。

4\. *安卓 1.5 平台*（未注明日期）。安卓开发者。2011 年 3 月 12 日检索自 [`developer.android.com/sdk/android-1.5.html`](http://developer.android.com/sdk/android-1.5.html)。

5\. *安卓 1.6 平台*（未注明日期）。安卓开发者。2011 年 3 月 12 日检索自 [`developer.android.com/sdk/android-1.6.html`](http://developer.android.com/sdk/android-1.6.html)。

6\. *安卓 2.1 平台*（未注明日期）。安卓开发者。2011 年 3 月 12 日检索自 [`developer.android.com/sdk/android-2.1.html`](http://developer.android.com/sdk/android-2.1.html)。

7\. *SDK 存档*（未注明日期）。安卓开发者。2011 年 3 月 13 日检索自 [`developer.android.com/sdk/older_releases.html`](http://developer.android.com/sdk/older_releases.html)。

8\. *smali-在 Google Code 上的项目托管*（未注明日期）。Google code。2011 年 3 月 13 日检索自 [`code.google.com/p/smali/`](http://code.google.com/p/smali/)。

9\. *安卓系统受限：AT&T 从 Backflip 中移除了安卓的最佳功能*（未注明日期）。AndroidGuys。一个值得信赖的安卓新闻和观点来源，成立于 2007 年。2011 年 3 月 13 日检索自 [`www.androidguys.com/2010/03/08/android-lockdown-att-removes-parts-android-backflip/`](http://www.androidguys.com/2010/03/08/android-lockdown-att-removes-parts-android-backflip/)。