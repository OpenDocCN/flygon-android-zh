# 第四章：从安卓设备逻辑提取数据

本章将尽可能使用免费和开源工具来覆盖逻辑数据提取。本章大部分内容将使用之前在第二章中讨论过的 ADB 方法。

在本章结束时，读者应该熟悉以下内容：

+   逻辑提取的含义

+   逻辑提取可以期望得到的数据

+   有和没有 Root 权限时可用的数据

+   手动 ADB 数据提取

+   ADB 备份提取

+   ADB dumpsys 信息

+   如何绕过安卓锁屏

+   SIM 卡提取

# 逻辑提取概述

在数字取证中，逻辑提取一词通常用于指不恢复已删除数据的提取，或者不包括证据的完整比特对比拷贝。然而，在第一章中定义的更正确的逻辑提取的定义是任何需要与基本操作系统通信的方法。由于与操作系统的交互，取证员无法确定他们已经恢复了所有可能的数据；操作系统正在选择允许取证员访问哪些数据。

在传统计算机取证中，逻辑提取类似于复制和粘贴文件夹以从系统中提取数据；这个过程只会复制用户可以访问和看到的文件。如果被复制的文件夹中存在任何隐藏或已删除的文件，它们将不会出现在粘贴版本的文件夹中。

然而，移动取证中逻辑提取和物理提取之间的界限要比传统计算机取证模糊得多。例如，由于 SQLite 数据库的普遍使用，可以经常从移动设备的逻辑提取中恢复已删除的数据。此外，几乎每次移动设备的提取都需要与安卓操作系统进行某种形式的交互；没有简单的等同于拔出硬盘并在不启动硬盘的情况下对其进行成像的操作。对于我们的目的，我们将逻辑提取定义为获取用户可见的数据的过程，并且可能包括已标记为删除的数据。

## 哪些数据可以逻辑恢复？

在大多数情况下，任何和所有用户数据都可以通过逻辑方式恢复：

+   联系人

+   通话记录

+   短信/彩信

+   应用程序数据

+   系统日志和信息

这些数据的大部分存储在 SQLite 数据库中，因此甚至可以通过逻辑提取恢复大量已删除的数据。

### Root 权限

在法庭上分析安卓设备时，限制因素通常不是所寻找的数据类型，而是检查员是否有能力访问数据。在第二章中已经广泛涵盖了 Root 访问权限，但这一点很重要，值得重复。上述所有存储在内部闪存存储器上的数据都是受保护的，需要 Root 访问权限才能读取。例外情况是存储在 SD 卡上的应用程序数据，这将在本书后面讨论。

没有 root 权限，法医检查员无法简单地从数据分区复制信息。检查员将不得不找到某种方法来提升他们的权限，以便访问联系人、通话记录、短信/彩信和应用程序数据。这些方法通常带有许多风险，例如可能破坏或“砖化”设备（使其无法启动），并且可能会改变设备上的数据以获得永久性。这些方法通常因设备而异，并且没有通用的、一键式的方法可以获得对每个设备的 root 权限。商业移动法医工具，如**MicroSystemation XRY**和**Cellebrite UFED**，具有内置功能，可以临时和安全地 root 许多设备，但不涵盖所有 Android 设备的广泛范围。

在本章中，我们将注意到每种技术所需的 root 权限。

### 注意

是否对设备进行 root 处理应根据您所在地区的操作程序和法院意见做出决定。通过 root 获取的证据的法律接受程度因司法管辖区而异。

# 手动 ADB 数据提取

ADB 的`pull`命令可用于直接从设备上将单个文件或整个目录拉到法医检查员的计算机上。这种方法对于小型、有针对性的检查特别有用。例如，在严格涉及短信的调查中，检查员可以选择只拉取相关文件。

## USB 调试

ADB 环境的设置在本书中已经讨论过。然而，受检设备也必须正确配置。USB 调试是法医检查员的计算机与设备通信的实际方法。**USB 调试**选项位于**设置**菜单中的**开发者选项**下。然而，从 Android 4.2 开始，开发者选项菜单是隐藏的；要显示它，用户必须转到**设置** | **关于手机**，然后点击**版本号**字段*七*次。屏幕上会出现一个对话框，上面写着**您现在是开发者！**此时，**开发者选项**在**设置**菜单中可用；只需打开此菜单并选择**启用 USB 调试**。

除了 USB 调试，检查员的计算机上还必须安装正确的驱动程序。通常可以在制造商的网站上或[www.xda-developers.com](http://www.xda-developers.com)上找到。如果在计算机上安装了商业法医工具，则可能已经安装了适当的驱动程序。

### 提示

另一个很好的资源是可以免费下载的通用 ADB 驱动程序，网址为[`drivers.softpedia.com/get/MOBILES/Clockworkmod/Clockworkmod-Universal-Android-ADB-Driver.shtml`](http://drivers.softpedia.com/get/MOBILES/Clockworkmod/Clockworkmod-Universal-Android-ADB-Driver.shtml)。

在 Android 4.2.2 之前，启用**USB 调试**是与设备通过 ADB 通信的唯一要求。在 Android 4.2.2 中，Google 添加了**安全 USB 调试**选项。**安全 USB 调试**选项增加了在设备屏幕上选择连接到计算机的额外要求；这可以防止来自不受信任计算机的 ADB 访问被锁定的设备：

![USB 调试](img/image00302.jpeg)

RSA 指纹对话框

如果选择了**始终允许此计算机**，设备将存储计算机的 RSA 密钥，并且在将来连接到该计算机时，提示将不会出现，即使设备被锁定。

### 提示

根据设备和操作系统版本的不同，可能可以规避**安全 USB 调试**保护。在[`labs.mwrinfosecurity.com/advisories/2014/07/03/android-4-4-2-secure-usb-debugging-bypass/`](https://labs.mwrinfosecurity.com/advisories/2014/07/03/android-4-4-2-secure-usb-debugging-bypass/)上找到更多信息。

还可以通过使用先前获得授权访问设备的计算机来绕过**安全 USB 调试**，这将在本章后面的 Android Lollipop 部分中讨论。

一旦**USB 调试**已启用并且**安全 USB 调试**检查通过（取决于 Android 版本），设备就准备好进行检查了。要验证设备是否已连接并准备使用 ADB，执行以下命令：

```kt
adb devices

```

如果没有显示任何设备，请确保**USB 调试**已启用，并且已安装正确的设备驱动程序：

![USB 调试](img/image00303.jpeg)

如果设备状态为`离线`或`未授权`，则需要在屏幕上选择**安全 USB 调试**提示：

![USB 调试](img/image00304.jpeg)

如果一切正常运行，设备状态应显示为`设备`：

![USB 调试](img/image00305.jpeg)

### 使用 ADB shell 来确定设备是否已 root

确定设备是否已 root 的最简单方法是使用 ADB shell。这将在设备上打开一个 shell，取证人员可以在自己的计算机上访问该 shell；这意味着在 shell 中运行的任何命令都将在设备上执行。一旦**USB 调试**已启用并且**安全 USB 调试**已绕过（或者从恢复模式中，如本章后面讨论的），在本地计算机上打开终端并运行：

```kt
adb shell

```

shell 将以`$`或`#`的方式出现：

![使用 ADB shell 来确定设备是否已 root](img/image00306.jpeg)

在 Linux 系统中，`#`符号用于表示根用户，`$`符号表示非根用户。如果 shell 返回显示`#`，则 shell 具有根访问权限：

![使用 ADB shell 来确定设备是否已 root](img/image00307.jpeg)

在一些已 root 的设备上可能需要进一步的步骤。如果 shell 返回`$`，尝试运行`su`命令：

```kt
su

```

如果设备上安装了 su 二进制文件，通常是 root 过程的一部分，这将提升 shell 的权限到 root，如果 shell 没有以 root 权限打开的话。

### 提示

一些旧设备会自动以 root 身份运行 shell；仅仅打开 ADB shell 可能就足以给取证人员 root 访问权限。

## ADB pull

如第二章*设置 Android 取证环境*中讨论的，ADB `pull`命令用于将文件从设备传输到本地工作站。ADB `pull`命令的格式为：

```kt
adb pull [-p] [-a] <remote> [<local>]

```

可选的`-p`标志显示传输的进度，而可选的`-a`标志将复制文件的时间戳和模式。`<remote>`参数是设备上文件的确切路径。可选的`<local>`参数是文件将被写入到取证人员计算机上的路径。如果没有指定本地路径，文件将被写入到当前工作目录。要查看 ADB `pull`命令的样子，运行以下命令：

```kt
adb pull –p /data/data/com.android.providers.telephony/databases/mmssms.db C:/Users/Cases/Case_0001

```

此命令将从设备中拉取短信数据库文件，并将其写入到案例的目录中。再次注意，设备必须已 root 才能正常工作；否则，输出将只显示拉取了`0`个文件。在我们的案例中，获得以下输出：

![ADB pull](img/image00308.jpeg)

前面的输出显示文件大小为`1020400`字节。由于我们的命令，`mmssms.db`数据库现在位于`Case_0001`文件夹中：

![ADB pull](img/image00309.jpeg)

从设备中拉取的数据库，在 Windows 资源管理器中可见

现在可以使用 SQL 浏览器或其他取证工具来检查数据库，这将在第七章*Android 应用程序的取证分析*中进行讨论。

同样，如果调查人员希望为整个应用程序拉取文件，也可以使用 ADB pull：

![ADB pull](img/image00310.jpeg)

这一次，ADB pull 命令获取了`com.google.android.gm`目录中的每个文件，这恰好包含了 Gmail 的所有数据。输出非常长，因为它逐个列出了所有`31`个拉取的文件，所以整个输出在下图中没有显示，我们可以看到传输的总大小显示为`1233373`字节：

![ADB pull](img/image00311.jpeg)

现在，`Case_0002`目录包含了 Gmail 应用程序的所有文件，如下截图所示：

![ADB pull](img/image00312.jpeg)

在 Windows 资源管理器中看到的从 Gmail 目录中拉取的所有文件

甚至可以做到以下事情：

```kt
adb pull -p /data/data/ \Cases\Case_0003

```

这将从`/data/data`目录中拉取所有可用的逻辑文件，并将它们放在检查员的`Case_0003`文件夹中。这并不等同于物理镜像，因为某些文件会被跳过，已删除的文件不会被复制，但这是拉取用户应用程序数据绝大部分的简单方法。

ADB `pull`命令的另一个优点是它非常适用于脚本目的。一个知识渊博的检查员可以维护一个常见感兴趣文件路径的列表，并编写一个自动从设备中拉取这些文件的脚本，甚至可以让脚本自动拉取整个`/data/data`目录。一个执行此功能的 Python 代码的简单示例是：

```kt
from subprocess import Popen
from os import getcwd

command = "adb pull /data/data " + getcwd() + "\data_from_device"
p = Popen(command)
p.communicate()
```

请注意，代码并不是非常精练；它的唯一目的是说明 ADB 命令可以被脚本化的简易性。至少，正确实现代码应该包括指定输出目录的选项并处理任何错误。然而，前述代码的六行足以逻辑上拉取整个`/data/data`目录，假设**USB 调试**已启用并且设备已 root。

## 恢复模式

为了真正具有法庭取证的可靠性，不应在手机开机时使用 ADB 数据提取。当设备正在运行时，时间戳可能会被修改，应用程序可能在后台运行并更新文件。为了避免这种情况，检查员应该将设备置于自定义恢复模式中，如第二章中所示，*设置 Android 取证环境*，如果可能的话。通过原始 Android 恢复模式无法访问 ADB。通常，rooting 过程的第一步是刷入自定义恢复模式，以便在出现问题时修复设备。已 root 的设备更有可能包含自定义恢复模式，但是也有可能将自定义恢复模式刷入未 root 的设备。这种方法还允许检查员避免在较新版本的 Android 上出现的**安全 USB 调试**提示，尽管我们的测试显示这在 Android Lollipop 上不起作用。恢复模式也可能不需要启用**USB 调试**，这使其成为绕过锁定设备的绝佳选择。

### 注意

这种方法不适用于启用了全盘加密的设备。进入恢复模式将*不*解密数据分区。

进入恢复模式的过程对于每个设备都会有所不同。通常情况下，它涉及一些关机并按住音量和电源键的组合。特定型号的指南可以在线找到。

原始恢复模式通常会显示一个正在操作的 Android 的图片：

![恢复模式](img/image00313.jpeg)

原始恢复模式

自定义恢复模式看起来像以下的截图。此外，原始恢复模式将不允许 ADB 通信；运行 adb devices 将只显示没有设备。

### 注意

许多设备的自定义恢复镜像可以在[`www.clockworkmod.com/rommanager`](https://www.clockworkmod.com/rommanager)和[`teamw.in/project/twrp2`](http://teamw.in/project/twrp2)找到。

如果设备处于自定义恢复模式，并且在审查员的计算机上安装了正确的驱动程序，则可以通过 ADB 访问设备，就像它是活动的一样。请注意，使用 adb devices 命令现在显示它处于恢复模式：

![恢复模式](img/image00314.jpeg)

在审查员可以开始通过 ADB 提取数据之前，还有最后一步：必须挂载数据分区以访问用户数据。一些自定义恢复可能会自动挂载这个分区，而其他可能不会。如果使用上面的 URL 中的 Clockwork Mod Recovery 或 Team Win Recovery Project（TWRP）映像，可以通过选择 Mounts 然后选择数据分区来挂载数据分区，如下面的屏幕截图所示。恢复菜单通常可以使用音量键上下移动和电源按钮选择，或者根据使用的自定义恢复映像而可能是基于触摸的。

对于 TWRP 恢复，从主恢复屏幕中选择**Mount**：

![恢复模式](img/image00315.jpeg)

选择**Mount**后，选择要挂载的分区：

![恢复模式](img/image00316.jpeg)

在 Clockwork Mod Recovery 中，选择 mounts and storage：

![恢复模式](img/image00317.jpeg)

然后选择要挂载的分区：

![恢复模式](img/image00318.jpeg)

一旦数据分区（以及审查员想要调查的任何其他分区）被挂载，审查员就可以执行 ADB 数据提取，就像本章前面演示的那样。

如果设备没有自定义恢复，下一节将显示如何启动到自定义恢复。

## 快速启动模式

Fastboot 是 Android SDK 中内置的另一个协议实用程序，用于直接与设备的引导加载程序进行交互。基本上，它是 ADB 的一个更低级别版本，并且经常用于向设备刷入新映像。这对审查员有什么帮助呢？

Fastboot 可以允许审查员从自定义恢复映像启动，并临时获得设备的 root 访问权限，从而访问以其他方式无法访问的数据。Fastboot 不需要启用 USB 调试或 root 访问权限。将自定义引导加载程序加载到设备上通常由商业取证工具使用，以临时获取设备的 root 权限，但是熟练的审查员也可以手动执行该过程。使用此方法，恢复映像加载到 RAM 中；设备上的任何永久数据都不会以任何方式被更改。

使用 fastboot 最重要的要求是解锁引导加载程序；锁定的引导加载程序将不允许设备从制造商专门签名的代码启动。不幸的是，出于取证目的，大多数设备不再出厂时带有解锁的引导加载程序，因为这是一个严重的安全风险，并且手动解锁引导加载程序通常会擦除用户数据。因此，这种方法适用的设备数量有些有限。但是，当它起作用时，对于审查员来说，这是一个绝对无价的工具。

### 注意

此方法不适用于启用了全盘加密的设备。进入恢复模式将*不*解密数据分区。

### 确定引导加载程序状态

与涉及 Android 取证的所有内容一样，确定引导加载程序是否已锁定没有一种保证的方法，因为这取决于制造商。要进入引导加载程序，请使用以下 ADB 命令：

```kt
adb reboot bootloader

```

设备应该启动到显示有关引导加载程序的信息的屏幕。通常，此屏幕将显示引导加载程序状态，如下面的屏幕截图所示。

以下是 Nexus 5 的通用、原始快速启动菜单。请注意，**Lock State**指示引导加载程序已解锁：

![确定引导加载程序状态](img/image00319.jpeg)

标准的 HTC 快速启动屏幕如下：

![确定引导加载程序状态](img/image00320.jpeg)

以下是标准的三星**Odin**模式屏幕；Odin 是三星专有的等效快速启动：

![确定引导加载程序状态](img/image00321.jpeg)

### 引导到自定义恢复映像

确定引导加载程序已解锁后，检查员将需要一个自定义恢复映像来引导。恢复映像的绝佳来源是[`www.clockworkmod.com/rommanager`](https://www.clockworkmod.com/rommanager)或[`teamw.in/twrp_view_all_devices`](http://teamw.in/twrp_view_all_devices)。这两个网站都提供各种设备的覆盖，并且将为此方法提供相同的功能。

### 注意

对于正在检查的设备，选择正确的恢复映像绝对至关重要；它们不能互换，从错误的映像引导可能会砖化设备。

选择并下载恢复映像后，需要将设备放入快速启动模式。可以通过以下两种方式之一完成：

+   ADB

+   物理设备按钮

要通过 ADB 进入快速启动设备，设备必须已启用**USB 调试**。通过 ADB 进入快速启动模式的命令是：

```kt
adb reboot bootloader

```

如果无法启用 USB 调试或无法使用 ADB，通常还有一组按钮组合可在设备启动时按下，类似于进入恢复模式。可以在线找到每个设备特定的确切组合。

设备进入快速启动模式后，运行以下命令将验证设备是否已连接并准备好通信：

```kt
fastboot devices

```

以下命令将加载自定义恢复映像到 RAM 并将设备引导到恢复模式：

```kt
fastboot boot 'path to image'

```

![引导到自定义恢复映像](img/image00322.jpeg)

设备现在应该重新启动并进入恢复模式。如恢复模式部分所示，可能需要挂载/data 分区以访问用户数据。

进入 ADB shell 将显示检查员现在具有根访问权限。设备将允许根访问权限，直到重新启动。

![引导到自定义恢复映像](img/image00323.jpeg)

如果快速启动引导命令失败，则很可能表明设备的引导加载程序已锁定，如下面的屏幕截图所示：

![引导到自定义恢复映像](img/image00324.jpeg)

# ADB 备份提取

Google 在 Android 4.0 冰淇淋三明治中实现了 ADB 备份功能。这允许用户（和取证检查员）通过 ADB 将应用程序数据备份到本地计算机。此过程不需要根权限，因此对于取证目的非常有用。但是，它不会获取设备上安装的每个应用程序。当开发人员创建新应用程序时，默认情况下将其设置为允许备份，但开发人员可以更改此设置。实际上，似乎绝大多数开发人员保留默认设置，这意味着备份可以捕获大多数第三方应用程序。不幸的是，大多数 Google 应用程序禁用备份；来自应用程序（如 Gmail 和 Google Maps）的完整应用程序数据将不包括在内。

### 提示

此方法对于锁定的设备将无效，因为需要用户与屏幕进行交互。

## 通过 ADB 提取备份

ADB 备份命令的格式是：

```kt
adb backup [-f <file>] [-apk|-noapk] [-obb|-noobb] [-shared|-noshared] [-all] [-system|-nosystem] [<packages...>]

```

以下是标志：

+   `-f`：为输出文件命名路径。如果未指定，默认为当前工作目录中的`backup.ab`。

+   `[-apk|noapk]`：选择是否备份`.apk`文件。默认为`-noapk`。

+   `[-obb|-noobb]`：选择是否备份`.obb`（APK 扩展）文件。默认为`-noobb`。

+   `[-shared|-noshared]`：选择是否备份共享存储和 SD 卡中的数据。默认为`-noshared`。

+   `[-all]`：包括启用备份的所有应用程序。

+   `[-system|-nosystem]`：选择是否包括系统应用程序。默认为`-system`。

+   `[<packages>]`：显式命名要备份的应用程序包。如果使用`-all`或`-shared`，则不需要。

捕获所有可能的应用程序数据的示例 ADB 备份命令如下：

```kt
adb backup –f C:/Users/Cases/Case_0001/backup.ab –shared –all

```

或者，捕获特定应用程序数据的示例 ADB 备份命令如下：

```kt
adb backup –f C:/Users/Cases/Case_0001/facebook.ab com.facebook.katana

```

您应该看到类似的东西：

![通过 ADB 提取备份](img/image00325.jpeg)

在执行备份时，用户必须在设备上批准备份。这意味着不能绕过屏幕锁定进行备份：

![通过 ADB 提取备份](img/image00326.jpeg)

在设备上接受备份

根据安装的应用程序数量，备份过程可能需要相当长的时间。

## 解析 ADB 备份

生成的备份数据存储为`.ab`文件，但实际上是使用**Deflate**算法压缩的`.tar`文件。如果在创建备份时在设备上输入了密码，文件也将被 AES 加密。还应该提到，这些文件可能存在于嫌疑人的计算机上，并且可以使用相同的方法进行分析。

有许多免费的实用程序可以将`.ab`备份文件转换为可以查看的`.tar`文件。其中一个实用程序是 Android 备份提取工具，位于[`sourceforge.net/projects/adbextractor/`](http://sourceforge.net/projects/adbextractor/)。 

要使用 Android 备份提取工具，只需将其文件提取到备份所在的目录中。运行该实用程序的命令是：

```kt
java -jar abe.jar unpack backup.ab backup.tar

```

如果命令正确运行，命令行将显示如下内容：

![解析 ADB 备份](img/image00327.jpeg)

输出的第一行通知审查员文件未加密。如果文件已加密，审查员将不得不在命令行的末尾作为参数传递密码。如输出所示，即使备份仍然被压缩，上一节中创建的备份大约为 4GB。`.tar`文件将位于命令行指定的路径或当前工作目录（如果未指定路径）。可以在 Linux 命令行上手动解压缩`.tar`文件，也可以使用许多 Windows 存档实用程序之一，如 WinRAR 或 7Zip：

![解析 ADB 备份](img/image00328.jpeg)

备份中的目录，在 Windows 资源管理器中查看

## ADB 备份中的数据位置

现在备份已转换为`.tar`文件并进行了提取，审查员可以查看备份中包含的数据。在我们的示例中，在备份的根目录中找到了两个目录：

+   `apps`：此文件夹包含备份中包含的应用程序的`/data/data`中的数据。

+   `shared`：此文件夹包含 SD 卡中的所有数据，仅在命令行传递了`-shared`参数时才存在。

请注意，应用程序目录中的文件存储在其包名称的目录中（就像在 ADB shell 内部的`/data/data`中看到的那样），共享目录正是用户如果将 SD 卡插入计算机中所看到的。

作为从备份中提取的用户数据的良性示例，下面显示了用户的**Pandora**活动。 Pandora 是一个在 Google Play 商店中有数百万次下载的流媒体音乐服务。 Pandora 的应用程序数据将包含在备份的 apps 文件夹中，文件夹名为`com.pandora.android`。

![ADB 备份中的数据位置](img/image00329.jpeg)

从备份中提取的 Pandora 目录

这是一个相当标准的 Android 应用程序布局，如第二章中所讨论的那样，*设置 Android 取证环境*。应用程序的数据库将在`db`文件夹中：

![ADB 备份中的数据位置](img/image00330.jpeg)

Pandora 备份的 db 文件夹中的文件

XML 配置设置将在`sp`文件夹中：

![ADB 备份中的数据位置](img/image00331.jpeg)

Pandora 备份的 sp 文件夹中的文件

使用数据库查看器查看`pandora.db`，可以查看用户创建的站点以及创建时间戳：

![ADB 备份中的数据位置](img/image00332.jpeg)

从备份中的 pandora.db 的内容

在 XML 首选项文件中，可以在`firstInstallId`下找到应用程序安装的时间戳。请注意，转换时间戳的确切方法显示在第七章中，*Android 应用程序的法医分析*中：

![ADB 备份中的数据位置](img/image00333.jpeg)

XML 首选项文件的内容

如果由于某种奇怪的原因，用户的 Pandora 使用在调查中是一个重要问题，调查员可以从这两个看似无害的文件中得出什么结论？

首先，`lastTransmission`和`firstInstallID`时间戳相差毫秒，表明该应用程序安装后从未使用过。此外，每个站点的创建日期在某些情况下早于应用程序的安装日期，有些甚至早于几年。这表明用户在其他设备上使用了 Pandora，这可能与调查高度相关。

虽然 Pandora 通常与数字取证调查无关，但它是通过 ADB 进行简单备份的数据的一个例子。更详细的应用程序分析将在第七章中呈现，*Android 应用程序的法医分析*。

# ADB Dumpsys

**Dumpsys**是内置在 Android 操作系统中的工具，通常用于开发目的，以显示设备上运行的服务的状态。但是，它也可能包含法医学上有趣的信息。Dumpsys 不需要 root 访问权限，但与所有 ADB 命令一样，它需要设备上启用 USB 调试和绕过安全 USB 调试。

可以查看的确切服务因设备和 Android 版本而异。要查看可以转储的所有可能服务的列表，请运行以下命令：

```kt
adb shell service list

```

命令的输出将显示为列表，如下所示：

![ADB Dumpsys](img/image00334.jpeg)

冒号前的服务名称是我们将传递给 dumpsys 的参数。使用前面截图中的服务编号七（`iphonesubinfo`）的有效 dumpsys 命令如下所示：

```kt
adb shell dumpsys iphonesubinfo

```

在下面的截图中，我们看到`iphonesubinfo`服务的输出包括设备的 IMEI：

![ADB Dumpsys](img/image00335.jpeg)

有许多法医学上有趣的 dumpsys 服务；以下是一些示例。由于 dumpsys 服务可能因操作系统版本和设备而异，此列表并不包括所有内容，仅旨在展示 dumpsys 对法医检查员的有用性：

+   iphonesubinfo

+   batterystats

+   procstats

+   用户

+   appops

+   Wi-Fi

+   通知

## Dumpsys batterystats

Batterystats 用于显示正在运行的应用程序的使用情况。根据使用的应用程序数量，其输出可能非常冗长。在下面的截图中，输出被重定向到文件，因为它无法适应 Windows 命令行：

![Dumpsys batterystats](img/image00336.jpeg)

这显示了 Google Chrome 的网络使用情况。这些信息可以用来显示该应用程序最近是否被使用，即使 Chrome 在隐身模式下使用并且在其他地方没有留下法医证据，这些信息仍将存在。

### 注意

**唤醒锁**部分对于检测恶意软件非常有用。唤醒锁是一种使设备保持唤醒状态（不进入睡眠模式）的方法，表明应用程序试图在后台保持运行。

## Dumpsys procstats

**procstats**是一种显示运行应用程序的处理器使用情况的服务。与 batterystats 类似，它是可以用来显示应用程序最近在设备上使用的另一种方法，如下截图所示：

![Dumpsys procstats](img/image00337.jpeg)

## Dumpsys user

从 Android Jelly Bean 开始，Google 为平板设备添加了多用户支持。随着 Lollipop 的发布，Google 将此支持扩展到手机。长期以来，数字取证中最具挑战性的问题之一是在执行可疑操作时证明谁在使用设备；“谁在键盘后面？”

在用户服务上运行 dumpsys 将显示所有用户的最后登录信息：

![Dumpsys user](img/image00338.jpeg)

由于一次只能有一个用户登录，查看最近登录的用户将确定设备上当前正在使用的帐户。

## Dumpsys App Ops

**App Ops**可能是最有趣的 dumpsys 服务。术语 App Ops 通常用于指代应用程序可以访问的权限。在较早版本的 Android 中，有传言称 Google 将包括用户可以撤消应用程序的特定权限的功能。尽管这从未实现，但这项服务至少仍然存在，并显示了应用程序上次使用每个可以访问的权限的时间。以下是来自 Google Chrome 的另一个示例：

![Dumpsys App Ops](img/image00339.jpeg)

在上面的输出中，我们可以看到大约在 App Ops 使用 dumpsys 之前 1 小时 7 分钟，Chrome 使用了`TAKE_AUDIO_FOCUS`权限，后来使用了`AUDIO_MEDIA_VOLUME`。这表明 Chrome 用于听音乐的时间和内容。

一个更有趣的例子是以下的手机应用程序：

![Dumpsys App Ops](img/image00340.jpeg)

`44`分钟前，用户使用了电话应用程序并需要`READ_CONTACTS`权限，然后立即使用了`WRITE_CALL_LOG`权限。我们可以推断用户在`44`分钟前打了电话；即使他们之后从记录中删除了通话。

## Dumpsys Wi-Fi

**Wi-Fi**服务将显示已保存连接的所有 SSID 的列表。例如，这可能对于显示用户曾经在某个位置非常有用。更详细的 Wi-Fi 信息也可以在文件系统上找到，但需要 root 权限才能查看。使用 dumpsys，我们可以在不需要 root 权限的情况下访问这些数据：

![Dumpsys Wi-Fi](img/image00341.jpeg)

## Dumpsys notification

**通知**服务将提供有关当前活动通知的信息。这对于记录设备被扣押时的状态，或者识别显示特定通知的应用程序非常有用。每个通知可能相当大，并包含大量信息，其中只有一些可能有用。以下示例显示了来自 Gmail 应用程序的一封新邮件，其中包括主题（`这是一封测试邮件`）和正文（`查看测试通知`）：

![Dumpsys notification](img/image00342.jpeg)

## Dumpsys 结论

在没有服务名称的情况下运行 dumpsys 命令将在所有可用的服务上运行 dumpsys。但是，输出将非常庞大，应将其重定向到文本文件中。在大多数平台上，执行此操作的命令将是：

```kt
adb shell dumpsys > dumpsys.txt

```

这将把输出写入到当前工作目录中的 dumpsys.txt 文件中。然后可以搜索输出，或者运行解析脚本来提取已知的相关字段。

Dumpsys 是一个非常强大的工具，可以用来显示设备上无法在其他地方获取的信息。我们建议在扣押 Android 设备时，在关机之前运行 dumpsys。这将保存各种各样的信息，以后可能会有用，并且不需要 root 权限。

# 绕过 Android 锁屏

锁屏是安卓取证调查中最具挑战性的方面。调查的整个过程经常取决于取证人员获取锁定设备的能力。虽然有绕过锁屏的方法，但这很大程度上取决于操作系统版本、设备设置和取证人员的技术能力。并没有一种可以在每台设备上都有效的神奇解决方案。商业取证工具如 Cellebrite 和 XRY 具有相当强大的绕过能力，但并非万无一失。本章将展示取证人员如何利用免费工具和方法提高绕过锁定设备的成功率。

### 注意

取证人员不应尝试在设备上猜测图案/PIN/密码。许多制造商实施了一个设置，将在一定次数的失败尝试后擦除设备。许多还允许用户降低这个次数。

## 锁屏类型

有许多用于保护设备的方法，以及绕过每种方法的方法都有所不同：

+   无/滑动

+   图案

+   PIN

+   密码

+   智能锁

+   可信面部

+   可信位置

+   可信设备

可能存在其他安全选项；由于安卓是开源的，可能性只受开发者想象力的限制。这些是谷歌发布的安卓棒棒糖原始版本中提供的选项。大多数供应商使用的安全选项通常将这些原始选项作为备用选项，以防用户无法使用他们独特的选项登录。首次使用该设置的版本也指的是原始安卓；各种制造商可能更早地实施了它们。

### 无/滑动锁屏

**滑动解锁**屏幕是大多数安卓设备的默认设置。它提供了零级安全，并且可以通过在屏幕上滑动手指来绕过。

### 图案锁屏

图案锁屏是标志性的安卓安全方法。经常被称为**滑动码**和类似的名称，这些要求用户用手指在设备上划出一个图案。这种锁的常见绕过方法是**指纹攻击**，寻找用户手指在屏幕上留下的图案。

### 密码/PIN 锁屏

熟悉苹果 iOS 的用户会认识到这个选项。它要求用户输入密码或 PIN 码才能解锁设备。它们被归为一类，因为在取证上它们是相同的：它们以相同的方式存储密码。

### 智能锁

**智能锁**是安卓棒棒糖中引入的术语，尽管面部解锁选项以前就已经存在。它们需要特定条件才能解锁设备：用户的脸必须被识别，用户必须在已知位置，或者附近必须有特定的其他设备。

#### 可信面部

面部解锁的工作原理就像它的名字一样：它使用面部识别来确定用户是否已被设置为可信用户。旧版本的面部锁很容易被信任用户的照片欺骗，尽管新版本可能要求用户眨眼才能解锁设备。

#### 可信位置

可信位置在安卓棒棒糖中可用，通常被称为**地理围栏**。如果用户在被标记为可信的位置（比如家或工作地点）上，设备将不会锁定。用户无需输入，但必须启用 GPS。

#### 可信设备

可信设备在安卓棒棒糖中可用，通过蓝牙工作；如果附近有设置为可信设备的设备，锁屏将被禁用。这可以与智能手表、通过蓝牙配对的车辆、蓝牙耳机或任何其他蓝牙设备一起使用。

### 注意

所有智能锁选项都需要图案/PIN/密码作为备用安全方法。这意味着我们只需要学会如何绕过图案/PIN/密码才能破解所有的安全选项。

## 绕过信息

在所有情况下，绕过锁屏将需要从设备中检索文件。图案锁存储为哈希值，位于`/data/system/gesture.key`，PIN/密码锁存储为哈希值，位于`/data/system/password.key`。此外，password.key 哈希值是加盐的；盐值存储在 Android 4.4 之前的设备上的`/data/data/com.android.providers.settings/databases/settings.db`，以及运行 Android 4.4 及更高版本的设备上的`/data/system/locksettings.db`。

如果设备被锁定，调查员应该如何访问这些文件？同样，并没有一个可以每次都有效的魔法解决方案，但有一些选项，如下所示：

+   ADB

+   需要 root 权限

+   需要**USB 调试**

+   需要**安全 USB 调试**配对（取决于操作系统版本）

+   引导到自定义恢复模式

+   不需要 root 权限（通过恢复映像将获得 root 权限）

+   无需 USB 调试（通过 fastboot 完成）

+   不需要**安全 USB 调试**（这完全被绕过）

+   需要解锁的引导加载程序

+   JTAG/Chip-off

+   非常先进

+   不需要任何特定的设备设置或选项

需要拉取的文件以破解 Android 4.4 之前的设备上的 PIN/密码为：

+   /`data/system/password.key`

+   `/data/data/com.android.providers.settings/databases/settings.db`

需要拉取的文件以破解运行 Android 4.4 及更高版本设备上的 PIN/密码为：

+   `/data/system/password.key`

+   `/data/system/locksettings.db`

只需要拉取一个文件即可破解所有版本的 Android 上的图案锁：

+   `/data/system/gesture.key`

### 提示

并不总是需要实际破解 PIN 或密码。它们也可以通过简单地覆盖或删除文件来绕过。然而，这会改变原始证据，可能在您的司法管辖区内并不具备法医学有效性。

请注意，下面的破解部分不适用于棒棒糖设备。图案锁不再是未加盐的，截至撰写时，尚未发布有关如何恢复盐的信息。然而，仍然可以通过删除相关文件来绕过锁屏。

存在许多可以自动绕过锁屏的工具；然而，在本章中，我们将展示手动过程，以解释这些工具在后台的操作。一款适用于执法人员的好工具可以在[`andriller.com/`](https://andriller.com/)找到。

# 破解 Android 图案锁

现在我们有了`gesture.key`，其中包含图案锁信息，让我们来看看文件内容：

![破解 Android 图案锁](img/image00343.jpeg)

在十六进制编辑器中的 gesture.key 的内容

文件的十六进制内容是滑动图案的未加盐 SHA-1 哈希。由于可能的图案数量有限（因为每个数字只能使用一次，最小为四位数，最大为九位数），破解此哈希的最简单方法是使用字典攻击。调查员可以创建一个包含每种可能图案的字典，但重新发明轮子并不总是必要的。总部位于英国的 CCL Forensics 提供了一个免费的 Python 脚本来创建哈希字典。它可以在[`www.cclgroupltd.com/product/android-pattern-lock-scripts/`](http://www.cclgroupltd.com/product/android-pattern-lock-scripts/)下载。

文件是`GenerateAndroidGestureRainbowTable.py`。要运行它，调查员的系统必须安装 Python 3。Python 3 可以在[`www.python.org/downloads/`](https://www.python.org/downloads/)下载。许多取证工具提供 Python 支持或自己使用它，因此调查员可能已经安装了它。要执行该文件，只需导航到包含它的目录并运行：

![破解 Android 图案锁](img/image00344.jpeg)

脚本可能需要一段时间才能运行，可能在 20 到 30 分钟之间。完成后，应该会在与`GenerateAndroidGestureRainbowTable.py`脚本相同的目录中有一个名为`AndroidLockScreenRainbow.sqlite`的文件。

现在我们有一个包含每个可能的安卓图案哈希的数据库，我们只需要在`gesture.key`文件中查找我们找到的哈希。这可以通过 SQLite 查看器或者 SQL 命令手动完成。然而，CCL Forensics 还提供了`Android_GestureFinder.py`，这是一个脚本，将在之前创建的数据库中查找哈希。

不幸的是，这个免费脚本并不完全符合我们的目的。它是用来查看物理转储二进制并找到锁定屏幕图案的；我们已经有包含图案的文件了。为了使脚本正常工作，我们需要修改代码。`Android_GestureFinder.py`需要在某种代码友好的编辑器中打开；Notepad++、Sublime Text 或 Python IDLE GUI 都可以。以下截图来自 Sublime Text。复制文件，打开原始文件，找到第 85 行，其中写着：

```kt
if regex.match(chunk) is not None:
```

这一行需要被注释掉。只需在行的开头放一个`#`号：

```kt
#if regex.match(chunk) is not None:
```

由于 Python 的格式，我们现在需要取消注释后面语句的缩进。第 86、89、91 和 92 行需要向左移动，使它们与我们注释掉的语句对齐。最后，第 94 行需要向左移动四个空格，使其与上面的行相齐。最终的代码应该是这样的：

![破解安卓图案锁](img/image00345.jpeg)

Android_GestureFinder.py 的最终代码

请注意，第 85 行现在以`#`开头，第 86、89、91 和 92 行与第 85 行对齐，第 94 行向右缩进一个制表符（或者从原来的位置向左移动四个空格）。

现在代码已经准备好运行我们的文件；保存对`Android_GestureFinder.py`的更改。确保`AndroidLockScreenRainbow.sqlite`和`gesture.key`文件与`Android_GestureFinder.py`在同一个目录中，并运行以下脚本：

![破解安卓图案锁](img/image00346.jpeg)

输出应该非常快，因为它只是在哈希数据库中进行简单的查找。`Offset`是负数，因为我们使用脚本对单个文件进行了操作；如果指向一个二进制物理转储，它将显示在 blob 中锁定屏幕哈希的偏移量。`Hash`列显示找到的哈希值，`Pattern`是相应的锁定屏幕图案：

![破解安卓图案锁](img/image00347.jpeg)

锁定屏幕编号

在这个例子中，图案将从左上角的`0`开始，经过中间的**4**，触摸右下角的**8**，然后穿过底部中间的**7**，最后在左下角的**6**结束。现在可以在设备上使用这个图案来绕过锁定屏幕。

如果脚本遇到错误，文件很可能没有正确修改。以下结果很可能表明脚本没有正确修改；也许运行的是文件的副本而不是修改后的版本：

![破解安卓图案锁](img/image00348.jpeg)

要解决这个错误，验证脚本是否按照上面显示的方式进行了修改。以下错误表明缩进不正确：

![破解安卓图案锁](img/image00349.jpeg)

要解决这个错误，导航到指定的行（在上面的例子中是第 86 行），确保对齐方式与前面修改后的代码一样。

如果错误无法解决，或者修改脚本对于检查员来说太困难，哈希值总是可以在哈希数据库中手动查找。一个优秀的免费 SQL 查看器，DB Browser for SQLite，可以在[`sourceforge.net/projects/sqlitebrowser/`](http://sourceforge.net/projects/sqlitebrowser/)找到。

使用 DB Browser for SQLite 打开 AndroidLockScreenRainbow.sqlite，然后选择浏览数据选项卡。然后，只需将在 gesture.key 中找到的哈希值输入到哈希列的搜索字段中。请注意，数据库中的字符是小写的；搜索字段区分大小写：

![破解 Android 图案锁](img/image00350.jpeg)

在 SQLite 浏览器中的 AndroidLockScreenRainbow.sqlite 文件的内容

结果与运行脚本的结果相同。

## 破解 Android PIN/密码

要破解 PIN/密码锁，我们需要查看之前提取的文件的内容。`Password.key`与`gesture.key`非常相似；它包含密码的哈希值，如下面的截图所示：

![破解 Android PIN/密码](img/image00351.jpeg)

在十六进制编辑器中的 password.key 的内容

然而，这次哈希是有盐的。为了有机会破解它，必须恢复盐。如上所述，其位置将取决于设备运行的 Android 版本。如果设备运行的是 4.3 或更低版本，则将位于`secure`表中的`settings.db`文件中。在 4.4 或更高版本中，它将位于`locksettings`表中的`locksettings.db`中。下面的示例显示了`locksettings.db`，但该过程对两者都是相同的。

在数据库文件中，我们需要找到`lockscreen.password_salt`键。这可以在 SQL 浏览器中完成，或者只需在十六进制编辑器中打开文件并搜索。盐值如下所示：

![破解 Android PIN/密码](img/image00352.jpeg)

在十六进制编辑器中的 locksettings.db 的内容

对于图案锁，我们能够使用字典攻击来快速破解图案，因为可能性相对较少。使用盐哈希，字典攻击是不可行的，所以我们将简单地进行暴力破解。

CCL Forensics 再次提供了一个有用的 Python 脚本。也可以使用其他破解工具，如 hashcat。CCL Forensics PIN/密码工具可以免费下载[`www.cclgroupltd.com/product/android-pin-password-lock-tool/`](http://www.cclgroupltd.com/product/android-pin-password-lock-tool/)。将下载两个文件，`BruteForceAndroidPin.py`和`RecoverAndroidPIN.py`。`RecoverAndroidPIN.py`用于在物理图像中定位必要的文件；我们不需要它。

`BruteForceAndroidPIN.py`的格式是：

```kt
python BruteForceAndroidPIN.py <hash> <salt> <max code length (4-16)> t

```

结尾的`t`参数用于指示哈希是密码的哈希；对于破解 PIN 是不需要的，只会增加运行时间。

上面显示的哈希值来自 PIN，所以我们只需要填写`<hash>`、`<salt>`和`<max code length>`字段：

![破解 Android PIN/密码](img/image00353.jpeg)

此示例中的 PIN 为`2587`，如输出所示。这个 PIN 破解不到一秒，但更长的 PIN 甚至短密码可能需要更长的时间。

# Android SIM 卡提取

传统上，SIM 卡用于在设备之间传输数据。过去，SIM 卡用于存储许多不同类型的数据，例如：

+   用户数据

+   联系人

+   短信

+   拨打电话

+   网络数据

+   **集成电路卡标识符**（**ICCID**）：SIM 卡的序列号

+   **国际移动用户识别码**（**IMSI**）：将 SIM 卡与特定用户账户绑定的标识符

+   **MSISDN**：分配给 SIM 卡的电话号码

+   **位置区域标识**（**LAI**）：标识用户所在的小区

+   **认证密钥**（**Ki**）：用于在移动网络上进行身份验证

+   其他各种特定于网络的信息

随着设备存储容量、SD 卡和云备份的增加，将数据存储在 SIM 卡上的必要性已经减少。因此，大多数现代智能手机通常不会在 SIM 卡上存储太多用户数据，如果有的话。上述所有网络数据仍然存储在 SIM 卡上，因为 SIM 卡是连接所有现代（4G）蜂窝网络的必要条件。

与所有 Android 设备一样，虽然没有明确规定用户数据不能存储在 SIM 卡上，但默认情况下并不会发生。个别设备制造商可以轻松决定将用户数据写入 SIM 卡，个别用户也可以下载应用程序来提供该功能。这意味着在取证调查期间应该始终检查设备的 SIM 卡。这是一个非常快速的过程，不应该被忽视。

## 获取 SIM 卡数据

SIM 卡应该始终从设备中取出并单独检查。虽然有些工具声称可以通过设备接口读取 SIM 卡，但这可能无法恢复已删除的数据或 SIM 卡上的所有数据；取证人员确保已获取所有数据的唯一方法是使用经过测试和验证的独立 SIM 卡读卡器读取 SIM 卡。

SIM 的位置会因设备而异，但通常存放在电池下方或设备侧面的托盘中。一旦 SIM 卡被取出，就应该放入 SIM 卡读卡器中。市场上有数百种 SIM 卡读卡器可供选择，所有主要的移动取证工具都配备了一个可与其软件配合使用的读卡器。通常，取证工具也会支持第三方 SIM 读卡器。

令人惊讶的是，缺乏彻底的免费 SIM 卡读取软件。任何使用的软件都应该在实际取证调查之前经过测试和验证，以确保在 SIM 卡上填充了已知数据。此外，请记住，许多免费软件适用于旧的 2G/3G SIM 卡，但可能无法在现代 4G SIM 卡上正常工作。我们使用了 Mobiledit! Lite，Mobiledit!的免费版本，用于以下截图。它可以在[`www.mobiledit.com/downloads`](http://www.mobiledit.com/downloads)上下载。

以下截图显示了从运行版本为 4.4.4 的 Android 手机中提取的样本 4G SIM 卡；请注意，尽管 SIM 卡在过去一年中一直在使用，但并未获取到可以被视为用户数据的内容，尽管 ICCID、IMSI 和 MSISDN（自己的电话号码）等字段可能对传票/令状或调查的其他方面有用。

![获取 SIM 卡数据](img/image00354.jpeg)

SIM 卡提取概述

以下截图突出显示了 SIM 卡上的短信：

![获取 SIM 卡数据](img/image00355.jpeg)

以下图片突出显示了 SIM 卡的电话簿：

![获取 SIM 卡数据](img/image00356.jpeg)

以下图片突出显示了 SIM 卡的电话号码（也称为 MSISDN）：

![获取 SIM 卡数据](img/image00357.jpeg)

## SIM 卡安全

由于 SIM 卡符合已建立的国际标准，所有 SIM 卡都提供相同的安全功能：4 到 8 位的 PIN 码。通常，必须通过设备菜单设置此 PIN 码。在 Android 设备上，此设置位于**设置** | **安全** | **设置 SIM 卡锁**。SIM PIN 完全独立于任何锁屏安全设置，只有在设备启动时才需要输入。SIM PIN 仅保护 SIM 卡上的用户数据；即使 SIM 卡被 PIN 锁定，所有网络信息仍然是可恢复的。

SIM 卡将允许三次尝试输入 PIN 码。如果其中一次尝试正确，计数器将重置。另一方面，如果所有这些尝试都不正确，SIM 卡将进入**个人解锁密钥**（**PUK**）模式。PUK 是由运营商分配的 8 位数字，通常在购买 SIM 卡时的文件中找到。使用任何商用取证软件都无法绕过 PUK；因此，检查员不应尝试在设备上输入 PIN 码，因为设备不会指示在激活 PUK 之前还有多少次尝试。检查员可能会无意中将 SIM 卡锁定，无法访问设备。然而，取证工具将显示在激活 PUK 之前还有多少次尝试，如前面的屏幕截图所示。

### 注意

SIM 卡的通用载体默认的 PIN 码是 0000 和 1234。如果在激活 PUK 之前还有 3 次尝试，检查员可以成功地使用这些默认值解锁 SIM 卡。

运营商经常在发放 SIM 卡时保留 PUK 密钥。这些密钥可以通过发给运营商的传票或令状获得。

### SIM 卡克隆

SIM 卡的 PIN 本身几乎没有提供额外的安全性，并且可以很容易地通过 SIM 卡克隆来绕过。SIM 卡克隆是几乎所有商用移动取证软件提供的功能，尽管克隆这个术语有些误导。在移动取证的情况下，SIM 卡克隆是将锁定的 SIM 卡上的网络数据复制到一个没有激活 PIN 的取证无菌 SIM 卡上的过程。手机将根据这些网络数据（通常是 ICCID 和 IMSI）识别克隆的 SIM 卡，并认为它是之前插入的相同 SIM 卡，但这次没有 SIM PIN。这个克隆的 SIM 卡也无法访问蜂窝网络，这使它成为类似飞行模式的有效解决方案。因此，SIM 卡克隆将允许检查员访问设备，但原始 SIM 卡上的用户数据仍然无法访问，因为它仍然受到 PIN 的保护。

我们不知道有任何免费软件可以进行取证 SIM 卡克隆。然而，几乎所有商用移动取证工具都支持这一功能。这些工具通常包括 SIM 卡读卡器、执行克隆的软件，以及多张空白 SIM 卡用于克隆过程。

# Android 棒棒糖的问题和机会

正如本章中多次提到的那样，最近发布的 Android 棒棒糖 5.0 版本引入了许多强大的安全功能，这当然给取证检查员带来了复杂性。最初宣布 Android 棒棒糖设备将默认启用全盘加密，然而，由于许多设备上的性能问题，谷歌后来撤回了这一要求。谷歌只是强烈建议用户在首次创建帐户时启用全盘加密。谷歌还暗示这将成为未来操作系统版本的要求，更多信息可以在[`static.googleusercontent.com/media/source.android.com/en/us/compatibility/android-cdd.pdf`](http://static.googleusercontent.com/media/source.android.com/en/us/compatibility/android-cdd.pdf)的第 9.9 节找到。

启用完整磁盘加密的设备几乎不可能绕过锁定的设备，因为即使可以恢复密钥文件，它们也会被加密；尽管商业工具制造商最终肯定会赶上。在撰写本文时，没有已知的方法可以绕过锁定的、加密的棒棒糖设备，除非它已启用**USB 调试**并且先前记住了计算机的 RSA 密钥以绕过**安全 USB 调试**。在这种情况下，可以从嫌疑人的计算机中拉取`adbkey`和`adbkey.pub`文件，并将其放在检查机器上；然后设备将认为它正在与已知的、批准的计算机通信。`adbkey`和`adbkey.pub`文件可以在 Windows 计算机的`C:\Users\<username>\.android`和苹果计算机的`/Users/<username>/.android`中找到。

对于进行棒棒糖取证的检查员来说，有一个重要的优势：本章前面提到的智能锁。智能锁允许用户设置条件，如果满足条件，设备将无需输入密码即可解锁。如果在启用的受信任位置进行检查，检查员将不需要绕过锁定。如果在检查设备时附近有一个已启用的受信任设备，情况也是如此。但是，设备上没有任何指示表明正在使用受信任的设备；设备看起来只是没有锁屏。因此，从现场保护所有数字证据变得更加关键。以前可能被忽视的设备，如蓝牙耳机，可能会成为让检查员绕过锁定设备的关键。越来越普遍的是设备与车辆配对，因此检查员可能不得不在嫌疑人的车辆中进行提取！棒棒糖的额外安全性意味着检查员可能需要在取证过程中变得更有创造力。

Android 棒棒糖还为所有设备带来了多用户支持，这在以前仅限于平板电脑。在具有多个帐户的设备上，所有用户的数据仍然位于`/data`分区中，但位于稍有不同的位置。如果在设备上设置了多个帐户，则每个帐户的应用数据目录可以在`/data/user`中找到。每个用户都将被分配一个唯一的编号；`0`是设备上设置的第一个帐户。`/data/user/0`目录实际上是一个符号链接到`/data/data`。第二个帐户位于`10`目录中，该目录直接包含了第二个用户的所有应用程序数据。每添加一个用户，都会存储在以`10`递增的目录中；即第三个用户在`/data/user/20`中，第四个用户在`/data/user/30`中，依此类推。

# 摘要

本章涵盖了与 Android 设备的逻辑提取相关的许多主题。简而言之，各种方法及其要求如下：

| 方法 | 要求 |
| --- | --- |
| ADB 拉取 |

+   **USB 调试**已启用

+   4.2.2+上绕过**安全 USB 调试**

+   获取用户数据的根访问权限

|

| 从恢复模式下的 ADB 拉取 |
| --- |

+   必须是自定义恢复以启用 ADB 访问

+   获取用户数据的根访问权限

|

| 从自定义恢复映像中启动的 Fastboot |
| --- |

+   解锁的引导加载程序

+   设备的引导镜像

|

| ADB 备份 |
| --- |

+   **USB 调试**已启用

+   4.2.2+上绕过**安全 USB 调试**

+   必须在运行设备上完成（而不是恢复模式）

|

| ADB dumpsys |
| --- |

+   **USB 调试**已启用

+   4.2.2+上绕过**安全 USB 调试**

+   必须在运行设备上完成（而不是恢复模式）

|

| SIM 卡提取 |
| --- |

+   无，应独立于设备完成

|

此外，有价值的用户数据可以从 SD 卡中恢复，这将在第五章中进行介绍，*从 Android 设备物理提取数据*。

如果屏幕被锁定，检查员可以使用上述列出的方法提取关键文件并破解它们，以绕过锁定。

本章节有大量的数据。为了帮助简化它，下面展示了一个建议的*最佳实践*流程图：

.

![摘要](img/image00358.jpeg)

安卓取证流程图