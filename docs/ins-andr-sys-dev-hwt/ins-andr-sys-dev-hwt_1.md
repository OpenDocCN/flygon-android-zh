# 第一章：即时 Android 系统开发指南

欢迎使用*Instant Android Systems Development How-to*。本书将为您提供成为成功的 Android 系统程序员所需的所有必要技能。我们将涵盖一系列主题，从构建源代码到刷写实际的 Android 手机。本书假定读者熟悉并了解 Android 软件开发工具包。要更好地了解如何为 Android 操作系统开发，请读者务必实际执行每个配方中的所有步骤。

# 构建 Android（必须知道）

此配方设置了您的构建计算机，并指导您如何从头开始下载和构建 Android 操作系统。

## 准备工作

您需要 Ubuntu 10.04 LTS 或更高版本（Mac OS X 也受到构建系统的支持，但我们将在本书中使用 Ubuntu）。这是受支持的构建操作系统，也是您将从在线社区获得最多帮助的操作系统。在我的示例中，我使用的是 Ubuntu 11.04，这个版本也得到了相当好的支持。您需要大约 6GB 的空闲空间来存储 Android 代码文件。对于完整的构建，您需要 25GB 的空闲空间。如果您在虚拟机中使用 Linux，请确保 RAM 或交换空间至少为 16GB，并且您有 30GB 的磁盘空间来完成构建。

从 Android 版本 2.3（姜饼）开始，只能在 64 位计算机上构建系统。在 32 位机器上仍然可以使用 Froyo（Android 2.2）。但是，您仍然可以在 32 位计算机上使用构建脚本上的一些“技巧”构建后续版本，我稍后将描述。

以下步骤概述了设置构建环境和编译 Android 框架和内核所需的过程：

+   设置构建环境

+   下载 Android 框架源代码

+   构建 Android 框架

+   构建自定义内核

一般来说，您的（Ubuntu Linux）构建计算机需要以下内容：

+   Git 1.7 或更新版本（GIT 是一个源代码管理工具），用于构建 Gingerbread 和更高版本的 JDK 6，或用于构建 Froyo 和旧版本的 JDK 5

+   Python 2.5 - 2.7

+   GNU Make 3.81 - 3.82

## 如何做...

我们将首先通过以下步骤设置构建环境：

### 注意

以下所有步骤都针对 64 位 Ubuntu。

1.  通过执行以下命令安装所需的 JDK：

```kt
JDK6
sudo add-apt-repository "deb http://archive.canonical.com/ lucid partner"
sudo apt-get update
sudo apt-get install sun-java6-jdk
JDK5
sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu hardy main multiverse"
sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu hardy-updates main multiverse"
sudo apt-get update
sudo apt-get install sun-java5-jdk

```

1.  安装所需的库依赖项：

```kt
sudo apt-get install git-core gnupg flex bison gperf build-essential \
 zip curl zlib1g-dev libc6-dev lib32ncurses5-dev ia32-libs \ 
 x11proto-core-dev libx11-dev lib32readline5-dev lib32z-dev \ 
 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown \ 
 libxml2-utils xsltproc

```

1.  【可选】在 Ubuntu 10.10 上，`libGL.so.1`和`libGL.so`之间没有创建符号链接，这有时会导致构建过程失败：

```kt
sudo ln -s /usr/lib32/mesa/libGL.so.1 /usr/lib32/mesa/libGL.so

```

1.  【可选】在 Ubuntu 11.10 上，需要额外的依赖项：

```kt
sudo apt-get install libx11-dev:i386

```

1.  现在，我们将从 Google 的存储库中下载 Android 源代码。

1.  安装 repo。确保您有一个`/bin`目录，并且它存在于您的`PATH`变量中：

```kt
mkdir ~/bin 
PATH=~/bin:$PATH 
curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo 
chmod a+x ~/bin/repo

```

### 注意

Repo 是一个用于下载 Android 源代码的 Python 脚本，还可以执行其他任务。它旨在在 GIT 之上工作。

1.  初始化 repo。

1.  在此步骤中，您需要决定要下载的 Android 源代码的分支。如果您希望使用 Gerrit，这是用于源代码审查的工具，请确保您拥有一个活动的 Google 邮箱地址。当 repo 初始化时，您将被提示使用此电子邮件地址。

1.  在本地计算机上创建一个工作目录。我们将其称为`android_src`：

```kt
mkdir android_src
cd android_src

```

1.  以下命令将初始化 repo 以下载“master”分支：

```kt
repo init -u https://android.googlesource.com/platform/manifest

```

1.  以下命令将初始化 repo 以下载 Gingerbread 2.3.4 分支：

```kt
repo init -u https://android.googlesource.com/platform/manifest -b android-2.3.4_r1

```

`-b`开关用于指定要下载的分支。

1.  一旦配置好 repo，我们就可以获取源文件。命令的格式如下：

```kt
repo sync -jX

```

### 注意

`-jX`是可选的，用于并行获取。

1.  以下命令将同步 Android 框架的所有必要源文件。请注意，这些步骤仅用于下载 Android 框架文件。内核下载是一个单独的过程。

```kt
repo sync -j16

```

### 注意

源代码访问是匿名的，也就是说，您无需向 Google 注册即可下载源代码。服务器为访问源代码的每个 IP 地址分配了固定的配额。这是为了保护服务器免受过多的下载流量。如果您恰好处于 NAT 之后并与其他人共享 IP 地址，而这些人也希望下载代码，您可能会遇到来自源代码服务器的错误消息，警告有关过度使用。在这种情况下，您可以通过经过身份验证的访问来解决问题。在这种方法中，您将获得一个基于密码生成系统生成的用户 ID 的单独配额。密码生成器和相关说明可在[`android.googlesource.com/new-password`](https://android.googlesource.com/new-password)找到。

1.  一旦您获得了用户 ID/密码并适当设置了系统，您可以通过使用以下命令强制进行身份验证：

```kt
repo init -u https://android.googlesource.com/a/platform/manifest

```

注意 URI 中的`/a/`。这表示经过身份验证的访问。

### 注意

**代理问题**

如果您是从代理后面下载的，请设置以下环境变量：

`export HTTP_PROXY=http://<proxy_user_id>:<proxy_password>@<proxy_server>:<proxy_port>`

`export HTTPS_PROXY=http://<proxy_user_id>:<proxy_password>@<proxy_server>:<proxy_port>`

接下来，我们描述构建 Android 框架源代码所需的步骤：

1.  初始化终端环境。

1.  某些构建时工具需要包含在当前终端环境中。因此，请导航到您的源目录：

```kt
cd android_src/
source build/envsetup.sh

```

可以为各种目标构建源代码。每个目标描述符都采用`BUILD-BUILDTYPE`格式：

+   `BUILD`：指的是特定设备的源代码的特定组合。例如，`full_maguro`目标 Galaxy Nexus 或`generic`目标模拟器。

+   `BUILDTYPE`：可以是以下三个值之一：

+   `user`：适用于生产构建

+   `userdebug`：与`user`类似，但在 ADB 中具有根访问权限，以便更容易进行调试

+   `eng`：仅用于开发构建

1.  在我们当前的示例中，我们将为模拟器构建。发出以下命令来执行：

```kt
lunch full-eng

```

要实际构建代码，我们将使用`make`。格式如下：

```kt
make -jX

```

其中`X`表示并行构建的数量。通常的规则是：`X`是 CPU 核心数+2。

这是一个实验性的公式，读者可以随意测试不同的值。

1.  构建代码：

```kt
make -j6

```

现在，我们必须等待构建完成。根据您系统的规格，这可能需要 20 分钟到 1 小时不等。在成功构建结束时，输出看起来类似于以下内容（请注意，这可能会因目标而异）：

```kt
...
target Dex: SystemUI 
Copying: out/target/common/obj/APPS/SystemUI_intermediates/noproguard.classes.dex 
target Package: SystemUI (out/target/product/generic/obj/APPS/SystemUI_intermediates/package.apk) 
 'out/target/common/obj/APPS/SystemUI_intermediates//classes.dex' as 'classes.dex'... 
Install: out/target/product/generic/system/app/SystemUI.apk 
Finding NOTICE files: out/target/product/generic/obj/NOTICE_FILES/hash-timestamp 
Combining NOTICE files: out/target/product/generic/obj/NOTICE.html 
Target system fs image: out/target/product/generic/obj/PACKAGING/systemimage_intermediates/system.img 
Install system fs image: out/target/product/generic/system.img 
Installed file list: out/target/product/generic/installed-files.txt 
DroidDoc took 440 sec. to write docs to out/target/common/docs/doc-comment-check 

```

更好的检查成功构建的方法是检查以下目录中新创建的文件。

构建会在`android_src/out/target/product/<DEVICE>/`目录下生成一些主要文件，如下所示：

+   `system.img`：系统映像文件

+   `boot.img`：包含内核

+   `recovery.img`：包含设备恢复分区的代码

在模拟器构建的情况下，前面的文件将出现在`android_src/out/target/product/generic/`中。

1.  现在，我们可以通过发出`emulator`命令来测试我们的构建：

```kt
emulator

```

这将启动一个 Android 模拟器，如下截图所示，运行我们刚刚构建的代码：

![如何做...](img/9762OS_01_01.jpg)

### 注意

我们下载的代码包含每个支持的目标的预构建 Linux 内核。如果您只想更改框架文件，可以使用预构建的内核，这些内核会自动包含在构建映像中。如果您对内核进行特定更改，您将需要获取特定的内核并单独构建它（如下所示），稍后将对此进行解释：

`更快的构建 - CCACHE`

框架代码包含 C 语言和 Java 代码。大部分 C 语言代码存在为共享对象，在构建过程中构建。如果您发出`make clean`命令，它将删除所有构建的代码（仅删除构建输出目录也会产生相同的效果），然后重新构建，这将需要大量时间。如果对这些共享库没有进行任何更改，可以使用`CCACHE`来加快构建时间，这是一个编译器缓存。

1.  在源目录`android_src/`的根目录中，使用以下命令：

```kt
export USE_CCACHE=1
export CCACHE_DIR=<PATH_TO_YOUR_CCACHE_DIR>

```

设置缓存大小：

```kt
prebuilt/linux-x86/ccache/ccache -M 50G

```

这将保留 50GB 的缓存大小。

要查看缓存在构建过程中的使用情况，请使用以下命令（在另一个终端中导航到您的源目录）：

```kt
watch -n1 -d prebuilt/linux-x86/ccache/ccache -s

```

在这部分中，我们将获取源代码并构建`goldfish`模拟器内核。为设备构建内核的方式类似。

### 注意

`goldfish`是为 Android QEMU 模拟器修改的内核的名称。

1.  获取内核源代码：

创建`android_src`的子目录：

```kt
mkdir kernel_code
cd kernel_code
git clone https://android.googlesource.com/kernel/goldfish.git
git branch -r

```

这将克隆`goldfish.git`到一个名为`goldfish`的文件夹中（自动创建），然后列出可用的远程分支。输出应该如下所示（在执行`git`分支后看到）：

```kt
origin/HEAD -> origin/master 
 origin/android-goldfish-2.6.29 
 origin/linux-goldfish-3.0-wip 
 origin/master

```

在下面的命令中，我们注意到`origin/android-goldfish-2.6.29`，这是我们希望获取的内核：

```kt
cd goldfish
git checkout --track -b android-goldfish-2.6.29 origin/android-goldfish-2.6.29

```

这将获取内核代码：

1.  设置构建环境。

1.  我们需要通过更新系统`PATH`变量来初始化终端环境，以指向将用于编译 Linux 内核的交叉编译器。这个交叉编译器已经作为 Android 框架源代码的预构建二进制文件进行了分发：

```kt
export PATH=<PATH_TO_YOUR_ANDROID_SRC_DIR>/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin:$PATH

```

1.  运行模拟器（您可以选择使用我们之前构建的系统镜像运行模拟器。我们需要这样做来获取内核配置文件。而不是手动配置它，我们选择拉取正在运行内核的配置文件。

### 提示

确保 ADB 仍然在您的路径中。如果您自从构建框架代码以来没有关闭终端窗口，它将在您的`PATH`变量中，否则按顺序执行以下步骤。

（请注意，您必须更改目录到`ANDROID_SRC`以执行以下命令）。

```kt
source build/envsetup.sh
lunch full_eng
adb pull /proc/config.gz
gunzip config.gz 
cp config .config

```

上述命令将把正在运行的内核的配置文件复制到我们的内核构建树中。

1.  开始编译过程：

```kt
export ARCH=arm
export SUBARCH=arm 
make

```

如果出现以下情况：

```kt
Misc devices (MISC_DEVICES) [Y/n/?] y 
 Android pmem allocator (ANDROID_PMEM) [Y/n] y 
 Enclosure Services (ENCLOSURE_SERVICES) [N/y/?] n 
 Kernel Debugger Core (KERNEL_DEBUGGER_CORE) [N/y/?] n 
 UID based statistics tracking exported to /proc/uid_stat (UID_STAT) [N/y] n 
 Virtual Device for QEMU tracing (QEMU_TRACE) [Y/n/?] y 
 Virtual Device for QEMU pipes (QEMU_PIPE) [N/y/?] (NEW)

```

输入`y`作为答案。这是构建所需的一些额外的特定于 Android 的配置。

1.  现在我们必须等待构建完成。构建输出的最后几行应该如下所示（请注意，这可能会根据您的目标而改变）：

```kt
...
 LD      vmlinux 
 SYSMAP  System.map 
 SYSMAP  .tmp_System.map 
 OBJCOPY arch/arm/boot/Image 
 Kernel: arch/arm/boot/Image is ready 
 AS      arch/arm/boot/compressed/head.o 
 GZIP    arch/arm/boot/compressed/piggy.gz 
 AS      arch/arm/boot/compressed/piggy.o 
 CC      arch/arm/boot/compressed/misc.o 
 LD      arch/arm/boot/compressed/vmlinux 
 OBJCOPY arch/arm/boot/zImage 
 Kernel: arch/arm/boot/zImage is ready

```

正如最后一行所述，新的`zImage`可在`arch/arm/boot/`中找到。

1.  为了测试它，我们使用这个新构建的镜像启动模拟器。

1.  将`zImage`复制到适当的目录。我只是将它复制到`android_src/`中：

```kt
emulator -kernel zImage

```

1.  要验证模拟器确实正在运行我们的内核，请使用以下命令：

```kt
adb shell 
# cat /proc/version 

```

输出将如下所示：

```kt
Linux version 2.6.29-gef9c64a (earlence@earlence-Satellite-L650) (gcc version 4.4.3 (GCC) ) #1 Mon Jun 4 16:35:00 CEST 2012

```

这是我们的自定义内核，因为我们观察到自定义构建字符串（`earlence@earlence-Satellite-L650`）以及编译时间都存在。构建字符串将是您计算机的名称。

1.  一旦模拟器启动，您将看到一个类似于以下内容的窗口：![如何做...](img/9762OS_01_02.jpg)

以下是在 32 位系统上构建框架所需的步骤：

1.  对 32 位 Ubuntu 进行以下简单更改以构建 Gingerbread。请注意，这些步骤假设您已经为 Froyo 构建设置了系统。假设 Froyo 构建计算机设置，以下步骤将指导您逐步进行更改，使 Gingerbread 和以后的构建成为可能。要设置 Froyo，请按照[`source.android.com/source/initializing.html`](http://source.android.com/source/initializing.html)中解释的步骤进行。在`build/core/main.mk`中，将`ifneq (64,$(findstring 64,$(build_arch)))`更改为`ifneq (i686,$(findstring i686,$(build_arch)))`。

请注意该行上有两个更改。

1.  在以下文件中：

+   `external/clearsilver/cgi/Android.mk`

+   `external/clearsilver/java-jni/Android.mk`

+   `external/clearsilver/util/Android.mk`

+   `external/clearsilver/cs/Android.mk`

更改为：

```kt
LOCAL_CFLAGS += -m64 
LOCAL_LDFLAGS += -m64
```

转换为：

```kt
LOCAL_CFLAGS += -m32 
LOCAL_LDFLAGS += -m32
```

1.  安装以下软件包（除了 Froyo 构建必须安装的软件包）：

```kt
sudo apt-get install lib64z1-dev libc6-dev-amd64 g++-multilib 
lib64stdc++6 

```

1.  使用以下命令安装 Java 1.6：

```kt
sudo apt-get install sun-java6-jdk

```

## 工作原理…

Android 构建系统是几个标准工具和自定义包装器的组合。Repo 就是这样一个包装器脚本，它负责 GIT 操作，并使我们更容易地使用 Android 源代码。

内核树与框架源树是分开维护的。因此，如果您需要对特定内核进行自定义，您将需要单独下载和构建它。热心的读者可能会想知道，如果我们在编译框架时从未构建过内核，我们是如何能够运行模拟器的。Android 框架源代码包括某些目标的预构建二进制文件。这些二进制文件位于框架源根目录下的`/prebuilt`目录中。

内核构建过程与构建桌面系统的内核几乎相同。只有少量特定于 Android 的编译开关，我们已经证明可以在现有配置文件的情况下轻松配置为适合预期目标。

源代码包括 C/C++和 Java 代码。框架不包括内核源代码，因为这些源代码是在单独的 GIT 树中维护的。在下一个步骤中，我们将解释框架代码的组织。了解在开发自定义构建时如何以及在哪里进行更改是很重要的。

# 分析源代码结构（必须知道）

在这个步骤中，我们将分析框架源代码的源代码结构。

## 准备工作

您需要使用合适的代码编辑器/查看器。我通常使用 gedit，并启用了几个与代码相关的选项。有些人更喜欢使用 vi、emacs 或 Eclipse。使用您熟悉的工具查看源代码。

## 如何做…

阅读以下表格时，请参考您的 Android 源代码副本的目录，并随意探索子目录。顶层文件夹如下：

### 注意

除非另有说明，否则所有文件夹均相对于 Android 源代码根目录。还要注意，随着在后续 Android 版本中添加新文件夹或子文件夹，源代码结构可能会发生变化。这个描述是针对 Gingerbread 的。

| 目录名称 | 描述 |
| --- | --- |
| `ANDROID_SRC/bionic/` | 这包含了由谷歌专门为 Android 开发的最小的 libc（标准 C 库子集）实现。除了链接器外，它还包含了 libm、libstdc++和动态链接库的源代码。 |
| `ANDROID_SRC/bootable/` | 包括引导加载程序示例。它还包含了恢复环境的代码。 |
| `ANDROID_SRC/build/` | 包含用于构建和维护 Android 框架源代码分发的所有构建脚本。包括`envsetup.h`文件。 |
| `ANDROID_SRC/cts/` | 包含用于验证框架不同部分的测试用例。 |
| `ANDROID_SRC/dalvik/` | 包含 Dalvik 虚拟机源代码、`dx`工具（将 Java `.class`文件转换为`.dex`文件）、dalvik 支持基础设施和核心类库实现。 |
| `ANDROID_SRC/development/` | 包含各种开发工具，用于构建 SDK 版本和 NDK 版本的脚本。 |
| `ANDROID_SRC/device/` | 设备特定代码，如平台库、附加组件、硬件抽象代码等。 |
| `ANDROID_SRC/external/` | 这是安卓内部使用的开源外部项目的本地副本，如 SQLite 和 WebKit。 |
| `ANDROID_SRC/frameworks/` | 包含所有核心框架代码。 |
| `frameworks/base/core` | 包含所有安卓类库代码。这与每个安卓应用程序链接。 |
| `frameworks/base/libs` | 这里最重要的是`/binder`目录，其中包含了 binder IPC（进程间通信）框架的源代码。 |
| `frameworks/base/services` | 运行时系统服务器。这代表了安卓为用户应用程序提供的核心功能。 |
| `ANDROID_SRC/packages/` | 包含各种用户空间安卓应用程序，包括系统应用程序，如设置、时钟等。 |
| `ANDROID_SRC/prebuilt/` | 包含不同主机环境的编译器、链接器的二进制文件，以及安卓的预构建 Linux 内核映像。 |
| `ANDROID_SRC/system/` | 包含几个本地代码（`.C`）文件，当框架启动后充当最小文件系统。这些工具用于基本的引导、操作和调试。 |

## 它是如何工作的…

所有这些子目录都是安卓框架的一部分（请注意，它们还包含不属于框架的代码，比如`/system`下的代码，但是框架的确切定义可以放宽一些）。在系统构建过程中，大多数这些都是通过这些目录中存在的安卓 make 文件一起拉在一起的。不建议创建新文件夹，因为所有特定于供应商的代码都可以添加到`/vendor`目录下（之前没有显示）。当为特定设备构建时，将创建此目录，并且其中包含专有二进制文件等内容，例如特定于供应商的框架代码。

# 系统引导顺序（必须知道）

在这个步骤中，我们将介绍系统在启动过程中执行的步骤。在我们引导您完成过程时，请参考这里提到的文件。

## 准备工作

准备好你的代码编辑器/查看器，因为我们将打开许多源文件并检查它们的内容。

安卓手机的启动有三个主要阶段，如下所示：

+   **第一阶段-固件启动**：上电后，固件开始执行。这通常是第一阶段的引导加载程序。最终，内核被加载到 RAM 中，并且执行了一个跳转到内核入口点。

+   **第二阶段-内核启动**：内核通过其通常的启动过程启动。内存和 I/O 被初始化。中断被启用，进程表被创建，最终运行`init`。

+   **第三阶段-用户空间框架启动**：这个过程有三个步骤。它从执行`init.rc`脚本开始。它位于`ANDROID_SRC/system/core/rootdir/init.rc`。

## 如何做…

1.  每当我们引用源代码时，都要在代码查看器中打开该文件。

1.  如果我们分析启动脚本的内容，我们会发现它设置了各种环境变量，包括`PATH`和`BOOTCLASSPATH`，其中包含了安卓进程所需的 Java 库的路径。

1.  之后，它创建了一堆目录并设置了适当的访问权限。它还为核心服务写入各种配置参数，例如`lowmemorykiller`。它通过`/proc`内核接口完成这些操作。以下是`init.rc`的一个示例提取：

```kt
# Write value must be consistent with the above properties. 
# Note that the driver only supports 6 slots, so we have combined some of 
# the classes into the same memory level; the associated processes of higher 
# classes will still be killed first. 
 write /sys/module/lowmemorykiller/parameters/adj 0,1,2,4,7,15

```

## 它是如何工作的…

安卓有一种特定的`init`语言，详细描述在安卓源代码的以下位置：

```kt
ANDROID_SRC/system/core/init/readme.txt
```

随后，`Zygote`和`system_server`有一个启动过程。

### 注意

从内核的角度来看，`Zygote`是第二个`init`进程，从框架的角度来看，是第一个 Android 进程。

以下是`init.rc`中`Zygote`的初始化提取：

```kt
service zygote /system/bin/app_process -Xzygote /system/bin --zygote –start-system-server

```

在这里，`app_process`是一个二进制文件，在系统初始化期间启动`zygote`进程。最后一个标志(`--start-system-server`)表示要启动`system_server`进程。`system_server`进程包含 Android 平台提供的所有核心服务。例如`ActivityManagerService`、`LocationManagerService`等。

`app_process`二进制文件调用`AndroidRuntime`的功能，这是启动 dalvik 环境的入口点；`AndroidRuntime.cpp`位于`ANDROID_SRC/frameworks/base/core/jni/`。

`app_process`二进制代码最终归结为`AndroidRuntime.cpp`中的以下内容：

```kt
void AndroidRuntime::start(const char* className, const bool startSystemServer)
```

然后调用`int AndroidRuntime::startVm(JavaVM** pJavaVM, JNIEnv** pEnv)`。

然后，上述代码行将 DVM 加载到本机进程中，并导致调用位于`ANDROID_SRC/frameworks/base/core/java/com/android/internal/os`的`ZygoteInit.java`的`main`方法。

调用`ZygoteInit.main()`方法，导致在该文件中调用`startSystemServer`。此方法将命令行参数传递给系统服务器。提取如下所示：

```kt
String args[] = { 
            "--setuid=1000", 
            "--setgid=1000", 
            "--setgroups=1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1018,3001,3002,3003",
            "--capabilities=130104352,130104352", 
            "--runtime-init", 
            "--nice-name=system_server", 
            "com.android.server.SystemServer", 
        };
```

启动系统服务器（详细信息将在后面介绍）后，设置`zygote`套接字，然后进程以“选择循环模式”运行。在此模式下，进程会旋转等待启动新的 Android 进程的请求。

现在我们将看一下`system_server`的启动过程。该过程的代码可以在`frameworks/base/services/java/com/android/server/SystemServer.java`中找到。

在启动期间调用了两种方法，其中一种在这里显示：

```kt
native public static void init1(String[] args);
```

此方法由`zygote`调用（如前所述），其工作是初始化存在于`android_servers`本机对象文件中的本机服务。这些本机服务的示例包括`SurfaceFlinger`、`AudioFlinger`等。`init1()`方法实现在`ANDROID_SRC/frameworks/base/cmds/system_server/library/system_init.cpp`中。

`init1()`方法通过`system_init.cpp`中的以下行引导`init2`：

```kt
runtime->callStatic("com/android/server/SystemServer", "init2");
```

现在，执行又回到了`SystemServer.java`内部，并运行了`init2`。它创建了一个线程，然后继续启动系统服务器。

服务器的示例启动序列如下（基于 Gingerbread 2.3.4_r1）：

1.  熵服务

1.  电源管理器

1.  活动管理器

1.  电话注册表

1.  包管理器

1.  帐户管理器

1.  内容管理器

此代码可以在`SystemServer.java`的`run()`方法中看到。一旦启动`ActivityManagerService`类（并完成引导），就会启动前几个 Android 应用程序，如下所示：

```kt
com.android.phone – The phone app.
android.process.acore – The home screen and core apps.
```

### 注意

每次需要新的 Android 进程时，都会分叉 Zygote。因此，当用户通过 UI 启动应用程序时，Zygote 会分叉并创建一个进程。

完整的引导系统序列如下图所示：

![它是如何工作的...](img/9762OS_02_01.jpg)

## 还有更多

现在，我们将看一下在为 Android 编写代码时应遵循的一些良好和安全的代码风格指针。

### 安全编码指南

在修改系统时，遵守安全编码规范至关重要。作为开发人员，您有责任确保您的更改不会无意中使系统变得不安全。为了帮助您进行此过程，请记住以下几点：

+   **始终使用权限字符串来保护功能**：每当您向系统添加新功能时，请使用`checkPermission(...)`调用来保护方法。

+   **确保修改的代码不会规避权限检查**：框架代码通常在执行方法的功能之前调用`checkPermission(...)`方法。当你修改这样的代码时，确保没有引入绕过检查的代码路径。使用本地测试用例来做到这一点。如果存在用于你正在修改的方法的测试用例，请在进行修改后执行它们。

+   **阅读代码中的安全文档**：许多系统服务，例如`PackageManagerService.java`，都有以注释形式的内部文档。只需按照这些说明。

+   **记录你新添加的代码**：如果有特定的安全指南需要遵循，如果有人修改了你的代码，清楚地提到这些。

# 创建一个基本的接口文件（必须知道）

我们将应用先前学到的概念，比如与构建系统、Android 启动过程和常见的 Android 系统设计模式的工作，来构建一个完整的工作系统，其中包括一个自定义的系统服务。在我们的示例中，我们将创建一个简单的服务来实现一个小的哈希函数。然后我们将把这个服务添加到启动过程中。正如在常见设计模式的配方中所述，系统服务是一个长时间运行的任务，实现一些功能，比如提供设备的 GPS 坐标。

接口文件是用**Android 接口定义语言**（**AIDL**）编写的。该接口表示服务的公共远程接口。

## 做好准备

我们将在`ANDROID_SRC/frameworks/base/core/java/android/os/packt`编写代码。代码写在这个位置是因为它遵循 Android 系统编码的惯例，更重要的是，构建系统被设计为自动从这些预先知道的位置中挑选文件。因此，为了避免对构建系统的修改，我们在标准位置编写我们的代码。另一个原因是，由于我们正在编写框架级扩展，它们必须与框架紧密集成，上述位置是所有这类代码的编写位置。

在`/os`下创建一个名为`packt`的目录。这有助于我们更好地组织代码，并轻松区分自定义代码和框架代码。这是很重要的，因为你正在修改一个已经经过测试的开源系统。仅仅因为 Android 的规模庞大，对代码进行不加区分的更改可能会引入非常难以找到的错误。因此，在新添加的代码和框架代码之间有一个明确的分隔是一个好的做法。

## 如何做...

以下代码表示系统服务的 Android 接口定义。它指定了服务为客户端使用暴露的方法。

我们的示例暴露了一个名为`getMD5(String)`的单一方法，用于计算输入参数的 MD5 哈希。

将以下代码文件保存为`IPacktCrypto.aidl`：

```kt
package android.os.packt; 

interface IPacktCrypto 
{ 
  byte [] getMD5(String data); 
}
```

我们必须将这个新文件传达给构建系统。这是通过在位于`ANDROID_SRC/frameworks/base`的`Android.mk`文件中的`LOCAL_SRC_FILES`条目中添加以下行来完成的。

滚动到`LOCAL_SRC_FILES`指令。最后几行应该如下所示（姜饼 2.3.4_r1）：

```kt
…
voip/java/android/net/sip/ISipSessionListener.aidl \ 
voip/java/android/net/sip/ISipService.aidl \ 
core/java/android/os/packt/IPacktCrypto.aidl
```

请注意，我们已经指定了我们新添加的接口定义文件。在这里包含文件名会导致对文件进行 AIDL 编译，以生成代理和存根类（代理/存根类包含编组代码）。

## 它是如何工作的...

代理/存根编组代码是必需的，因为系统服务器在自己的进程中运行。因此，为了从其他进程调用它的函数，你需要一个中间层，将调用从一个进程编组到另一个进程。在 Android 的情况下，生成的存根/代理类构成了这一层。

AIDL 文件必须由构建系统借助 AIDL 编译器进行编译。因此，我们在 make 文件的现有框架文件末尾列出我们的文件名。当系统正在构建时，AIDL 将在`IPacktCrypto.aidl`上调用，并将生成代理和存根类。这些类生成在`android_src/out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/src/core/java/android/os`。

# 创建系统服务骨架（必须知道）

在本教程中，我们将创建自定义系统服务的概要代码。这将帮助我们了解系统服务的基本机制和组件。为了提醒您，系统服务是提供有用功能给 Android 应用程序的长时间运行的任务。一个例子是与 GPS 硬件进行接口并提供诸如接近警报之类的服务的 GPS 服务。

## 准备工作

我们将把系统服务器代码添加到`ANDROID_SRC/frameworks/base/services/java/com/android`。

在上述位置创建一个名为`packt`的目录。在该目录中，创建一个名为`PacktCrypto.java`的文件。

## 如何做…

1.  编写以下代码并将其保存为`PacktCrypto.java`。这是主要的系统服务类文件：

```kt
package com.android.packt; 

import java.security.MessageDigest; 
import java.io.UnsupportedEncodingException; 
import java.security.NoSuchAlgorithmException; 
import android.util.Log; 
import android.os.packt.IPacktCrypto; 
/* 
Our system server provides a hashing service 
*/ 
public class PacktCrypto extends IPacktCrypto.Stub
{ 
  private static final String TAG = "PacktCrypto"; //use this for logging 
  private static PacktCrypto mSelf; 

  private PacktCrypto() 
  { 
    //perform one-time initialization here 
    //if needed. 
  } 
  /* 
  This is a singleton pattern. Only one instance of PacktCrypto may exist 
  in the entire system 
  */ 
  public static PacktCrypto getInstance() 
  { 
    if(mSelf == null) 
      mSelf = new PacktCrypto(); 

    return mSelf; 
  }
```

### 提示

**下载示例代码**

您可以从[`www.packtpub.com`](http://www.packtpub.com)的帐户中下载您购买的所有 Packt 图书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便直接通过电子邮件接收文件。

1.  以下方法实现了系统服务的功能。您可能已经注意到，方法名和签名与 AIDL 文件中指定的方法名和签名相同。这一点非常重要，因为它必须匹配才能生成正确的编组代码。

```kt
  /* 
  The interface method specified in IPacktCrypto.aidl 
  */ 
  public byte [] getMD5(String data) 
  { 
    byte [] dataBytes = null; 
    MessageDigest md5 = null; 

    try { 

      dataBytes = data.getBytes("UTF-8"); 
      md5 = MessageDigest.getInstance("MD5");
      Log.i(TAG, "MD5 digestion invoked"); 

    } catch(java.io.UnsupportedEncodingException uee) 
    { 
      Log.e(TAG, "Unsupported Encoding UTF8"); 
    } 
    catch(java.security.NoSuchAlgorithmException nsae) 
    { 
      Log.e(TAG, "No Algorithm MD5"); 
    } 
    catch(Exception e)

    {
 }
    if(md5 != null) 
      return md5.digest(dataBytes); 
    else 
      return null; 
  } 
}
```

上述代码块代表了系统服务。它扩展了`IPacktCrypto.Stub`，这是在运行`IPacktCrypto.aidl`文件时 AIDL 编译器将生成的存根类。我们使用单例模式来实例化该类，以确保系统中只存在一个`PacktCrypto`对象。我们需要确保这一点，因为只有一个服务将被输入到服务目录中。该代码还说明了各种其他最佳实践。例如，使用日志标签和服务实例化的单例模式。这些都是系统服务的常见编码风格。

在这个阶段，我们已经为我们的服务创建了一个接口定义，创建了获取服务引用的方法，同时我们也实现了服务提供的功能，即哈希函数。

1.  现在我们可以运行一个测试构建，确保一切都编译正常。打开终端模拟器并为模拟器启动构建：

```kt
cd android_src
. build/envsetup.sh
lunch
select option for generic-eng
make -jX where X = number of CPU cores + 2

```

## 工作原理…

传统上，代理类代表在远程请求的客户端端执行的代码组件。同样，存根在服务器端执行。因此，我们的系统服务器扩展了从`IPacktCrypto.aidl`文件生成的`IPacktCrypto.Stub`。我们还必须实现`getMD5()`接口方法，因为它将为客户端提供所需的功能。我们选择利用单例模式来保证系统中只存在一个服务对象。这是有道理的，因为服务目录中可能只存在一个系统服务的副本。

# 将自定义服务添加到 SystemServer 进程（必须知道）

我们需要在 Android 系统中注册我们的服务并创建一个对象。Service Manager 是一个组件，它维护服务名称和相关服务对象的映射。进程通过名称调用 Service Manager 来获取对系统服务器的引用。用于获取服务对象引用的方法是`ServiceManager.getService(String)`。您可以将 Service Manager 视为对服务消费者可用的目录服务。

## 准备工作

我们将把我们的自定义服务器添加到位于`ANDROID_SRC/frameworks/base/services/java/com/android/server`的`SystemServer.java`中。

## 如何做…

以下代码表示您需要对`SystemServer.java`文件进行的修改。找到`run()`方法，并在适当的位置添加以下行。为了举例说明，我们选择在所有服务启动后添加这些行。

```kt
...
//begin packt 
Slog.i(TAG, "PacktCrypto service"); 
com.android.packt.PacktCrypto pcrypt = com.android.packt.PacktCrypto.getInstance(); 
ServiceManager.addService("PacktCryptoService", pcrypt); 
//end packt
…
```

## 它是如何工作的…

前面的代码修改获取了一个`PacktCrypto`类型对象的引用。然后将该对象添加到`ServiceManager`类中，如果您还记得的话，这是所有系统服务的目录服务。它通过调用`addService()`方法将`PacktCrypto`对象添加到目录中，该方法接受一个字符串服务标识符和对象本身作为参数。

在代码片段中，我们必须创建一个`PacktCrypto`对象，并将其添加到带有字符串名称的 Service Manager 目录中。我们选择`PacktCryptoService`作为示例。在这个阶段，我们的自定义服务器将被创建并注册到 Service Manager 中。

# 测试 PacktCrypto 服务（必须知道）

在这个阶段，我们已经准备好测试自定义服务（`PacktCrypto`），该服务为客户端提供了哈希功能。因此，我们将在`SystemServer`进程中编写一个小的测试用例。此用例在自定义服务器启动后执行。

## 准备工作

在`SystemServer.java`中的服务创建位置，也就是在*向 SystemServer 进程添加自定义服务*配方中进行更改的位置后，编写以下测试代码。

## 如何做…

1.  以下代码行是添加到`SystemServer.java`的几行代码。将其添加到服务对象创建后的位置：

```kt
        //PacktCrypto Test start
android.os.packt.IPacktCrypto ipc = android.os.packt.IPacktCrypto.Stub.asInterface(ServiceManager.getService("PacktCryptoService")); 
try { 
            byte [] hash = ipc.getMD5("packttest"); 

            StringBuffer sb = new StringBuffer(); 
          for (int i = 0; i < hash.length; i++) 
          { 
              sb.append(Integer.toString((hash[i] & 0xff) + 0x100, 16).substring(1)); 
          } 

          Log.i("PacktTest", "MD5 sum: " + sb.toString()); 

} catch(RemoteException re1) 
{ 
            Log.e("PacktTest", "remote exception"); 
} 
//PacktCrypto Test end
```

1.  在添加了代码之后，运行 make，然后启动模拟器（有关如何执行此操作的步骤，请参考第一个配方中描述的步骤）。

1.  在 logcat 中的输出应该如下（在将适当的过滤器应用到日志输出之后）。例如，如果我们使用以下日志命令：

```kt
adb logcat *:S PacktCrypto:I PacktTest:I

```

输出将是：

```kt
I/PacktCrypto(69): MD5 digestion invoked
I/PacktTest(67): MD5 sum: 422164113c2fc595dd0ab44a18925ac5

```

## 它是如何工作的…

前面的代码使用`IPacktCrypto`的实例来存储从 Service Manager 获取的`PacktCrypto`的引用。然后我们调用`getMD5()`方法，传入一个测试字符串。然后我们打印输出。由于这是一个跨进程调用，可能会发生`RemoteException`。

# 分析 Android 系统分区（必须知道）

Android 手机包含一些基本分区以及其他支持分区。了解代码是如何以及在哪里刷入设备的知识至关重要。

## 准备工作

在我们的测试设备——三星 Galaxy Nexus（或模拟器）上，我们可以使用以下命令在 adb shell 中执行以查看这些分区。要在设备上获取 shell，您应该通过 USB 连接设备，并确保**USB 调试**选项已启用（位于**设置** | **开发人员选项**）。

### 注意

如果您使用的是 Jellybean 或更高版本，则该选项是隐藏的，因此您需要转到**设置** | **关于手机**，并继续点击版本号，直到出现一个 Toast，表示您现在是开发人员。**开发人员选项**将出现在通常的位置。

最后，要实际获取一个 shell，在设备连接的情况下，启动终端并输入`adb shell`，然后按*Enter*。

### 注意

有时，Linux 无法检测到 Android 设备，在这些情况下，您需要编辑 USB 规则文件。由于这不是一个系统开发问题，而是 SDK 开发人员经常遇到的问题，我们不会在这里详细介绍步骤。

## 如何做…

1.  在连接三星 Galaxy Nexus 并启用调试的终端中执行以下命令。当我们列出设备的分区时，将生成以下输出：

```kt
shell@android:/ $ ls -l /dev/block/platform/omap/omap_hsmmc.0/by-name 
lrwxrwxrwx root     root              2012-06-28 22:03 boot -> /dev/block/mmcblk0p7 
lrwxrwxrwx root     root              2012-06-28 22:03 cache -> /dev/block/mmcblk0p11 
lrwxrwxrwx root     root              2012-06-28 22:03 dgs -> /dev/block/mmcblk0p6 
lrwxrwxrwx root     root              2012-06-28 22:03 efs -> /dev/block/mmcblk0p3 
lrwxrwxrwx root     root              2012-06-28 22:03 metadata -> /dev/block/mmcblk0p13 
lrwxrwxrwx root     root              2012-06-28 22:03 misc -> /dev/block/mmcblk0p5 
lrwxrwxrwx root     root              2012-06-28 22:03 param -> /dev/block/mmcblk0p4 
lrwxrwxrwx root     root              2012-06-28 22:03 radio -> /dev/block/mmcblk0p9 
lrwxrwxrwx root     root              2012-06-28 22:03 recovery -> /dev/block/mmcblk0p8 
lrwxrwxrwx root     root              2012-06-28 22:03 sbl -> /dev/block/mmcblk0p2 
lrwxrwxrwx root     root              2012-06-28 22:03 system -> /dev/block/mmcblk0p10 
lrwxrwxrwx root     root              2012-06-28 22:03 userdata -> /dev/block/mmcblk0p12 
lrwxrwxrwx root     root              2012-06-28 22:03 xloader -> /dev/block/mmcblk0p1 

```

1.  同样，在 Nexus S 和 Nexus One 设备上，我们可以使用以下命令查看已挂载的分区。以下命令列出了`mtd` proc 文件的内容：

```kt
cat /proc/mtd

```

输出看起来类似于以下内容：

```kt
dev:    size   erasesize  name 
mtd0: 00200000 00040000 "bootloader" 
mtd1: 00140000 00040000 "misc" 
mtd2: 00800000 00040000 "boot" 
mtd3: 00800000 00040000 "recovery" 
mtd4: 1d580000 00040000 "cache" 
mtd5: 00d80000 00040000 "radio" 
mtd6: 006c0000 00040000 "efs"

```

1.  当在 Nexus One 上执行相同的命令时，观察到以下输出：

```kt
dev:    size   erasesize  name 
mtd0: 00040000 00020000 "misc" 
mtd1: 00500000 00020000 "recovery" 
mtd2: 00280000 00020000 "boot" 
mtd3: 04380000 00020000 "system" 
mtd4: 04380000 00020000 "cache" 
mtd5: 04ac0000 00020000 "userdata"

```

### 注意

请注意，您看到的设备输出可能略有不同。

## 它是如何工作的…

这里需要注意的主要是存在一些常见分区，这些分区对于刷写新软件非常重要。以下是大多数 Android 设备上的主要分区：

+   `/boot`：这包含内核映像和相关的 RAM 磁盘。在启动过程中由引导加载程序执行。任何新构建的内核都将写入此分区。如果此分区为空，手机将无法启动。

+   `/system`：这包含 Android 框架和相关的系统应用程序。在系统操作期间，这被挂载为只读，以便关键系统文件永远不会被修改。

+   `/recovery`：是用于将设备引导到恢复模式的备用引导分区。恢复代码位于`ANDROID_SRC/bootable/recovery`。有许多自定义恢复固件映像可用。一个著名的例子是`ClockWorkMod`。

## 还有更多...

刚才提到的三个分区是刷写 Android 新版本到设备上所涉及的分区。除此之外，还有一些其他分区存在：

+   `/data`：这包含用户数据，有时被称为用户数据分区。所有用户安装的应用程序、设置和个人数据都存储在此分区中。

+   `/cache`：将包含频繁访问的应用程序。

+   `/sdcard`：这是连接到手机的 SD 卡。这不是内部设备存储器上的分区。

# 为特定设备编译（必须知道）

特定于设备的二进制文件被刷写到刚才描述的各个分区。框架需要为特定目标进行编译。目标代表要刷写二进制文件的设备。

### 注意

构建变体：构建系统提供了几种类型的构建。这些构建会对最终二进制文件进行轻微更改。

`engineering`（`eng`）：是默认选项。普通 make 默认为此。包括所有标记为`eng`、`user`、`debug`和`userdebug`的模块。ADB 已启用，并将以 root 用户身份运行命令。

`user`：这是用于最终生产构建的。ADB 被禁用，不会以 root 用户身份运行命令。

`userdebug`：基本上与`user`相同，但系统是可调试的，并且 ADB 默认启用。

所有这些标签都分配给`Android.mk`文件中的项目。如果您打开其中任何一个文件，都会在`LOCAL_MODULE_TAGS`命令的帮助下提到它。

## 准备工作

转到您的 Android 源目录，并像往常一样包含构建环境（`source build/envsetup.sh`）。

## 如何做…

1.  要获取您正在使用的源版本支持的可用目标列表，请在终端中使用`lunch`命令：

```kt
lunch
The output (for 2.3.4_r1) should look like:
You're building on Linux
Lunch menu... pick a combo:
 1\. generic-eng
 2\. simulator
 3\. full_passion-userdebug
full_crespo-userdebug

```

1.  在为特定目标启动构建之前，您需要获取手机的专有二进制文件并将其解压缩到源目录中。通常，二进制文件附带协议和解压缩脚本。在浏览协议后，键入`I AGREE`并按*Enter*。所需的文件将解压缩到源目录中的正确位置。

这是一个下载 Nexus S 的 Orientation Sensor，Build GRJ22 的示例：

1.  导航到[`developers.google.com/android/nexus/drivers#crespogrj22`](https://developers.google.com/android/nexus/drivers#crespogrj22)。

1.  下载方向传感器的 ZIP 文件，并将其放置在计算机上的 Android 源目录中。

1.  将其解压缩到当前目录。将创建一个名为`extract-akm-crespo.sh`的文件。

1.  执行它并滚动到协议。最后，输入`I AGREE`。然后将提取二进制文件。

1.  对设备的其他文件执行类似的过程。

### 注意

Nexus One 的二进制文件必须从设备本身提取。在源目录`ANDROID_SRC/device/htc/passion`中，存在一个 shell 脚本，用于直接从设备中提取所需的二进制文件。将您的 Nexus One 连接到计算机，并通过 adb 执行以下操作：

`./extract-files.sh`

这将提取各种专有二进制文件并将其复制到源目录中的适当位置（`ANDROID_SRC/vendor/`）。

## 它是如何工作的…

`lunch`命令是构建环境的一部分。它提供了您可以使用当前源分发构建的可用目标列表。在所有分发中，模拟器和`generic-eng`目标都是可用的。在 QEMU 模拟器可用之前，曾使用模拟器。此目标现已过时，不应再使用。

我们可以为通用目标（模拟器）或模拟器目标构建代码（当前已过时，在 QEMU 模拟器尚未准备好时存在）。更有趣的选项是`full_passion-userdebug`和`full_crespo-userbedug`。第一个代表 Google Nexus One 设备。Passion 是该设备的代号。类似地，后者代表 Google Nexus S。

+   Google Nexus One – Passion

+   Google Nexus S – Crespo + crespo4g

+   Galaxy Nexus – Maguro + Toro

因此，根据您的目标设备，您可以选择所需的构建目标并执行 make 命令。

尽管 Android 是一个开源项目，但某些硬件驱动程序是闭源的。这些包括图形驱动程序，某些型号的 WiFi 芯片组驱动程序，方向传感器，无线电基带软件和摄像头驱动程序。因此，如果您只使用从 Android GIT 树下载的源代码构建，某些手机功能将无法正常工作。例如，如果没有包含正确的无线电图像，您将无法进行电话呼叫和接听电话。但是，这些驱动程序以二进制格式提供下载，网址为[`developers.google.com/android/nexus/drivers`](https://developers.google.com/android/nexus/drivers)。

## 还有更多…

如果您的自定义代码出现问题，设备变得无法使用，您需要将其恢复到工作状态。Google 为其开发者设备提供了工厂映像。它们包含了通常的`system.img`，`boot.img`和`recovery.img`映像，可以将设备恢复到出厂状态。这些可以在[`developers.google.com/android/nexus/images`](https://developers.google.com/android/nexus/images)上找到。

### 注意

Nexus One 的文件在该位置不可用，您需要从其他位置获取，例如 Cyanogen Mod 或 modaco。

在包含专有二进制文件后，您可能需要执行以下命令，以确保它们包含在生成的软件映像中：

```kt
make clobber

```

# 使用 Fastboot 刷写（必须了解）

Fastboot 是一个用于与引导加载程序通信的工具和协议。它作为一个二进制文件存在，并在您使用 Android 源代码时包含在您的路径中。Fastboot 也是标准 SDK 的一部分（位于`platform-tools`下）。

## 准备工作

在您可以刷写任何软件之前，您需要将设备引导到快速启动模式。有两种方法可以做到这一点：

1.  使用按键组合：

+   首先，完全关闭手机

+   （Nexus One）Passion：按住轨迹球，然后按**电源**

+   （Nexus S）Crespo：按住**音量增加**，然后按住**电源**

+   （Galaxy Nexus）Maguro：同时按住**音量增加**和**音量减少**，然后按住**电源**

1.  使用 ADB 命令：以下命令将设备重启到恢复模式。这与按键组合具有相同的效果。

```kt
adb reboot-bootloader

```

解锁引导加载程序：只有在引导加载程序允许的情况下才能刷入软件。一旦设备处于快速启动模式，我们需要使用以下命令来解锁引导加载程序。

```kt
fastboot oem unlock

```

### 注意

请务必备份设备中您需要的所有文件/数据，因为此操作会擦除设备所有的内存。

并按照屏幕上的指示操作。

### 注意

在 Nexus One 上，此操作会使保修失效。

对于 Galaxy Nexus 和 Nexus S 设备，您可以通过以下命令锁定引导加载程序：

`fastboot oem lock`

## 如何操作…

1.  要刷写，您需要确保设备处于快速启动模式下连接。以下命令将在终端上显示设备的序列号：

```kt
fastboot devices

```

1.  然后，按顺序执行以下操作：

```kt
fastboot erase userdata
fastboot erase cache
fastboot flash boot boot.img
fastboot flash recovery recovery.img
fastboot flash system system.img
fastboot reboot

```

设备将启动到自定义操作系统。有关不同的快速启动命令和刷写过程的更多信息，请参考[`source.android.com/source/building-devices.html`](http://source.android.com/source/building-devices.html)。

### 注意

成功构建后，所需的系统镜像将可在`ANDROID_SRC/target/out/product/<NAME>/`中找到。

这里，`<NAME>`指的是目标。对于模拟器，它是`generic`，同样地，对于 Nexus S，它将是`crespo`。可用的镜像将是`system.img`、`boot.img`和`recovery.img`。

## 工作原理…

Fastboot 是一种与设备引导加载程序通信的协议。它的设计是使刷写操作与底层引导加载程序无关。解锁引导加载程序的过程仅适用于开发者设备。这是 Nexus S 开始的最新功能。重新锁定引导加载程序可以防止安装新固件。

谷歌开发者手机可以加载我们在先前教程中构建的自定义软件（谷歌开发者手机是专为平台开发人员设计的特殊设备，而不是普通消费者）。只有在解锁引导加载程序后才能向这些设备的闪存写入固件。消费者设备通常会锁定其引导加载程序，因此无法刷写。开发者手机（Nexus One、Nexus S 和 Galaxy Nexus）的工作流程在很大程度上是相同的。Fastboot 是一种协议和刷写工具，用于向设备写入新的软件镜像。

有关 fastboot 协议的更多细节，请参考`ANDROID_SRC/bootable/bootloader/legacy/fastboot_protocol.txt`。

# 为 Nexus S 构建自定义系统镜像（应该知道）

现在，我们准备构建一个自定义系统镜像。我们将重复使用先前编写的代码，并在实际设备上进行测试。因此，您需要在本教程中构建自定义系统服务器代码并将其刷入 Nexus S 设备。

## 准备工作

导航至`ANDROID_SRC`目录。

## 如何操作…

1.  导航至 Nexus S 的专有二进制页面，并下载 GRJ22 版本的所有文件。解压并提取它们。

1.  启动`full_crespo-userdebug`目标。

1.  执行完整的 make。成功后，导航至`ANDROID_SRC/target/out/product/crespo/`。

1.  按照上述描述刷写`system.img`、`boot.img`和`recovery.img`。

1.  重新启动手机。您可以使用：

```kt
fastboot reboot

```

## 工作原理…

代码镜像是为 ARM 架构交叉编译的，并且专有二进制文件已包含在其中。在构建过程中，适当的预构建内核镜像将被选中并包含在`boot.img`中。

在前面的示例中，我们创建了一个自定义服务，可以通过直接获取服务管理器的引用来调用。在这个示例中，我们将创建一个类库，将大部分代码抽象成一个清晰的接口。创建类库的优势在于它就像我们自定义服务的 SDK-API。这里我们要做的示例还将指导我们如何向 Android 类库添加代码。这些代码通常独立于系统服务，也可以用于其他目的。Android 类库的一个示例是`android.app.Activity`，这是一个常用的类，用于表示 Android 活动。这个类是 Android 类库的一部分。

# 创建类库（必须知道）

在这个示例中，我们将创建一个访问我们自定义系统服务器的类库。

## 准备工作

在`ANDROID_SRC/frameworks/base/core/java/android`创建一个目录。我们将其命名为`packt`。在其中，我们有以下代码文件。

## 如何做...

我们需要写一个围绕`PacktCryptoService`的包装器，它为我们提供 MD5 创建功能。我们写的包装器将是类库。我选择包装一个服务调用，因为这种模式被许多 Android 类库所遵循，也就是说，它们包装了服务功能。但是，你并不局限于使用这种类型的包装器：

1.  对于这个示例，我们需要编写以下代码，它包装了`PacktCryptoService`。将其保存在一个名为`PacktCryptoService.java`的文件中。

```kt
package android.packt; 
import android.os.packt.IPacktCrypto; 
import android.os.packt.PacktCryptoNative; 
import android.os.ServiceManager; 
import android.os.RemoteException; 
import android.util.Log; 

/* 
Custom Class Library PacktCrypto 
The class library is linked into the Android application and this code 
is executed as part of the applications process. It makes an IPC to the 
custom system server. This is just one example of the use of a class library. 
*/ 
public class PacktCrypto 
{ 
  IPacktCrypto pcRef; 
  private static final String SERVICE_NAME = "PacktCryptoService"; 
  private static final String TAG = "PacktCryptoClassLibrary"; 

  public PacktCrypto() 
  { 
    pcRef = PacktCryptoNative.asInterface(ServiceManager.getService(SERVICE_NAME));
  }

  public byte [] getMD5(String data) 
  { 
    byte [] hash = null; 
    try { 
      if(pcRef != null) 
        hash = pcRef.getMD5(data); 
    } catch(RemoteException re) 
    { 
      Log.e(TAG, Log.getStackTraceString(re));   
    } 

    return hash; 
  } 
}
```

1.  现在运行`make update-api`，因为我们已经修改了系统的公共 API。Android 在`ANDROID_SRC/frameworks/base/api`目录下的 XML 文件中维护着接口、权限和方法的列表。其中一个重要的文件是`current.xml`。这个文件代表了 Android 支持的公共 API 的接口、方法和权限。由于我们的自定义类库打算成为公共 API 的一部分，我们需要更新`current.xml`。因此，使用以下命令：

```kt
make update-api

```

1.  在这个示例中，我们将做一些稍微不同的事情来测试我们的代码。我们将构建我们自定义的 SDK。构建 SDK 会生成包含 Android 类库的 JAR 文件。例如，当使用 SDK 构建普通应用程序时，我们的项目层次结构中有一个名为`android.jar`的文件。这个文件是提供 Android 框架类的 SDK。我们需要用我们新添加的类库构建一个更新的`android.jar`文件。请注意，SDK 不需要包含我们添加到平台的服务，因为这些服务只存在于 Android 操作系统上。它们不需要用于基于 SDK 的开发。要构建一个新的 SDK，请发出以下命令。

```kt
make sdk

```

这个命令构建了 SDK。在构建结束时，输出应该类似于以下内容：

```kt
[previous output lines omitted for brevity]
DroidDoc took 0 sec. to write docs to out/target/common/docs/dexdeps
Package SDK Stubs: out/target/common/obj/PACKAGING/android_jar_intermediates/android.jar
Package SDK: out/host/linux-x86/sdk/android-sdk_eng.earlence_linux-x86.zip

```

1.  如果你打开`android-sdk_eng.earlence_linux-x86.zip`并检查它的内容，你会发现它基本上和普通的 Android SDK 一样。不同之处在于我们已经用我们的自定义代码进行了构建。使用这个 SDK，我们可以构建一个使用自定义类库的 APK。

1.  将 SDK ZIP 文件复制到某个外部位置（不在`ANDROID_SRC`内部）。现在通过发出`make`命令来运行正常的构建，如前面的示例所述。这将构建包含自定义系统服务的系统映像。这些系统映像用于运行模拟器，我们将在模拟器上测试我们的代码。

## 它是如何工作的...

我们的类库只是从服务管理器中获取自定义服务器的引用。然后调用`getMD5()`方法。这样做的好处是我们有一个更简单和更统一的 API 来访问我们的自定义服务器。另一个好处是它可以打包成一个 SDK，而不需要打包实际的系统服务本身。这是有道理的，因为 SDK 中从来没有打包系统服务，只有访问它们的 API 被打包。

### 注意

SDK 构建目标在每个源代码分发中都是可用的。

# 针对自定义 SDK 构建 Android 应用程序（应该知道）

我们将编写一个 Android 应用程序，利用我们在上一个配方中构建的自定义 SDK。

### 注意

在这个配方中，我假设您已经安装了 Eclipse，并且已经安装并配置了一个功能齐全的 Android SDK 与 ADT Eclipse 插件。这些步骤与 Android 开发者网站上找到的步骤相同（[`developer.android.com/index.html`](http://developer.android.com/index.html)）。

## 准备就绪

我们需要使我们的自定义 SDK 对现有的 Android SDK 可见。因此，解压生成的自定义 SDK。在内部，将会有`platforms/android-2.3.4`目录。我们对`android-2.3.4`目录感兴趣。将其重命名为`android-packt`并将其复制到`ANDROID_SDK/platforms/`中，其中`ANDROID_SDK`是您的 Android SDK 安装路径。

接下来，我们需要更新 API 级别号，以避免与相同 API 级别的现有安装发生冲突。在`android-packt`内，存在一个名为`build.prop`的文件。更改以下行：

```kt
ro.build.version.sdk=10
```

至

```kt
ro.build.version.sdk=-20
```

![准备就绪](img/9762OS_06_04.jpg)

这里，`-20`是一个任意的数字。这是为了防止冲突。

启动 Eclipse 并导航到 SDK Manager。您应该看到类似以下内容：

![准备就绪](img/9762OS_06_01.jpg)

请注意，插件已检测到具有 API 级别`-20`的新 SDK 安装，这是我们的自定义 SDK。

## 如何做…

现在按照通常的步骤创建一个骨架 Android 应用程序。在提示时确保选择正确的目标。如下截图所示，必须选择 API 级别`-20`。这是我们的自定义 SDK：

![如何做…](img/9762OS_06_02.jpg)

现在，我们可以像访问任何其他 Android API 一样访问我们的`PacktCrypto`类库。

1.  编写以下简单的测试活动。将以下代码保存在名为`PacktTestActivity.java`的文件中：

```kt
package com.packt.test; 
import android.app.Activity; 
import android.os.Bundle; 
import android.packt.PacktCrypto; 
import android.util.Log; 

public class PacktTestActivity extends Activity { 
    /** Called when the activity is first created. */ 
    @Override 
    public void onCreate(Bundle savedInstanceState) { 
        super.onCreate(savedInstanceState); 
        setContentView(R.layout.main); 

        PacktCrypto pc = new PacktCrypto(); 

        byte [] hash = pc.getMD5("packtest"); 

        StringBuffer sb = new StringBuffer(); 
    for (int i = 0; i < hash.length; i++) 
    { 
        sb.append(Integer.toString((hash[i] & 0xff) + 0x100, 16).substring(1)); 
    } 

    Log.i("PacktTest", "MD5 sum: " + sb.toString()); 
    } 
}
```

在上述代码中，我们只是创建了一个类型为`PacktCrypto`的对象，初始化它，然后调用`getMD5`方法，该方法调用我们的自定义系统服务器。

## 它是如何工作的…

`PacktCrypto`类库方法在新的 SDK `framework.jar`文件中可用。这有助于编译应用程序。在部署到模拟器时，类库会获取对正在系统上运行的`PacktCrypto`服务的引用，并将其功能提供给 Android 应用程序。

# 测试类库（应该知道）

这是一个非常简单的配方，我们用它来验证类库是否按预期工作。

## 准备就绪

如果您还没有使用我们的自定义系统映像启动模拟器（前面已经解释过），现在开始启动。

## 如何做…

构建 APK。通过命令行安装它（`adb install`）。由于我们使用的 SDK 是标准/未修改的，因此 SDK 不会检测到正在运行的模拟器，并且因此不会理解虚拟 API 级别`-20`。因此，从 Eclipse 启动将不起作用。但是，安装过程与通过命令行安装普通应用程序的安装过程没有区别。根据 Android 开发者文档的规定，可以通过指定带有 APK 文件名的`adb install`命令来安装 APK。输出如下：

![如何做…](img/9762OS_06_03.jpg)

## 它是如何工作的…

模拟器正在运行包含`PacktCrypto`服务的固件映像。这是在系统启动时启动的。类库只是通过服务管理器获取服务对象的副本，并调用“getMD5（）”方法。这在类库中被抽象化，允许应用程序的开发独立于在实际系统上运行的代码。

在前面的示例中，我们的修改已经与平台代码紧密集成。在某些情况下，可能不需要这种级别的集成，但需要向系统添加新功能。在这种情况下，开发人员可以将代码添加到框架中，形成一个平台库。在这个示例中，我们将学习如何创建一个平台库，以及如何编写使用它的应用程序。

# 编写平台库源代码（必须知道）

我们编写简单的方法来使用**数据加密标准**（**DES**）来加密和解密一个字符串，使用 8 字节的密码。我们的加密库名为`PacktPlatformLibrary`。

## 准备工作

由于这段代码是外部的，不需要与系统紧密集成，我们将其放在一个名为`/vendor`的单独目录下`ANDROID_SRC`。通常，在这个目录中，会添加特定于供应商的文件。创建`ANDROID_SRC/vendor/PacktVendor`。在其中，包括以下一行的`Android.mk`文件，以便在构建期间调用后续的 make 文件。

### 注意

从 Android 4.0 及更高版本开始，平台库代码可以放在`ANDROID_SRC/device/`下。然而，读者应该注意，这并不是平台库工作方式的根本变化，这里的概念很容易扩展到 Android 源代码的后续版本。

## 如何做…

1.  我们首先编写一个一行的项目 make 文件。将此文件保存为`Android.mk`，这是顶级 make 文件：

```kt
include $(call all-subdir-makefiles)
```

1.  在 PacktVendor 下，创建一个名为 PacktPlatformLibrary 的目录。在其中，我们将创建平台库并编写其代码。

1.  在`PacktPlatformLibrary/`下创建一个名为`java/packt/platformlibrary`的目录。这将保存库的源代码。

1.  将以下代码保存为`PacktPlatformLibrary.java`：

```kt
package packt.platformlibrary;

import java.io.UnsupportedEncodingException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;

import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;

import android.util.Log;

public class PacktPlatformLibrary
{
  private static final String TAG = "packt.PlatformLibrary";

         /* key has to be 8 bytes */
    public static byte [] encryptDES(String key, String data)
    {
      byte [] encr = null;
      DESKeySpec keySpec;
    try {
      keySpec = new DESKeySpec(key.getBytes("UTF8"));
      SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey skey = keyFactory.generateSecret(keySpec);
        Cipher cipher = Cipher.getInstance("DES");
        cipher.init(Cipher.ENCRYPT_MODE, skey);
        encr = cipher.doFinal(data.getBytes("UTF8"));
    } catch (InvalidKeyException e) {
      Log.e(TAG, Log.getStackTraceString(e));
    } catch (UnsupportedEncodingException e) {
      Log.e(TAG, Log.getStackTraceString(e));
    } catch (NoSuchAlgorithmException e) {

      Log.e(TAG, Log.getStackTraceString(e));

    } catch (InvalidKeySpecException e) {

      Log.e(TAG, Log.getStackTraceString(e));

    } catch (NoSuchPaddingException e) {

      Log.e(TAG, Log.getStackTraceString(e));

    } catch (IllegalBlockSizeException e) {

      Log.e(TAG, Log.getStackTraceString(e));

    } catch (BadPaddingException e) {

      Log.e(TAG, Log.getStackTraceString(e));

    }
    return encr;

    }
```

1.  前面的方法只是对字符串进行了 DES 加密。您可以用任何您喜欢的函数替换这个函数。示例的重点是演示一个平台库：

```kt
 public static String decryptDES(String key, byte [] data)
    {
      DESKeySpec keySpec;
      byte [] unencr = null;
    try {
      keySpec = new DESKeySpec(key.getBytes("UTF8"));
      SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey skey = keyFactory.generateSecret(keySpec);
        Cipher cipher = Cipher.getInstance("DES");// cipher is not thread safe
        cipher.init(Cipher.DECRYPT_MODE, skey);
        unencr = cipher.doFinal(data);

} catch (InvalidKeyException e) {
      Log.e(TAG, Log.getStackTraceString(e));

} catch (UnsupportedEncodingException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    } catch (NoSuchAlgorithmException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    } catch (InvalidKeySpecException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    } catch (NoSuchPaddingException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    } catch (IllegalBlockSizeException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    } catch (BadPaddingException e) {
      Log.e(TAG, Log.getStackTraceString(e));

    }
    return new String(unencr);
    }

    public static void printHex(byte [] data)

    {
      StringBuffer sb = new StringBuffer(); 

      for (int i = 0; i < data.length; i++) 
      { 

          sb.append(Integer.toString((data[i] & 0xff) + 0x100, 16).substring(1)); 

      } 
      Log.i("PacktDESTest", "DES bytes: " + sb.toString()); 

    }

}
```

1.  同样，`decryptDES`执行`encryptDES`的相反功能。您可以选择用自己喜欢的函数替换它。如果选择这样做，请记住后续文件将根据新函数进行一些调整。但是，在任何构建文件中都不需要进行更改：

1.  Android 系统要求为每个新的平台库创建一个 XML 文件。在`ANDROID_SRC/vendor/PacktVendor/PacktPlatformLibrary/`创建一个 XML 文件。将这个 XML 文件保存为`PacktPlatformLibrary.xml`：

```kt
<permissions>
  <library name="PacktPlatformLibrary" file="/system/framework/PacktPlatformLibrary.jar"/>

</permissions>
```

1.  最后，我们需要一个 make 文件来将所有组件整合在一起。在与前面文件相同的目录中创建一个`Android.mk`文件：

```kt
This code is saved as Android.mk

#
# PacktPlatformLibrary

LOCAL_PATH := $(call my-dir)

# the library
# ============================================================
include $(CLEAR_VARS)
LOCAL_SRC_FILES := $(call all-subdir-java-files)
LOCAL_MODULE_TAGS := optional
# This is the target being built.
LOCAL_MODULE:= PacktPlatformLibrary
include $(BUILD_JAVA_LIBRARY)
# the documentation
# ============================================================
include $(CLEAR_VARS)
LOCAL_SRC_FILES := $(call all-subdir-java-files) $(call all-subdir-html-files)
LOCAL_MODULE:= PacktPlatformLibrary

LOCAL_DROIDDOC_OPTIONS := PacktPlatformLibrary

LOCAL_MODULE_CLASS := JAVA_LIBRARIES
LOCAL_DROIDDOC_USE_STANDARD_DOCLET := true
include $(BUILD_DROIDDOC)
########################

include $(CLEAR_VARS)
LOCAL_MODULE := PacktPlatformLibrary.xml
LOCAL_MODULE_TAGS := optional
LOCAL_MODULE_CLASS := ETC
# This will install the file in /system/etc/permissions
#
LOCAL_MODULE_PATH := $(TARGET_OUT_ETC)/permissions
LOCAL_SRC_FILES := $(LOCAL_MODULE)
include $(BUILD_PREBUILT)

```

前面的文件有三个部分。第一部分编译库的 java 代码并创建一个 JAR 文件。这个 JAR 的名称在`LOCAL_MODULE`标签中指定。接下来，如果项目目录包含文档，那么会进行构建。最后，我们需要确保 XML 文件被添加到系统映像中的`/system/etc/permissions/`中。`LOCAL_MODULE_CLASS`用于指定这一点。现在，我们需要构建组件。在终端中，进入`ANDROID_SRC`，发出以下命令：

```kt
make PacktPlatformLibrary

```

以下命令将编译 java 代码，生成一个签名的 JAR，并将其放在`system/framework/`中：

```kt
make PacktPlatformLibrary.xml

```

这将把 XML 文件放在`/system/etc/permissions/`中。

## 工作原理…

平台库有两个主要组件。一个是库本身，它是用 Java 或 C 编写的，并打包成一个 JAR（如果使用本机代码，则可以选择共享对象）。第二个组件是一个 XML 文件，它将 JAR 声明为平台库。这个组件很重要，因为它帮助系统在需要加载到应用程序中时识别平台库的位置。

在为 XML 文件指定的 make 文件中指定的模块类`ETC`用于将文件放入固件映像的`/system/etc`目录中。

# 创建平台客户端（应该知道）

要测试创建的平台库，我们需要创建一个使用该库的 Android 应用程序。为此，我们将创建一个新的“系统 APK”。系统 APK 是一个 Android 应用程序，位于设备的只读`/system`分区中，类似于设置和联系人等应用程序。系统应用位于`ANDROID_SRC/packages/apps`中。

## 准备工作

在该位置创建一个名为`PacktLibraryClient`的目录。在其中，我们编写一个小型的 Android 应用程序，访问平台库并调用一个方法。

在`ANDROID_SRC/packages/apps/PacktLibraryClient/src/com/packtclient`创建以下文件。

## 如何做...

1.  我们首先编写将访问我们平台库的客户端文件。以下代码保存为`Client.java`：

```kt
package com.packtclient;
import packt.platformlibrary.PacktPlatformLibrary;
import android.app.Activity;
import android.os.Bundle;
/**
 * utilize the packt DES encryption platform library
 */

public class Client extends Activity {

    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
        byte [] encr = PacktPlatformLibrary.encryptDES("password", "Packt");

        PacktPlatformLibrary.printHex(encr);        

    }
}
```

1.  与任何其他 Android 应用程序一样，我们需要一个清单文件，该文件位于`ANDROID_SRC/packages/apps/PacktLibraryClient/`。以下代码保存在名为`AndroidManifest.xml`的文件中：

```kt
<?xml version="1.0" encoding="utf-8"?>

<!-- This is an example of writing a client application for a custom

     platform library. -->

<manifest 

    package="com.packtclient">
    <application android:label="Packt Library Client">
        <!-- This tells the system about the custom library used by the

             application, so that it can be properly loaded and linked

             to the app when the app is initialized. -->

        <uses-library android:name="PacktPlatformLibrary" />
        <activity android:name="Client">

            <intent-filter>

                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>

            </intent-filter>

        </activity>

    </application>

</manifest>
```

这里的新添加是`<uses-library>`标签。请注意，我们必须指定我们自定义平台库的名称，以指示运行时加载我们的客户端应用程序。

1.  最后，我们需要在与前面文件相同的目录级别上创建一个 make 文件。将此 make 文件保存为`Android.mk`：

```kt
# This makefile is an example of writing an application that will link against
# a custom shared library included with an Android system.
LOCAL_PATH:= $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := optional
# This is the target being built.
LOCAL_PACKAGE_NAME := PacktLibraryClient
# Only compile source java files in this apk.
LOCAL_SRC_FILES := $(call all-java-files-under, src)
# Link against the current Android SDK.
#LOCAL_SDK_VERSION := current
# Also link against our own custom library.

LOCAL_JAVA_LIBRARIES := PacktPlatformLibrary
LOCAL_PROGUARD_ENABLED := disabled
include $(BUILD_PACKAGE)
```

### 注意

注意使用`LOCAL_JAVA_LIBRARIES`标签来指定我们编译时使用的平台库。

1.  现在我们准备构建我们的客户端 APK。在终端中，执行以下命令（假设终端环境已正确设置；有关说明，请参考本书的第一个教程）：

```kt
make PacktLibraryClient

```

1.  最后，我们需要为模拟器构建系统镜像来测试我们的代码：

```kt
make

```

1.  确保以下文件包含在系统镜像中。这是通过检查`installed-files.txt`的内容来完成的，该文件位于模拟器构建的情况下的`ANDROID_SRC/out/target/product/generic/`。在这里，我从我的副本中提取了相关内容：

```kt
        1978  /system/framework/PacktPlatformLibrary.jar
         119  /system/etc/permissions/PacktPlatformLibrary.xml
        3563  /system/app/PlatformLibraryClient.apk
```

1.  因此，所有所需的部件都已集成到系统镜像中。启动模拟器，然后点击`PlatformLibraryClient`应用程序。Logcat 应该输出类似以下内容：

```kt
I/PacktDESTest(  425): DES bytes: fd068bdc755be524

```

## 工作原理...

平台客户端只是另一个 Android 应用程序。这里唯一的区别是它与系统镜像捆绑在一起，并安装在`/system/app`——只读分区。

应用程序的 make 文件中最重要的一行是`LOCAL_JAVA_LIBRARIES`标签。这指定了我们将使用平台客户端的功能。

## 还有更多...

为了帮助澄清本教程中提出的概念，通常有助于可视化项目结构。在下文中，我们以图形方式描述了 Android 源代码中平台库的外观。

### 平台库项目组织

大多数平台库的结构如下图所示。如前所述，顶级目录可以从`ANDROID_SRC/vendor`更改为`ANDROID_SRC/device/`，如果您从 Gingerbread 开发转移到 Ice Cream Sandwich 开发。

![平台库项目组织](img/9762OS_07_01.jpg)

### 系统应用项目组织

系统应用在 Android 源代码中具有以下结构。此图描述了我们刚刚构建的`PacktLibraryClient`系统应用的组织方式，但组织方式与其他系统应用类似。这里要注意的是系统应用位于`ANDROID_SRC/packages/apps`下，并且在大多数情况下，目录结构与普通 Android 应用程序相同。

![系统应用项目组织](img/9762OS_07_02.jpg)

我们先前的系统服务只包含 Java 代码；然而，这样的服务也可能包含对本地代码的调用。开发人员可能出于各种原因选择使用本地代码。最常见的原因是速度优于解释代码和重用现有库。在下一个示例中，我们将向您展示如何在我们的`PacktCrypto`系统服务中使用本地函数。

此外，在跳转到下一个示例之前，我假设您对 JNI 技术感到满意。如果不是，我建议阅读优秀的书籍：*Java Native Interface: Programmer's Guide and Specification* by Sheng Liang. ([`www.amazon.com/Java-Native-Interface-Programmers-Specification/dp/0201325772`](http://www.amazon.com/Java-Native-Interface-Programmers-Specification/dp/0201325772)).

# 编写本地方法并在 JNI Onload 事件中注册（应该知道）

系统服务器的本地方法实现在`ANDROID_SRC/frameworks/base/services/jni`中。该目录包含一个`Android.mk`文件。

## 如何做...

我们首先通过 C 代码实现本地方法。在本例中，我选择实现流行的快速排序算法。请注意，文件必须按照命名约定命名。这只是包名按相反顺序排列，但使用下划线而不是点作为分隔符。

1.  打开您喜欢的文本/代码编辑器。

1.  在名为`com_android_packt_PacktCrypto.cpp`的文件中编写以下代码；它包含本地方法：

```kt
/* 
Native code for System Servers 
*/ 
#define LOG_TAG "PacktCrypto" 

#include "jni.h" 
#include "JNIHelp.h" 
#include "android_runtime/AndroidRuntime.h" 

#include <utils/misc.h> 
#include <utils/Log.h> 

namespace android 
{ 

//taken from http://www.algorithmist.com/index.php/Quicksort.c 
void qsort(int *data, int N) 
{ 
  int i, j; 
  int v, t; 

   if(N <= 1) 
      return; 

    // Partition elements 
    v = data[0]; 
    i = 0; 
    j = N; 
    for(;;) 
    { 
      while(data[++i] < v && i < N) { } 
      while(data[--j] > v) { } 
      if( i >= j ) 
          break; 
      t = data[i]; 
      data[i] = data[j]; 
      data[j] = t; 
    } 
    t = data[i-1]; 
    data[i-1] = data[0]; 
    data[0] = t; 
    qsort(data, i-1); 
    qsort(data+i, N-i); 
} 

jintArray quickSort(JNIEnv* env, jobject *clazz, jintArray data) 
{ 
  jsize len = env->GetArrayLength(data); 
  jint *arr = env->GetIntArrayElements(data, 0); 
  jintArray result = env->NewIntArray(len); 

  qsort(arr, len); 

  env->SetIntArrayRegion(result, 0, len, arr); 
  env->ReleaseIntArrayElements(data, arr, 0); 

  return result; 
} 

//specify the method table with pointers to our functions
static JNINativeMethod method_table[] = { 
  { "quickSort", "([I)[I", (void *) quickSort } 
}; 

//register the method table
int register_android_packt_PacktCrypto(JNIEnv *env) 
{ 
  return jniRegisterNativeMethods(env, "com/android/packt/PacktCrypto", method_table, NELEM(method_table)); 
} 

};
```

1.  接下来，修改`onload.cpp`文件。有两个位置需要进行添加。第一个是在`namespace android {`块下。将显示一系列注册函数，如下面的代码片段所示：

```kt
namespace android { 
int register_android_server_AlarmManagerService(JNIEnv* env); 
int register_android_server_BatteryService(JNIEnv* env); 
int register_android_server_InputManager(JNIEnv* env); 
int register_android_server_LightsService(JNIEnv* env); 
int register_android_server_PowerManagerService(JNIEnv* env); 
int register_android_server_UsbService(JNIEnv* env); 
int register_android_server_VibratorService(JNIEnv* env); 
int register_android_server_SystemServer(JNIEnv* env); 
int register_android_server_location_GpsLocationProvider(JNIEnv* env);

};
```

1.  我们需要在自定义注册函数中添加一行。在`namespace`块的末尾添加以下行：

```kt
//PacktCrypto 
int register_android_packt_PacktCrypto(JNIEnv *env);
```

这是我们在`com_android_packt_PacktCrypto.cpp`中编写的注册函数。

1.  第二个添加是在同一文件`onload.cpp`中的`JNI_OnLoad`内。在所有注册函数调用的末尾添加以下行：

```kt
//PacktCrypto 
register_android_packt_PacktCrypto(env);
```

1.  最后，我们需要通知构建系统已添加了一些新代码。通过在`LOCAL_SRC_FILES`标签的末尾添加以下行来修改`Android.mk`文件（与我们一直在修改的所有其他文件位于同一目录中）：

```kt
com_android_packt_PacktCrypto.cpp
```

因此，新标签将如下所示：

```kt
LOCAL_SRC_FILES:= \ 
    com_android_server_AlarmManagerService.cpp \ 
    com_android_server_BatteryService.cpp \ 
    com_android_server_InputManager.cpp \ 
    com_android_server_LightsService.cpp \ 
    com_android_server_PowerManagerService.cpp \ 
    com_android_server_SystemServer.cpp \ 
    com_android_server_UsbService.cpp \ 
    com_android_server_VibratorService.cpp \ 
    com_android_server_location_GpsLocationProvider.cpp \ 
    onload.cpp \ 
    com_android_packt_PacktCrypto.cpp
```

## 工作原理...

我们编写的本地方法必须被加载和识别。Android 框架提供了一个名为`jniRegisterNativeMethods`的函数，它以方法表和将调用本地方法的 Java 类的完全限定类名作为输入。在这种情况下，它是我们的`PacktCrypto`系统服务。方法表使用 JNI 方法描述符表示输入/输出参数的类型。它还包括指向本地方法实现的函数指针。`JNI_OnLoad`事件在本地库加载到虚拟机中时调用。在这一点上，我们像其他系统服务器一样注册我们的本地方法。`NELEM`是一个框架提供的宏，用于计算数组的长度。它定义如下，并位于`frameworks/base/include/utils/misc.h`文件中：

```kt
# define NELEM(x) ((int) (sizeof(x) / sizeof((x)[0])))
```

# 编写 Java 驱动程序（应该知道）

为了测试我们的本地库，我们需要编写一些 Java 代码来调用本地函数。我将重用之前示例中的代码，其中我们创建并测试了`PacktCrypto`系统服务。

我们需要从基于 Java 的`PacktCrypto`服务中调用本地代码。因此，在本示例中，我们将修改`PacktCrypto`系统服务，以便它可以调用本地方法。

## 准备工作

打开位于`services/java/com/android/packt`目录中的`PacktCrypto.java`文件，并通过在文件末尾添加以下行来进行更改。

## 如何做...

1.  为了测试`sort`方法，我只需在现有的`getMD5`方法中调用`sort()`方法。我这样做是为了让您能够快速测试本地代码是否按预期工作，而不必进行太多更改。如果您还记得，我们已经在`SystemServer.java`中为`getMD5`编写了一个测试工具。第一步是通过在文件末尾添加以下行来修改`PacktCrypto.java`文件。

1.  这是`PacktCrypto.java`文件的一个片段，用于测试我们的本地函数：

```kt
void sort(int [] data) 
  { 
    int [] sorted = quickSort(data); 
    for(int i = 0; i < data.length; i++) 
      Log.i(TAG, "s: " + sorted[i]); 
  } 

  native int [] quickSort(int [] data);
```

1.  通过向`getMD5`方法末尾添加以下行，我们将能够快速测试我们的本地代码。`getMD5()`的新版本如下：

```kt
public byte [] getMD5(String data) 
  { 
    byte [] dataBytes = null; 
    MessageDigest md5 = null; 

    try { 

      dataBytes = data.getBytes("UTF-8"); 
      md5 = MessageDigest.getInstance("MD5"); 
      Log.i(TAG, "MD5 digestion invoked"); 

    } catch(java.io.UnsupportedEncodingException uee) 
    { 
      Log.e(TAG, "Unsupported Encoding UTF8"); 
    } 
    catch(java.security.NoSuchAlgorithmException nsae) 
    { 
      Log.e(TAG, "No Algorithm MD5"); 
    } 
    //quick hack to test our native method 
    int toSort [] = { 5, -1, 0, 30, 4, 0, 1, 3, 15, 3 }; 
    sort(toSort); 

    if(md5 != null) 
      return md5.digest(dataBytes); 
    else 
      return null; 
  }
```

注意调用`sort()`。

1.  现在，我们可以构建我们的代码。像往常一样，在终端中，输入`make`命令。构建完成后，运行模拟器并观察日志：

```kt
I/SystemServer(   67): PacktCrypto service 
I/PacktCrypto(   67): MD5 digestion invoked 
I/PacktCrypto(   67): s: -1 
I/PacktCrypto(   67): s: 0 
I/PacktCrypto(   67): s: 0 
I/PacktCrypto(   67): s: 1 
I/PacktCrypto(   67): s: 3 
I/PacktCrypto(   67): s: 3 
I/PacktCrypto(   67): s: 4 
I/PacktCrypto(   67): s: 5 
I/PacktCrypto(   67): s: 15 
I/PacktCrypto(   67): s: 30 
I/PacktTest(   67): MD5 sum: 422164113c2fc595dd0ab44a18925ac5
```

## 它是如何工作的...

Java 代码包含本地方法定义。框架加载本地代码，并在`onload`事件期间，加载和注册我们的本地实现。当调用`getMD5()`时，我们的本地代码被执行，并且输出可见于日志记录器。

在下一个配方中，我们将学习一些最重要的核心 Android 服务是如何组织的，以及如何对这些服务进行更改。我们还将学习如何通过添加到框架的自定义权限来保护我们的更改。

Android 上的 Internet 基础设施需要讨论，因为其设计与其他服务（如位置管理器服务）相比是非常规的。

# 分析 ActivityManagerService 类（应该知道）

`ActivityManagerService`类可能是 Android 系统中存在的所有服务中最重要的一个。在这个配方中，我们将介绍其主要功能，并突出该服务的特殊之处。

## 准备就绪

`ActivityManagerService.java`是源文件，位于`ANDROID_SRC/frameworks/base/services/java/com/android/server/am`。

## 如何做...

1.  在您选择的代码编辑器中打开此文件。我们将浏览`ActivityManagerService`文件并了解其各个组件。

1.  Activity Manager 扩展了一个名为`ActivityManagerNative`的类，该类实现了远程调用的绑定器协议。这段代码是手动编写的，就像我们在使用 IPC 与系统服务代码的配方中所写的那样。这是因为在 Google 员工编写 Activity Manager 代码时，AIDL 编译器并不存在。

1.  文件开头有各种与活动相关的不同方面的控制变量。其中一个变量是：

```kt
static final int MAX_ACTIVITIES = 20;
```

这定义了堆栈中可以存在的最大活动数。这意味着可以同时运行的最大活动数。

1.  另一个有趣的变量是系统等待启动进程的持续时间。

```kt
static final int PROC_START_TIMEOUT = 10*1000;
```

1.  SDK 开发人员将知道，使用广播接收器时，`onReceive()`方法应该快速完成。超时由以下规定：

```kt
static final int BROADCAST_TIMEOUT = 10*1000;
```

大约`10`秒钟。

1.  在文件中向下滚动，有一个处理程序，它执行大部分不需要立即返回的工作。您可能知道，Android 有一个处理程序的概念，基本上是一种在单独的线程中执行代码并稍后返回结果的机制。处理程序定义如下：

```kt
final Handler mHandler = new Handler() {
```

1.  执行的一些示例任务包括 ANR 对话框，由`SHOW_NOT_RESPONDING_MSG`指示，以及向注册接收器发送广播。

1.  其他功能是作为同步返回的方法实现的。异步返回意味着请求的结果将在稍后返回给调用者，而不是立即返回，就像同步调用一样。

1.  源代码中有一个专门的部分用于权限操作和验证。权限检查的唯一公共入口点是该方法：

```kt
public int checkPermission(String permission, int pid, int uid)
```

1.  系统中执行的所有权限检查都通过此方法。因此，它作为一个可靠的检查点，可以了解进程在保护数据和操作方面的操作。

1.  在此之后，活动管理器有一组通过操作活动堆栈来管理任务的方法。堆栈是在与活动管理器服务相同目录中的`ActivityStack.java`文件中定义的数据结构。

1.  当系统完全启动并准备好开始启动用户进程时，将执行以下方法：

```kt
public void systemReady(final Runnable goingCallback)
```

有单独的部分管理服务、内容提供程序和广播。在每个部分的开头，注释指示接下来的方法类型。`ActivityManagerService`类在同一文件中实现了各种 API 方法，如`registerReceiver`、`startActivity`和`startService`。

## 它是如何工作的...

活动管理器服务在启动过程中早期启动。在结束时，当所有初始化任务完成时，将执行`systemReady`方法并触发系统启动完成广播。此方法由`SystemServer`服务调用。它用于通知`ActivityManagerService`系统已经到达可以开始运行第三方代码的点。虽然`ActivityManagerService`不会立即执行此操作，但它在接收到`systemReady`调用时准备就绪。

## 还有更多...

每当对`ActivityManagerService`类进行更改时，请确保不要引入使服务容易受到攻击或泄露敏感数据给恶意应用程序的代码路径。始终遵循安全编码原则，并通过权限检查保护您的方法。如果不希望第三方开发人员访问您的功能，请为该方法分配签名权限。敏锐的读者会注意到`ActivityManagerService`类与任何其他系统服务一样，并在`SystemServer`服务的上下文中执行。因此，现在您意识到您的自定义系统服务与如此重要的服务一起执行。您的系统服务中的任何错误都有可能导致整个框架崩溃。因此，在编写此类代码时要小心！

# 向 Activity Manager 服务添加自定义方法（应该知道）

在此示例中，我们将指导您完成引入自定义权限所需的步骤，并学习如何利用它来保护我们向 Activity Manager 服务添加的方法。

## 准备就绪

我们将编辑以下文件：

| 文件名 | 位置 |
| --- | --- |
| `ActivityManagerService.java` | `ANDROID_SRC/frameworks/base/services/java/com/android/server/am` |
| `ActivityManagerNative.java` | `ANDROID_SRC/frameworks/base/core/java/android/app` |
| `ActivityManager.java` | `ANDROID_SRC/frameworks/base/core/java/android/app` |
| `IActivityManager.java` | `ANDROID_SRC/frameworks/base/core/java/android/app` |
| `AndroidManifest.xml` | `ANDROID_SRC/frameworks/base/core/res` |
| `strings.xml` | `ANDROID_SRC/frameworks/base/core/res/res/values` |

## 如何做...

1.  我们首先要做的是向`IActivityManager.java`添加一个新的事务和方法原型。

1.  以下代码附加到`IActivityManager.java`：

```kt
//Packt - Add this towards the end of the file
int PACKT_TRANSACTION = IBinder.FIRST_CALL_TRANSACTION + 118;
public int packtSensitiveMethod(int data) throws RemoteException;
```

数字 118 只是我在源代码版本中拥有的最后一个事务号加 1。您可能看到的数字可能不同。使用您版本中看到的任何数字并加 1。

1.  上述行基本上为我们的方法设置了一个事务号。由于底层的 binder 驱动程序使用这些数字来匹配在进行 IPC 调用时需要执行哪些方法，我们将我们方法的事务号设置为上一个已知事务号加一。根据您正在使用的源代码版本，上面写的数字可能需要增加。逻辑很简单，就是在您看到的上一个事务号上加 1。IPC 方法需要抛出`RemoteException`，因为这是 Binder IPC 协议规定的。然后我们为这个方法创建公共接口。为此，请在`ActivityManager.java`文件的末尾添加以下行：

```kt
 /* 
    Packt sensitive method demo 
    */ 
    public int packtSensitiveMethod(int data) 
    { 
      try { 
        return ActivityManagerNative.getDefault().packtSensitiveMethod(data); 
      } catch (RemoteException e) 
      { 
      } 
      return -1; 
    }
```

1.  这是一个非常简单的方法，没有任何实际用途。这个配方的目的是帮助您理解向 Activity Manager 服务添加新方法的机制。

1.  接下来，我们将为这个方法实现编组代码。编组的概念与其他进程间框架没有什么不同。基本目的是序列化复杂结构。

1.  在`ActivityManagerNative.java`中，我们进行了两个更改。第一个是在文件中的`ActivityManagerProxy`类内部。代码组织得很好，`ActivityManagerProxy`类位于文件的末尾。因此，我们在`ActivityManagerNative.java`文件的末尾，在`ActivityManagerProxy`类的范围内，编写我们的`packtSensitiveMethod`的代理实现。

以下代码添加到`ActivityManagerNative.java`文件的末尾。这是`packtSensitiveMethod`代理实现：

```kt
    //Packt 
    public int packtSensitiveMethod(int data) throws RemoteException 
    { 
      Parcel out = Parcel.obtain(); 
      Parcel reply = Parcel.obtain(); 

      out.writeInterfaceToken(IActivityManager.descriptor); 
      out.writeInt(data); 

      mRemote.transact(PACKT_TRANSACTION, out, reply, 0); 

      int result = reply.readInt(); 

      out.recycle(); 
      reply.recycle(); 

      return result; 
    }
```

1.  第二个更改是在文件的`onTransact`方法中进行的。在最后的`case`语句中，我们添加我们的`case`代码：

```kt
  //Packt 
        case PACKT_TRANSACTION: 
          data.enforceInterface(IActivityManager.descriptor); 
          int param = data.readInt(); 
          int result = packtSensitiveMethod(param); 
          reply.writeInt(result); 
          reply.writeNoException(); 
          return true;
```

1.  最后，我们必须实现这个方法。以下实现写在`ActivityManagerService.java`文件中：

```kt
//===========================================================
// PACKT 
//=========================================================== 

    public int packtSensitiveMethod(int data) throws RemoteException 
    { 
      if(checkCallingPermission("packt.PACKT_PERMISSION") 
                       != PackageManager.PERMISSION_GRANTED) 
      { 
          throw new SecurityException("Requires permission packt.PACKT_PERMISSION"); 
        } 
        else 
        { 
        Log.i("PACKTinAMS", "sensitive method called with parameter: " + data); 

        return data * 2; 
      }
    }
```

### 注意

我们已经编写了检查权限代码来保护这个方法。在下一个配方中，我将指导您介绍如何将自定义权限引入框架。

## 工作原理...

在 Android 中，`ActivityManagerService`类在系统服务器进程的上下文中执行。因此，对它的调用将是远程调用。因此，我们必须利用 Binder IPC 机制。`IActivityManager.java`文件包含了 Activity Manager 服务公开的一组远程调用。因此，我们添加一个新的接口方法`packtSensitiveMethod()`和一个事务常量`PACKT_TRANSACTION`，它将对应于这个方法。

`ActivityManager.java`代表用户应用程序用来访问 Activity Manager 服务功能的类库。这类似于我们在早期的创建自定义类库配方中添加的自定义类库。正如我之前所说，我们的实现遵循 Android 代码中使用的设计准则。因此，您将在 Android 源代码中看到几个现有的类似之处。这只是众多存在的例子之一。因此，我们向该文件添加一个 shim 方法，通过远程过程调用调用真实方法。

如前所述，Activity Manager 服务是在 AIDL 编译器存在之前编写的，因此所有远程调用的代理和存根都是手动实现的。因此，我们向`ActivityManagerNative.java`添加了编组代码。

您可能会想为什么我们仍然手动添加编组代码。答案是，即使 AIDL 编译器现在可用，构建过程也从未更新为`ActivityManagerService`类使用 AIDL 编译器。

# 向框架添加自定义权限（必须知道）

在这个配方中，我们将向框架添加自定义权限，以便只有经过授权的进程才能访问和执行我们向`ActivityManagerService`添加的新方法。

## 准备工作

从上表中指定的位置打开`AndroidManifest.xml`文件。

## 如何做...

1.  现在我们将编辑清单以插入新的权限对象。只需将这些代码行添加到`AndroidManifest.xml`中：

```kt
<!-- ==================================== --> 
     <!-- Packt system permissions             --> 
     <!-- ==================================== --> 
     <eat-comment /> 

     <permission-group android:name="android.permission-group.PACKT" 
         android:label="@string/permgrouplab_packt" 
         android:description="@string/permgroupdesc_packt" /> 

     <!-- PACKT Test permission --> 
     <permission android:name="packt.PACKT_PERMISSION" 
         android:permissionGroup="android.permission-group.PACKT" 
         android:protectionLevel="signature" 
         android:description="@string/packt_permDesc" 
         android:label="@string/packt_label" />
```

### 注意

请注意，您必须使用字符串资源来指定权限的各种文本属性。这些值在`strings.xml`中定义。

1.  因此，打开文件（在前面的表中给出的位置）并将以下代码附加到`strings.xml`：

```kt
<!-- Packt Permission Strings --> 
    <string name="permgrouplab_packt">Packt Permission Group Label</string> 
    <string name="permgroupdesc_packt">Packt Permission Group Description</string> 
    <string name="packt_permDesc">Packt Permission Description</string> 
    <string name="packt_label">Packt Permission Label</string>
```

1.  在这个阶段，我们应该确保一切都编译正常。首先发出以下命令，因为我们通过向`ActivityManager.java`添加方法已更改了公共 API：

```kt
make update-api

```

1.  接下来，发出正常的`make`命令。

## 它是如何工作的...

即使 Android 框架也有一个清单文件。该文件包含系统中存在的所有权限的权限定义。`permission`标签要求您在其他参数中指定权限描述。系统使用这些字符串进行用户显示。例如，权限描述参数在要求用户授予权限的应用程序安装时显示给用户。

# 使用自定义方法（应该知道）

我们需要测试我们新添加的方法。因此，我们使用了在*编写平台库源代码*中创建的`PacktLibraryClient`应用程序。

## 准备就绪

从`PacktLibraryClient`应用程序中打开`Client.java`，该文件位于`ANDROID_SRC/packages/apps/PacktLibraryClient/src/com/packtclient/`。

## 如何做...

1.  以下代码代表使用新添加的方法的客户端代码，并保存在名为`Client.java`的文件中：

```kt
package com.packtclient; 

import packt.platformlibrary.PacktPlatformLibrary; 
import android.app.Activity; 
import android.app.ActivityManager; 
import android.os.Bundle; 
import android.util.Log; 

public class Client extends Activity { 
    @Override 
    public void onCreate(Bundle savedInstanceState) { 
        super.onCreate(savedInstanceState); 

        //byte [] encr = PacktPlatformLibrary.encryptDES("password", "Packt"); 
        //PacktPlatformLibrary.printHex(encr); 

        invokeNewPacktAMSMethod();       
    } 

    void invokeNewPacktAMSMethod() 
    { 
      ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE); 
      int val = am.packtSensitiveMethod(2); 
      Log.i("CustomAMS", "return: " + val); 
    } 
}
```

我刚刚评论了之前的代码，并添加了一个执行`packtSensitiveMethod`的方法。

1.  现在运行完整的系统构建并启动模拟器。

### 注意

有时，模拟器可能会抱怨分区大小太小，必须调整大小。在这种情况下，您可以使用内存和分区大小开关。（请注意，此命令仅在构建 Android 的同一窗口中发出才有效。否则，您将不得不使用完全限定的路径并指定模拟器映像的完整路径——内核映像、系统映像等。）

例如，模拟器内存为 512，分区大小为 1024。

1.  启动`PacktLibraryClient`应用程序。观察 logcat。输出将类似于以下内容：

```kt
I/ActivityManager(   63): Start proc com.packtclient for activity com.packtclient/.Client: pid=344 uid=10010 gids={} 
I/dalvikvm(   63): Jit: resizing JitTable from 512 to 1024 
I/ARMAssembler(   63): generated scanline__00000177:03515104_00001002_00000000 [ 87 ipp] (110 ins) at [0x4386b6f0:0x4386b8a8] in 1311254 ns 
D/AndroidRuntime(  344): Shutting down VM 
W/dalvikvm(  344): threadid=1: thread exiting with uncaught exception (group=0x40015560) 
E/AndroidRuntime(  344): FATAL EXCEPTION: main 
E/AndroidRuntime(  344): java.lang.RuntimeException: Unable to start activity ComponentInfo{com.packtclient/com.packtclient.Client}: java.lang.SecurityException: Requires permission packt.PACKT_PERMISSION 
E/AndroidRuntime(  344):   at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:1647) 
E/AndroidRuntime(  344):   at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:1663) 
E/AndroidRuntime(  344):   at android.app.ActivityThread.access$1500(ActivityThread.java:117) 
E/AndroidRuntime(  344):   at android.app.ActivityThread$H.handleMessage(ActivityThread.java:931) 
E/AndroidRuntime(  344):   at android.os.Handler.dispatchMessage(Handler.java:99) 
E/AndroidRuntime(  344):   at android.os.Looper.loop(Looper.java:130) 
E/AndroidRuntime(  344):   at android.app.ActivityThread.main(ActivityThread.java:3683) 
E/AndroidRuntime(  344):   at java.lang.reflect.Method.invokeNative(Native Method) 
E/AndroidRuntime(  344):   at java.lang.reflect.Method.invoke(Method.java:507) 
E/AndroidRuntime(  344):   at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:839) 
E/AndroidRuntime(  344):   at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:597) 
E/AndroidRuntime(  344):   at dalvik.system.NativeStart.main(Native Method) 
E/AndroidRuntime(  344): Caused by: java.lang.SecurityException: Requires permission packt.PACKT_PERMISSION 
E/AndroidRuntime(  344):   at android.os.Parcel.readException(Parcel.java:1322) 
E/AndroidRuntime(  344):   at android.os.Parcel.readException(Parcel.java:1276) 
E/AndroidRuntime(  344):   at android.app.ActivityManagerProxy.packtSensitiveMethod(ActivityManagerNative.java:2918) 
E/AndroidRuntime(  344):   at android.app.ActivityManager.packtSensitiveMethod(ActivityManager.java:1077) 
E/AndroidRuntime(  344):   at com.packtclient.Client.invokeNewPacktAMSMethod(Client.java:28) 
E/AndroidRuntime(  344):   at com.packtclient.Client.onCreate(Client.java:22) 
E/AndroidRuntime(  344):   at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1047) 
E/AndroidRuntime(  344):   at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:1611) 
E/AndroidRuntime(  344):   ... 11 more 
W/ActivityManager(   63):   Force finishing activity com.packtclient/.Client
```

这里出了什么问题？在分析日志时，我们注意到抛出了安全异常。所以，我们的代码有效！敏感方法受到了保护。但是，如果`PacktLibraryClient`是一个合法的应用程序，并且需要访问`packtSensitiveMethod`，我们需要做两件事：

+   授予`PACKT_PERMISSION`。

+   使用平台证书对 APK 进行签名，因为权限是签名级别的权限。

1.  为此，请编辑`PacktLibraryClient`的`AndroidManifest.xml`文件，将以下标签添加到`application`标签之外：

```kt
<uses-permission android:name="packt.PACKT_PERMISSION" />
```

1.  接下来，通过添加以下行来编辑其`Android.mk`文件：

```kt
LOCAL_CERTIFICATE := platform

```

1.  现在我们将使用快捷方式快速构建 APK。在终端中，导航到您的 android 源的根目录，并使用以下命令：

```kt
mmm packages/apps/PacktLibraryClient

```

1.  这将构建 APK，但不会将其放入系统映像或推送到模拟器中。为了做到这一点，我们将重新挂载系统分区为读/写，然后手动推送它。如果你想直接将其放入系统映像并重新运行模拟器，请使用以下命令：

```kt
mmm packages/apps/PacktLibraryClient snod

```

1.  如果关闭了模拟器，请重新启动。然后通过`adb`进入 shell。

1.  要找出`/system`挂载在哪个块设备上，请使用：

```kt
cat /proc/mtd

```

输出如下：

```kt
dev:    size   erasesize  name 
mtd0: 3e100000 00020000 "system" 
mtd1: 3e100000 00020000 "userdata" 
mtd2: 04000000 00020000 "cache"
```

因此，`/system`是`mtd0`。

1.  重新挂载为读/写：

```kt
mount -o rw,remount -t yaffs2 /dev/block/mtd0 /system
chmod 777 /system/app

```

1.  现在退出`adb` shell，并推送构建的 APK。APK 将可在`ANDROID_SRC/out/target/product/generic/system/app`找到。

1.  以下命令将自动重新安装 APK 到模拟器上。再次启动应用程序并观察日志。

```kt
adb push PacktLibraryClient.apk /system/app
I/PACKTinAMS(   68): sensitive method called with parameter: 2 
I/CustomAMS(  372): return: 4 

```

我们看到一切都运行正常。

## 它是如何工作的...

应用程序获取对活动管理器服务的引用。这个引用是以`ActivityManager`对象的形式存在的。在客户端执行`packtSensitiveMethod` shim，这会导致一个 RPC 发送到活动管理器服务，在执行方法的主体之前执行权限检查。当我们的 APK 没有使用平台密钥签名并且没有包含正确的`<uses-permission>`标签时，这个检查失败，应用程序崩溃了。在第二个实例中，我们进行了适当的更改，权限检查成功了。

# 分析 PackageManagerService 类（应该知道）

软件包管理器服务是 Android 系统中的一个核心服务，也是最重要的服务之一。它管理系统中的所有软件包，并且对平台安全至关重要。它维护用户/组标识符和更高级别权限字符串之间的映射。在这个示例中，我们将看到软件包管理器服务如何管理和授予权限。正如您所知，Android 安全模型的基本方面是权限系统。这个系统由`PackageManagerService`类管理。

## 准备工作

软件包管理器服务的源代码位于`ANDROID_SRC/frameworks/base/services/java/com/android/server/`。在您喜欢的代码编辑器中打开名为`PackageManagerService.java`的文件。

## 操作方法...

1.  内置权限 UID 映射存储在文件系统中的`/system/etc/permissions/platform.xml`。这会加载到软件包管理器服务中的以下变量：

```kt
final SparseArray<HashSet<String>> mSystemPermissions =
            new SparseArray<HashSet<String>>();
```

1.  该服务在文件系统中维护用户应用程序权限，位于`/data/system/packages.xml`。启动模拟器并进入`adb` shell。我们将检查`packages.xml`文件的内容。使用以下命令获取副本： 

```kt
adb pull /data/system/packages.xml

```

1.  在文本编辑器中打开文件并搜索术语"packt"：

```kt
<package name="com.packtclient" codePath="/system/app/PacktLibraryClient.apk" nativeLibraryPath="/data/data/com.packtclient/lib" flags="1" ft="1389a1a82a8" it="13879fbb780" ut="1389a1b8ae2" version="10" userId="10010">
<sigs count="1">
<cert index="1" />
</sigs>
</package>
```

在这里我们注意到`PacktLibraryClient`被分配了`userId`为`10010`。

## 工作原理...

软件包管理器服务以适当的 Linux 权限将所有配置数据存储和管理在 XML 文件中。在内部，该服务利用 XML pull 解析器来读取和处理这些文件。其他服务会询问该服务，尤其是活动管理器服务，一个软件包是否拥有某个权限。

## 还有更多...

每当您对此服务进行更改时，请仔细重新考虑您的设计，只有在没有其他方法可以完成任务的情况下才运行单元测试。

要运行这些测试，在终端中运行以下命令（一如既往，我假设您已经通过包括`build/envsetup.sh`来设置了构建环境）：

```kt
mmm frameworks/base/tests/AndroidTests

```

然后启动模拟器，并在 ADB 上执行以下命令：

```kt
adb install -r -f out/target/product/passion/data/app/AndroidTests.apk

```

最后，进入`adb` shell 并执行：

```kt
adb shell am instrument -w -e class com.android.unit_tests.PackageManagerTests com.android.unit_tests/android.test.InstrumentationTestRunner

```

单元测试是**兼容性测试套件**（**CTS**）的一部分。CTS 是 Android 兼容性计划的一部分。其目标是在 Android 供应商实现之间实现标准化。有兴趣的读者可以在[`source.android.com/compatibility/overview.html`](http://source.android.com/compatibility/overview.html)上找到兼容性计划概述。CTS 本身的详细信息可以在[`source.android.com/compatibility/cts-intro.html`](http://source.android.com/compatibility/cts-intro.html)上找到。

# 分析互联网基础设施（应该知道）

进程/应用程序使用网络套接字访问网络（基于 WiFi 或基于 GSM 的网络）。Java 代码使用 Apache Harmony 套接字实现，本地代码使用标准的 bionic 套接字，与 libc 套接字调用相同。在这个示例中，我们将分析如何访问网络以及如何保护这些访问。

## 准备工作

在这个示例中，我们将查看一个内核源文件，所以请找到您在本书第一个示例中下载和构建的内核源代码。

Java 中的网络访问代码是在`OSNetworkSystem.java`中的 Apache Harmony 库中实现的，位于`ANDROID_SRC/libcore/luni/src/main/java/org/apache/harmony/luni/platform`。

这是在调用本机套接字调用之前的最后一点。

## 如何做...

1.  导航到我们下载并在本书介绍中构建的内核源代码。

1.  打开文件`ANDROID_SRC/kernel_code/goldfish/goldfish/net/ipv4/af_inet.c`。

1.  在这个文件中，我们观察到以下添加：

```kt
#ifdef CONFIG_ANDROID_PARANOID_NETWORK
#include <linux/android_aid.h>

static inline int current_has_network(void)
{
  return in_egroup_p(AID_INET) || capable(CAP_NET_RAW);
}
#else
static inline int current_has_network(void)
{
  return 1;
}
#endif
```

这个函数检查当前执行函数调用的进程是否属于`AID_INET`组。例如，看看名为`inet_create`的函数。

有一个对`current_has_network()`的调用，如果没有，它会返回`-EACCES`。

## 它是如何工作的...

`OSNetworkSystem.java`文件包含最终使用 libc 套接字调用在网络上执行 I/O 的本机方法。系统库调用最终会调用内核中实现的系统调用。在 Android 上，网络调用已更改为仅允许执行，如果当前执行系统调用的进程属于特定组。该组名为`inet`。

当应用程序请求`android.permission.INTERNET`时，并且即将运行时，包管理器服务会通知 Zygote 应用程序将作为`inet`组的一部分运行。

稍后，修改后的系统调用将验证进程是否作为`inet`的成员运行，并且调用成功。

`current_has_network()`函数使用`in_egroup_p(gid_t)`函数来检查执行它的进程的组标识符。只有当 Android 进程被授予`android.permission.INTERNET`时，它才作为该组的成员运行。

系统应用程序安装在 Android 设备的系统/只读分区上。这些应用程序使用系统证书之一进行签名（有四个证书：testkey，platform，shared 和 media），并执行某些特权任务。示例包括联系人应用程序和设置应用程序。有时，您的设计可能需要更改这些系统应用程序。在下一个教程中，我们将学习最重要的系统应用程序的目的，并学习如何编译对它们所做的更改。

# 学习各种系统应用程序的功能（必须知道）

所有系统应用程序的源代码位于`ANDROID_SRC/packages/`。在本教程中，我们将了解此位置上最重要的包的重要性。

## 准备工作

导航到`ANDROID_SRC/`下的`/packages`目录。

## 如何做...

以下表总结了四个顶级目录及其包含的代码：

| 目录名称 | 目的 |
| --- | --- |
| `Apps` | 包含基于 Activity 的应用程序的代码。始终包含 UI 元素。 |
| `Experimental` | 由谷歌员工维护的随机工具。内容可能会被归档而不另行通知。 |
| `Inputmethods` | 三种默认输入法的来源（根据 2.3.4_r1），即 LatinIME，OpenWnn 和 PinyinIME |
| `Providers` | 所有主要内容提供程序的代码，包括联系人和短信/彩信 |
| `Wallpapers` | 与开源系统一起提供的壁纸的代码 |

现在，我们将了解`Apps`和`Providers`下最重要的目录的目的。

在`/Apps`目录下：

| 目录名称 | 目的 |
| --- | --- |
| `Browser/` | Android 浏览器的前端 |
| `Camera/` | 相机应用 |
| `Contacts/` | 前端联系人应用程序 |
| `Gallery/` | 用于查看 SD 卡上的图像和视频的应用程序 |
| `Launcher2/` | 默认的启动屏幕 |
| `Mms/` | MMS/SMS 应用程序 |
| `Nfc/` | NFC 控制应用程序，包括与 NFC 芯片交互的本机库。包括 NFC 管理器和 Nfc 服务 |
| `Phone/` | 拨号应用程序和接听电话的用户界面 |
| `Settings/` | 系统设置和安全设置应用程序 |

在`/Providers`目录下：

| 目录名称 | 目的 |
| --- | --- |
| `ApplicationsProvider/` | 维护所有已安装应用程序的数据库 |
| `ContactsProvider/` | 用于维护设备上无数服务使用的联系人数据存储的代码 |
| `TelephonyProvider/` | 存储三种类型的数据：短信内容、彩信内容和当前网络配置 |

## 工作原理...

所有上述应用程序都有一个 make 文件，Android 构建系统在进行完整系统构建期间会捡起它。构建的 APK 被打包到`system.img`中，并在系统启动时安装到`/system/app`目录中。

# 修改搜索小部件应用程序（应该知道）

在这个示例中，我们将修改快速搜索小部件中的默认搜索文本。这个示例的目的是让读者熟悉进行更改、编译和测试这些更改。

## 准备工作

搜索小部件的代码位于名为`QuickSearchBox`的目录下`ANDROID_SRC/packages/apps/`。导航到此目录，打开`ANDROID_SRC/packages/apps/QuickSearchBox/res/drawable/text_field_search_empty_google.xml`。

## 如何做...

1.  通过替换 XML 布局文件来编辑：

```kt
<item android:drawable="@drawable/hint_google" />
```

with

```kt
<item android:drawable="@drawable/packt" />
```

1.  现在，使用流行的图像编辑器创建一个尺寸为 97 x 40 像素的图标。我们在 GIMP 中为此任务创建了一个 Packt 标志变体。确保将图像保存为 PNG 类型。将此图像复制到三个位置：`drawable-hdpi`，`drawable-ldpi`和`drawable-mdpi`。

### 注意

对于每种屏幕密度，应存储具有不同参数的图像。否则，您可能会注意到渲染问题；图像可能看起来不清晰。由于我们只是在这里演示一个概念，我不专注于创建不同尺寸的图像。

1.  现在我们将构建新的`QuickSearchWidget`应用程序。像往常一样，在终端中，导航到 Android 源代码目录，包括环境设置并启动模拟器目标。发出以下命令：

```kt
mmm packages/app/QuickSearchWidget

```

1.  这将仅构建搜索小部件。构建完成后，启动模拟器并切换到`adb` shell。我们需要将系统分区重新挂载为读/写，因为系统应用程序安装在只读挂载的分区上。为了能够写入新的`QuickSearchWidget`应用程序的可执行文件，而不必重新构建和重新刷写整个操作系统，我们将简单地“推送”生成的 APK 到重新挂载的目录上。

1.  我们需要找出`/system`分区挂载在哪里。在`adb` shell 中，执行以下命令：

```kt
cat /proc/mtd

```

输出将类似于以下内容：

```kt
dev:    size   erasesize  name

mtd0: 0c200000 00020000 "system"

mtd1: 0c200000 00020000 "userdata"

mtd2: 04000000 00020000 "cache"
```

### 注意

在真实设备上，这将显示不同。MTD 挂载根据设备制造商而异。这里看到的输出是在模拟器上，这是您在尝试任何真实设备之前应该进行初始开发和调试的地方。

1.  我们看到`/system`存在于`mtd0`中。要重新挂载为读/写，在`adb` shell 中：

```kt
mount -o rw,remount -t yaffs2 /dev/block/mtd0 /system
chmod 777 /system/app

```

1.  现在，退出`adb` shell 并导航到`ANDROID_SRC/out/target/product/generic/system/app`。这是构建的 APK 存储的位置。

1.  在终端中，执行以下命令：

```kt
adb push QuickSearchWidget.apk /system/app

```

这将安装新的小部件。更改应该在模拟器上可见：

![如何做...](img/9762OS_10_01.jpg)

## 工作原理...

`mmm`命令有选择地构建代码。它的参数是包含`Android.mk`文件的源目录的路径。路径应以可以构建的目标名称结尾。例如，如果打开`Android.mk`文件，目标由`LOCAL_PACKAGE_NAME`标签标识。

正如我们在之前的示例中看到的，系统分区出于安全原因被挂载为只读。要安装系统应用程序，我们需要重新挂载。重新挂载只能由 root 用户完成。在模拟器上，ADB 默认以 root 身份运行。

仅仅将 APK 推送到`/system/app`会导致其安装的副作用。

根据不同的屏幕密度使用不同的`drawable`目录。这与编写基于 SDK 的应用程序时使用的目录具有相同的含义。

# 使用 CCACHE（应该知道）

CCACHE 是用于 C/C++代码的编译器缓存。Android 框架分发包括几个位于`/external`目录中的本机库。您可能已经注意到，在干净的构建中，即使它们从未更改过，这些库也会被重新构建。重新构建这些库需要很长时间。为了节省您的时间，您可以使用 CCACHE 机制。在这个教程中，我们将介绍一些使 Android 系统开发人员生活更轻松的技巧和窍门。特别是，我们将解释如何使用 CCACHE 机制，如何选择性地编译模块以及如何测试它们。

## 准备工作

导航到您的 Android 源根目录，并发出`make clean`命令。我们想要从头开始构建缓存。

## 如何做到...

1.  在 Android 源根目录的终端中，执行以下命令：

```kt
export USE_CCACHE=1
export CCACHE_DIR=/<path_of_your_choice>/.ccache

```

前两个环境变量控制我们是否使用 CCACHE，如果使用的话，CCACHE 目录的位置在哪里。您可以自由地使用任何目录作为 CCACHE。

1.  在`prebuilt/linux-x86`目录中，有一个包含 ccache 二进制文件的`ccache`目录。只需执行以下命令，就会呈现出这个二进制文件可以做什么的选项：

```kt
prebuilt/linux-x86/ccache/ccache

```

这个命令提供的输出如下：

```kt
ccache, a compiler cache. Version 2.4
Copyright Andrew Tridgell, 2002
Usage:
 ccache [options]
 ccache compiler [compile options]
 compiler [compile options]    (via symbolic link)
Options:
-s                      show statistics summary
-z                      zero statistics
-c                      run a cache cleanup
-C                      clear the cache completely
-F <maxfiles>           set maximum files in cache
-M <maxsize>            set maximum size of cache (use G, M or K)
-h                      this help page
-V                      print version number

```

因此，我们可以通过以下命令设置最大缓存大小：

```kt
prebuilt/linux-x86/ccache/ccache -M 20G

```

这将把它设置为 20GB。

1.  现在，如果您为任何目标启动 make（选择模拟器，因为它最容易），我们可以观察 CCACHE 是如何被使用的。在另一个终端中，使用以下命令：

```kt
watch -n1 -d prebuilt/linux-x86/ccache/ccache -s

```

`watch`命令用于监视缓存状态和使用情况。

助手文件应保存为`build_helper.sh`：

```kt
#!/bin/sh
source build/envsetup.sh
export USE_CCACHE=1
export CCACHE_DIR=<YOUR_CACHE_PATH>/.ccache
prebuilt/linux-x86/ccache/ccache -M 20G
lunch $1
```

模拟器目标的一个示例调用是：

```kt
./build_helper.sh 1

```

并且它输出如下：

```kt
including device/htc/passion/vendorsetup.sh
including device/samsung/crespo/vendorsetup.sh
Set cache size limit to 20971520k

=========================================

PLATFORM_VERSION_CODENAME=REL
PLATFORM_VERSION=2.3.4
TARGET_PRODUCT=generic
TARGET_BUILD_VARIANT=eng
TARGET_SIMULATOR=false
TARGET_BUILD_TYPE=release
TARGET_BUILD_APPS=
TARGET_ARCH=arm
HOST_ARCH=x86
HOST_OS=linux
HOST_BUILD_TYPE=release
BUILD_ID=GRJ22

=========================================
```

## 工作原理...

Android 构建系统是这样编写的，如果 CCACHE 被环境变量激活，它将自动使用。

上面的 shell 脚本非常简单，简化了开发人员初始化进一步工作环境的任务。每当为 Android 系统开发打开新终端时，必须执行 shell 脚本中的命令。因此，执行一个 shell 脚本比执行多个单独的命令更容易。

# 选择性编译模块（应该知道）

有时，您不需要为一个代码更改编译整个框架。在这种情况下，您可以利用选择性编译。

## 准备工作

例如，我们将使用选择性编译来重新构建`ANDROID_SRC/packages/apps/Phone`下的`Phone`应用程序。

## 如何做到...

做一个小改变，比如在一个源文件中添加一个注释：

1.  要重新构建，请使用以下命令：

```kt
mmm packages/apps/Phone

```

1.  要在运行设备/模拟器上测试您的更改，只需使用之前介绍的系统分区重新挂载技术。

## 工作原理...

`mmm`仅在指定的参数上调用构建系统，并将输出放在指定的位置（这是从正在编译的模块的 make 文件中推断出来的）。

只要您在设备/模拟器上替换所有重新构建的组件，就可以为任何模块使用这种技术。

# 构建命令技巧（应该知道）

这个教程将为您提供一些较少为人知的`make`命令选项。

## 准备工作

您只需要一个设置好构建环境的终端窗口。

## 如何做到...

以下表格列出了标准 make 的命令选项：

| 命令 | 目的 |
| --- | --- |
| `Make sdk` | 构建 SDK |
| `Make snod` | 从当前可用的二进制文件构建系统映像 |
| `Make services` | 创建包含所有系统服务的服务 JAR |
| `Make runtime` | 构建作为 Java-based Android 框架和用于其功能的本机内容之间粘合剂的本机代码 |
| `Make droid` | 默认 make |
| `Make modules` | 显示可以使用`make <MODULE_NAME>`构建的所有模块的列表 |
| `Make clean` | 完全清除所有已编译文件 |
| `Make clobber` | 与`rm -rf out/`相同 |
| `Make clean-<LOCAL_MODULE>` | 仅清理`LOCAL_MODULE`的任何已构建文件 |

## 它是如何工作的...

这些命令可以定期用于开发过程中的多个任务。例如，假设我想重新构建 Android 框架并将其推送到模拟器，我可以使用以下命令序列，编写为一个 shell 脚本：

你可以将此构建文件保存为`mk_frmwrk.sh`：

```kt
#!/bin/sh
mmm -j4 frameworks/base frameworks/base/core/res snod
adb shell stop
adb sync
adb shell start

```

这将重新编译框架代码，重新编译资源，重建`system.img`并将其同步到当前连接的设备。
