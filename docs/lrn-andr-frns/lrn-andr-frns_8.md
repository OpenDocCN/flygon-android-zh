# 第八章：Android 取证工具概述

本章概述了免费和开源的 Android 取证工具，并将向您展示如何在常见的调查场景中使用这些工具。在本章结束时，读者应该熟悉以下工具：

+   ViaExtract

+   Autopsy

+   ViaLab

# ViaExtract

ViaExtract 是由 NowSecure（以前称为 ViaForensics）创建的逻辑和物理提取工具。免费版本提供逻辑获取（包括备份），而付费版本则添加了物理提取。它在 NowSecure 的 Santoku Linux 发行版中以虚拟机文件（VMWare 或 Virtual Box 格式）的形式免费分发。在使用免费版本时需要活动的互联网连接。下载和完整的功能列表可以在[`www.nowsecure.com/forensics/community/`](https://www.nowsecure.com/forensics/community/)找到。需要注册。

在 ViaExtract 虚拟机的桌面上可以找到注册工具和启动 ViaExtract 的图标：

![ViaExtract](img/image00415.jpeg)

在启动 ViaExtract 之前，请确保要检查的设备通过 USB 连接到计算机。这将确保设备被检测到。设备还需要通电。请注意适当的网络隔离措施，如第一章中所讨论的*介绍 Android 取证*。在下面的示例中，我们将检查运行 Android Lollipop 5.1 的 LG Nexus 5。请注意，这在下面屏幕截图的左下角显示。

然后，按照以下步骤进行：

1.  在左上角点击**新建**按钮将弹出**创建新项目**对话框：![ViaExtract](img/image00416.jpeg)

1.  选择**前进**将弹出**设备信息**选项卡，这是我们将开始提取的地方。支持的提取列表如下屏幕截图所示。在这种情况下，我们的两个选项是**Android 备份**或**Android 逻辑**：![ViaExtract](img/image00417.jpeg)

## 使用 ViaExtract 进行备份提取

要执行备份提取，请按照以下步骤进行：

1.  单击**提取**（如前面的屏幕截图所示）将显示**选择提取类型**对话框。我们首先从**类型**下拉菜单中选择备份提取：![使用 ViaExtract 进行备份提取](img/image00418.jpeg)

1.  填写所有字段后，**确定**按钮将变为可用。选择**确定**将显示在设备上接受该过程的说明。但是，此步骤目前在设备上不可用：![使用 ViaExtract 进行备份提取](img/image00419.jpeg)

1.  在**Android 备份：允许备份**屏幕上选择**前进**后，ViaExtract 将为您提供尝试恢复已删除的 SQLite 数据的选项。尽管这只是逻辑提取，但可能会成功，因为 SQLite 数据库存储数据的方式已在本书中详细讨论过。![使用 ViaExtract 进行备份提取](img/image00420.jpeg)

1.  在**Android 备份：SQLite 恢复**屏幕上选择**前进**将开始备份。此时，备份将需要在设备上接受，如前面的**Android：允许备份**屏幕截图所示：![使用 ViaExtract 进行备份提取](img/image00421.jpeg)

1.  点击**关闭**后，备份现在应该在**任务**选项卡中显示进度，如下面的屏幕截图所示：

![使用 ViaExtract 进行备份提取](img/image00422.jpeg)

## 使用 ViaExtract 进行逻辑提取

要开始逻辑提取，请按照以下步骤进行：

1.  从**设备信息**选项卡中选择**提取**，就像我们之前完成的备份提取一样。但是，这次从**类型**下拉菜单中选择**Android 逻辑**。这将启动逻辑提取向导，它首先指出需要我们在设备上安装一个应用程序：![使用 ViaExtract 进行逻辑提取](img/image00423.jpeg)

1.  选择**前进**将进入**逻辑提取：选项**菜单。在这里，可以忽略某些文件类型以加快提取过程：![使用 ViaExtract 进行逻辑提取](img/image00424.jpeg)

1.  此时，ViaExtract 应用程序将被推送到设备上，并且可能需要在设备上进一步操作。我们将在以下弹出消息上选择**DECLINE**：![使用 ViaExtract 进行逻辑提取](img/image00425.jpeg)

1.  在拒绝弹出窗口后，可以看到应用程序正在设备上运行：![使用 ViaExtract 进行逻辑提取](img/image00426.jpeg)

1.  回到电脑上，将显示以下消息：![使用 ViaExtract 进行逻辑提取](img/image00427.jpeg)

1.  进度将再次在左上角的**任务**选项卡中可见，如下所示：

![使用 ViaExtract 进行逻辑提取](img/image00428.jpeg)

## 在 ViaExtract 中检查数据

一旦提取完成，数据可以在左上角的**项目**选项卡中查看：

![在 ViaExtract 中检查数据](img/image00429.jpeg)

要查看内容，只需点击它，它将显示在右侧。这是在逻辑提取中找到的书签的示例：

![在 ViaExtract 中检查数据](img/image00430.jpeg)

当检查备份时，流程是一样的。这里是在《第七章》*Android 应用程序的取证分析*中分析的`Tango tc.db`文件的摘录：

在 ViaExtract 中检查数据

## ViaExtract 中的其他工具

ViaExtract 还可以尝试对设备进行 root 和绕过密码。从**设备信息**选项卡中点击**Root**按钮将启动 root 向导。只需按照弹出消息进行操作即可对设备进行 root。我们在我们的测试设备 Nexus 5、Moto X（2013 年型号）和 HTC Droid DNA 上没有成功。

从右上角的工具菜单中启动解锁屏幕向导。然后，选择**解锁屏幕**。这将向设备推送一个应用程序（需要启用 USB 调试和通过 RSA 身份验证），该应用程序将删除锁定屏幕。这是一个有用的工具，因为与[第四章]中显示的手动方法不同，*从 Android 设备逻辑提取数据*，它不需要 root 访问权限。它在我们的 Nexus 5 和 HTC Droid DNA 上失败了，但在我们的 Moto X 上完美地工作。

# Autopsy

Autopsy 是由 Brian Carrier 最初开发的免费开源分析工具。Autopsy 最初是基于 Linux 的 SleuthKit 工具集的图形用户界面，但最新版本（版本 3）是为 Windows 构建的独立工具。Autopsy 可以在[`www.sleuthkit.org/autopsy/`](http://www.sleuthkit.org/autopsy/)上下载。

Autopsy 并不打算执行移动设备的获取，但可以分析最常见的 Android 文件系统（如 YAFFS 和 ext）。在这个例子中，我们将加载从 HTC Droid DNA 通过 dd 获得的完整物理镜像，如《第五章》*从 Android 设备物理提取数据*中所述。

## 在 Autopsy 中创建一个案例

打开 Autopsy 时，用户将被提示选择**创建新案例**、**打开最近的案例**或**打开现有的案例**：

![在 Autopsy 中创建一个案例](img/image00432.jpeg)

我们将创建一个新案例。按照以下步骤进行：

1.  填写“案例名称”字段后，“下一步”按钮将变为可用：![在 Autopsy 中创建案例](img/image00433.jpeg)

1.  在下一个屏幕上，可以输入可选的“案例编号”和“审查员”：![在 Autopsy 中创建案例](img/image00434.jpeg)

1.  选择“完成”将显示“输入数据源信息”屏幕。单击“浏览”将允许用户选择要加载的图像文件：![在 Autopsy 中创建案例](img/image00435.jpeg)

1.  选择图像文件后，可以单击“下一步”按钮以进入“配置摄取模块向导”：![在 Autopsy 中创建案例](img/image00436.jpeg)

摄取模块是内置在 Autopsy 中的工具，可以在案例启动时或之后的任何时候运行。此版本的 Autopsy 中的默认模块如下：

+   最近活动：这会提取最近的用户活动，如网络浏览、最近使用的文档和已安装的程序。

+   哈希查找：这会使用提供的哈希数据库识别已知和显著的文件，如标准 NSRL 数据库。它还允许导入自定义哈希数据库。

+   文件类型识别：这会基于二进制签名匹配文件类型。

+   存档提取器：这会提取存档文件（.zip、.rar、.arj、.7z、.gzip、.bzip2、.tar）。它会自动提取这些文件类型，并将它们的内容放入目录树中。

+   EXIF 解析器：这会摄取 JPEG 文件并检索它们的 EXIF 元数据。

+   关键词搜索：这会使用关键词和正则表达式在列表中执行文件索引和定期搜索。它允许加载自定义关键词/列表。

+   电子邮件解析器：此模块检测和解析 mbox 和 pst/ost 文件，并在黑板上填充电子邮件工件。

+   扩展名不匹配检测器：这些是基于文件类型的非标准扩展名的标志文件。

+   E01 验证器：这会验证 E01 文件的完整性。

+   Android 分析器：这会提取 Android 系统和第三方应用程序数据。

+   有趣文件标识符：这会识别有趣的项目，如有趣的项目规则集所定义的那样。

### 注意

许多这些模块对于 Android 设备来说是不需要的（例如 E01 验证器和电子邮件解析器）。只选择有用的模块将加快摄取时间。此外，请注意，单击模块可能会带来更多选项，如前面的屏幕截图所示。

1.  单击“下一步”将加载数据源并开始摄取过程。遇到的任何错误都将被记录：![在 Autopsy 中创建案例](img/image00437.jpeg)

1.  选择“完成”将带领审查员到摄取案例的主要屏幕进行分析：![在 Autopsy 中创建案例](img/image00438.jpeg)

## 在 Autopsy 中分析数据

尽管案例仍在加载并且摄取模块正在运行（如前一个屏幕截图右下角的进度条所示），审查员可以开始分析案例。展开左上角的图像文件将显示 Autopsy 识别的分区/卷：

![在 Autopsy 中分析数据](img/image00439.jpeg)

Autopsy 在我们的设备上识别了 65 个分区，其中绝大多数是未分配的。要找到数据分区（因为我们知道我们感兴趣的绝大多数数据存储在这里），我们可以简单地展开已分配的分区，直到找到一个看起来像数据分区的分区：

![在 Autopsy 中分析数据](img/image00440.jpeg)

在我们的图像中，卷 124 是数据分区。我们可以看到它有一个应用程序目录（存储 APK 文件的位置）、一个数据目录（存储应用程序数据的位置）和一个媒体目录（SD 卡的符号链接位置）。

展开数据目录将显示我们应该从第七章中记住的信息，即*Android 应用程序的取证分析*。这也可以在以下屏幕截图中看到：

![在 Autopsy 中分析数据](img/image00441.jpeg)

我们立即可以看到`com.android.providers.telephony`和`userdictionary`以及`com.facebook.katana`。如何分析这些应用程序在第七章*Android 应用程序取证分析*中有所涵盖；这是如何使用 Autopsy 访问相关文件的方法。例如，展开`com.android.providers.telephony`将显示分析短信和彩信数据所需的`mmssms.db`文件。

![在 Autopsy 中分析数据](img/image00442.jpeg)

右键单击文件将允许用户选择**提取文件**或**在外部查看器中打开**以进行进一步分析：

![在 Autopsy 中分析数据](img/image00443.jpeg)

现在，让我们看看 Autopsy 的其他功能。在屏幕左侧展开**视图**部分将显示一些使用的摄入模块的结果，如下所示：

![在 Autopsy 中分析数据](img/image00444.jpeg)

**文件类型**视图显示了由`文件类型识别`模块识别的文件。**最近文件**显示了`最近活动`模块的结果。在这种情况下，设备似乎在 6 天内没有使用，然后在**最后一天**使用了。查看这里识别的文件可以显示用户在那段时间内的活动。请注意红色的叉号，表示其中一些文件已被删除，但被 Autopsy 恢复了：

![在 Autopsy 中分析数据](img/image00445.jpeg)

在我们的案例中，我们可以看到`downloads.db`和`EmailProvider.db`数据库已被修改。分析这些文件将显示收到带附件的电子邮件，然后附件被下载到设备上。

最后，**视图**部分标识了已删除的文件（这在移动设备上非常常见，因为磨损平衡的结果），以及大文件（可以快速找到图像/视频或识别隐写术）。 

**结果**部分将显示 Android 分析器和关键词搜索模块的输出，如下面的截图所示：

![在 Autopsy 中分析数据](img/image00446.jpeg)

在**提取的内容**下看到的 Android 分析器结果大多如预期。值得注意的是**联系人（1）**部分只指向`contacts.db`文件，并没有实际解析数据。例如，**通话记录**显示了从`contacts2.db`中提取的数据，如第七章*Android 应用程序取证分析*中所述：

![在 Autopsy 中分析数据](img/image00447.jpeg)

**扩展名不匹配检测**结果还显示了我们在第七章*Android 应用程序取证分析*中发现的数据。有几个应用程序被描述为具有实际上是 JPEG 图像的`.cnt`文件，这些文件被 Autopsy 恰当地识别出来，如下面的截图所示：

![在 Autopsy 中分析数据](img/image00448.jpeg)

双击上面看到的任何文件将带用户到文件在文件系统中的位置。

**关键词命中**部分恰当地找到了许多电子邮件地址和电话号码。然而，许多这些都是在应用程序文件中找到的（即应用程序开发者的联系信息）以及用户实际上没有存储的其他位置（这在移动和计算机取证工具中都很常见）。

Autopsy 还有许多其他更高级的功能，这里没有涵盖。要了解更多信息，Basis Technology 提供了 Autopsy 培训课程，网址为[`www.basistech.com/digital-forensics/autopsy/training/`](http://www.basistech.com/digital-forensics/autopsy/training/)。

# ViaLab 社区版

ViaLab 社区版是 NowSecure 开发和发布的另一个免费工具。它作为一个独立的虚拟机发货，并且可以在[`www.nowsecure.com/apptesting/community/`](https://www.nowsecure.com/apptesting/community/)找到（需要注册）。该虚拟机实际上与我们在本章开头讨论的 Santoku 下载非常相似，但包括 ViaLab 社区版工具。

### 提示

ViaLab 要求检查员的计算机必须有互联网连接，以便使用该工具。

ViaLab 的主要目的是分析 APK 的行为，尽管许多用于此目的的功能在免费的社区版中不可用。ViaLab 允许您手动将 APK 文件加载到 Android 模拟器中，或在已 root 的设备上运行应用程序。在我们的示例中，我们手动将 Kik 的 APK 文件加载到 Android 模拟器中。我们选择 Kik，因为它在第七章中进行了彻底分析，因此我们对预期结果有很好的了解，并且可以确认我们之前的发现。这样做的一个很好的取证用例是研究一个应用程序以了解它存储了哪些数据。例如，如果检察官正在寻找保存的视频，检查员可以确定应用程序是否具有该功能以及它们存储在何处。

## 在 ViaLab 中设置模拟器

使用 ViaLab 之前，必须先激活。这是通过在桌面上找到的 ViaForensics 产品激活工具完成的。注册后，按照以下步骤设置模拟器：

1.  单击桌面上的**ViaLab**图标启动工具：![在 ViaLab 中设置模拟器](img/image00449.jpeg)

1.  程序可能会提示输入 root 密码。在 ViaLab VM 中，默认密码是`vialab1`：![在 ViaLab 中设置模拟器](img/image00450.jpeg)

1.  程序启动后，将运行系统检查。单击**关闭**将完成系统检查。还可以取消选择**启动时始终运行系统检查**框，以在将来跳过此步骤：![在 ViaLab 中设置模拟器](img/image00451.jpeg)

1.  要启用 Android 模拟器（或配置 ViaLab 的真实设备），请在主屏幕左下角选择**设备管理器**图标，并在打开的窗口中进行适当的选择：![在 ViaLab 中设置模拟器](img/image00452.jpeg)

1.  第一次使用 Android 模拟器时，将需要安装额外的软件包：![在 ViaLab 中设置模拟器](img/image00453.jpeg)

1.  安装模拟器包并重新启动 ViaLab 后，**设备管理器**现在将显示**启动模拟器**选项。选择此选项启动 Android 模拟器：![在 ViaLab 中设置模拟器](img/image00454.jpeg)

1.  Android 模拟器将启动并显示在单独的窗口中。主屏幕右下角的**设备管理器**图标应显示 ViaLab 现在已连接到模拟器：![在 ViaLab 中设置模拟器](img/image00455.jpeg)

## 在模拟器上安装应用程序

要开始 ViaLab 分析，请按照以下步骤操作：

1.  在左上角选择**新建**。这将打开一个窗口以添加新项目：![在模拟器上安装应用程序](img/image00456.jpeg)

1.  从**添加项目**对话框中选择**前进**将带您到设置页面：![在模拟器上安装应用程序](img/image00457.jpeg)

1.  要选择要安装到模拟器中的 APK 文件，请选择**手动添加应用程序**并选择文件。一旦选择了 APK 文件，**下载**按钮将变为可用。选择此按钮将 APK 推送到模拟器，并在完成后选择**确定**：![在模拟器上安装应用程序](img/image00458.jpeg)

1.  现在您可以转到模拟器窗口并使用该应用程序填充测试数据。请注意，性能可能会非常慢，因为模拟器是在虚拟机中运行的虚拟机：![在模拟器上安装应用程序](img/image00459.jpeg)

应用程序在模拟环境下可能会有不同的表现。例如，Kik 要求我们解决验证码以证明我们是人类！其他应用程序可能会有降低的功能（例如涉及 GPS 数据的任何内容）。

## 使用 ViaLab 分析数据

在应用程序中填充数据后，让我们来分析它！按照以下步骤进行：

1.  返回 ViaLab 并选择屏幕顶部的**取证**选项卡。选择**刷新应用程序文件夹**将从设备中提取数据：![使用 ViaLab 分析数据](img/image00460.jpeg)

1.  一旦数据同步完成，可以选择**文件列表**按钮按类型筛选文件：![使用 ViaLab 分析数据](img/image00461.jpeg)

例如，这是我们在应用程序中填充的联系人数据，存储在我们在第七章中检查的数据库中，*Android 应用程序取证分析*：

![使用 ViaLab 分析数据](img/image00462.jpeg)

# 总结

本章概述了一些供 Android 取证人员使用的免费工具。这些工具在下表中进行了总结：

| 工具 | 特点 |
| --- | --- |
| ViaExtract |

+   免费，需要注册和有效的互联网连接

+   通过将应用程序推送到设备进行逻辑提取

+   备份提取

+   如果设备已 root，则进行文件系统提取

+   对设备进行 root

+   通过将应用程序推送到设备，无需 root 即可绕过屏幕锁定

|

| Autopsy |
| --- |

+   免费且开源

+   用于检查其他工具提取的数据

+   允许关键字搜索、哈希列表和其他常见取证方法

+   强大的时间轴功能

+   可以从支持的文件系统中恢复已删除的数据

|

| ViaLab |
| --- |

+   免费，需要注册和有效的互联网连接

+   允许取证人员从 APK 运行应用程序并确定数据存储位置

+   在模拟器或测试设备上运行应用程序

+   宝贵的工具，可以向取证人员展示应用程序目录中的数据存储位置，以及查看应用程序的功能

|

# 结论

我们希望所有人在未来的 Android 取证中都能好运。我们真诚地希望本书中的内容能在某个时候帮助到您。我们的目标是制作一本关于整个 Android 取证过程的信息指南。我们希望您在学习的过程中有所收获（我们在写作过程中也有很多收获）。谢谢您的阅读！