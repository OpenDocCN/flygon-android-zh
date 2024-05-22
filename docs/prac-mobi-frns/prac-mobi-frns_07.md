# 第六章：iOS 取证工具

像您这样的取证人员不仅必须知道如何使用取证工具，还必须了解工具在调查中使用的方法和获取技术。除了节省时间外，取证工具还使取证分析过程变得更加容易。然而，每个工具都有其缺陷。您必须发现任何错误，并知道如何通过利用另一个工具或技术来纠正它们。一个工具不可能支持所有设备。您有责任学习并使用最佳工具来完成工作。正如我们在前几章中讨论的那样，您必须了解数据在 iOS 设备上的存储方式，以确保工具捕获所有可访问的数据。

目前，有许多商业工具可用于对 iOS 设备进行取证获取和分析，例如 Cellebrite UFED 物理分析器、BlackBag BlackLight、Oxygen Forensic Detective、Belkasoft Evidence Center、MSAB XRY、Magnet AXIOM 等。出于熟悉的目的，本章将带您了解其中一些工具的使用，并提供执行 iOS 设备取证和分析所需步骤的详细信息。

在本章中，我们将涵盖以下主题：

+   使用 Cellebrite UFED 物理分析器

+   使用 Magnet AXIOM

+   使用 Belkasoft Evidence Center

+   使用 Elcomsoft Phone Viewer

# 使用 Cellebrite UFED 物理分析器

根据供应商，Cellebrite **通用取证提取设备**（**UFED**）赋予执法、反恐和安全组织从手机、智能手机、PDA 和便携式手机等各种设备中捕获关键的取证证据的能力，包括对新发布型号的更新。该工具能够进行取证合规的数据提取、解码和分析技术，以获取来自不同移动设备的现有和已删除数据。截至 2019 年 2 月，UFED 支持从近 28000 种移动设备中提取数据。

# Cellebrite UFED 物理分析器的特点

以下是 Cellebrite UFED 物理分析器的特点：

+   支持不同类型的获取

+   提取需要解密物理图像和钥匙链项目的设备密钥

+   解密物理图像和钥匙链项目

+   显示设备密码（并非所有锁定设备都可用）

+   允许您使用已知密码打开加密的原始磁盘映像文件

+   支持密码恢复攻击

+   支持对提取的应用程序数据进行高级分析和解码

+   提供对物理和逻辑数据的访问，以在同一用户界面中进行分析更容易

+   生成多种流行格式的报告，包括 Microsoft Excel、PDF 和 HTML

+   转储原始文件系统分区，以便在另一个取证工具中导入和检查

+   创建二进制图像文件，以及用于导入其他取证工具进行验证的 UFED 快捷文件

现在让我们来看高级逻辑获取和分析。

# 使用 Cellebrite UFED 物理分析器进行高级逻辑获取和分析

如前所述，物理分析器不仅可用于解析获取的图像中的不同类型的取证物件，还可用于从 iOS 设备中执行逻辑和文件系统（甚至是旧设备的物理）类型的提取。由于物理获取实际上仅适用于旧设备，最佳选择是高级逻辑获取。

我们将从运行 iOS 13.2.3 的 iPhone 中获取和分析数据。让我们开始吧：

1.  通过适当的电缆将设备连接到您的工作站。确保它是受信任的，并启动物理分析器。

1.  转到提取 | iOS 设备提取。iOS 设备数据提取向导窗口将弹出：

![](img/d760b426-7dc8-4a85-ab3c-de0df8d180bb.png)

选择提取类型

1.  由于我们正在处理现代 iOS 设备，让我们选择高级逻辑提取。如果设备被识别，您将看到设备的名称、其 UDID，以及其 iOS 版本：

![](img/dece4869-de70-4a76-8ec5-c90ceb61be1e.png)

连接设备

在我们的案例中，iPhone 的 iTunes 备份受到已知密码的保护，所以最好的方法是方法 1：

![](img/8d6fed72-ad97-4294-80b6-d7636f0b2ce1.png)

选择提取方法

1.  如果您希望备份被加密（建议），您可以在下一页选择此选项：

![](img/343de65a-4131-4ca3-978d-64d2546687ae.png)

选择备份是否应该加密

1.  是时候选择数据将保存在哪里了；在我们的案例中，是`D:\`驱动器的根目录：

![](img/57eb13be-1d34-46b3-a744-e73b3e7d5a93.png)

选择提取的保存位置

1.  现在，获取过程将开始。确保设备连接到整个过程结束：

![](img/57e9cbfc-6be3-4c86-86c6-78ce1c699fbc.png)

提取数据

一旦提取过程完成，提取的数据将通过强大的物理分析器插件进行解析。结果，您将获得一组分成多个类别的工件：

![](img/94edba14-72bb-4366-b38b-a151fa0c3acd.png)

由物理分析器提取和解析的手机数据

关于数据文件也可以说同样的话：

![](img/9aa33e2e-747a-4a7d-a73f-7e4d873bdf78.png)

由物理分析器提取的数据文件

您可能已经注意到，括号中有红色数字——这些是物理分析器插件恢复的已删除记录。正如您已经知道的那样，从 iOS 广泛使用的 SQLite 数据库中恢复已删除的数据并不是什么奇迹。

谈到 SQLite 数据库，物理分析器还有另一个令人惊叹的功能，可能对于向移动取证报告添加自定义工件和解析未知应用数据非常有用——SQLite 向导。您可以在工具 | SQLite 向导下找到它：

1.  让我们从选择数据库开始。当然，最好选择一个物理分析器没有自动解析的应用。在我们的例子中，这是一个名为 Scan 的应用：

![](img/97bd5ad7-0eec-41fa-9531-025ab7a00a03.png)

选择数据库

1.  确保您已选择了“包括已删除的行”选项；这将帮助您自动恢复数据，但当然也会增加误报记录的数量：

![](img/2824e656-f6df-40c1-8992-e17ba433ab04.png)

启动 SQLite 向导

因此，我们的应用程序用于扫描 QR 码，并包含四个感兴趣的列——扫描日期和时间、纬度、经度和扫描结果。所有行都属于`ZSCSCANEVENT`：

![](img/ac215964-177a-4951-bf26-97457a0e3838.png)

选择数据库表和列

1.  下一步是选择时间戳。您已经学到了很多关于 iOS 时间戳，并且应该能够识别`ZTIMELESSCREATIONDATE`中的格式，但即使您不知道，SQLite 向导也会为您完成这一步：

![](img/09b0f770-2836-49f8-bd4c-1562252624ae.png)

选择时间戳格式

1.  通用型号适用于任何数据库，但也有一些现有的物理分析器型号可用于典型内容，如*聊天*或*联系人*。在我们的案例中，我们使用通用型号：

![](img/728b0766-f96b-475a-ab3e-7b9f93396684.png)

选择型号

一旦您选择了模型和列的字段类型，您可以运行查询并将新解析的工件添加到您的提取中，然后添加到您的报告中。

# 使用 Magnet AXIOM

Magnet AXIOM 是市场上最有用的数字取证工具之一。它可用于计算机和移动取证；套件的最新版本引入了最新功能-云取证。至于 iOS 取证，它可以用于逻辑和文件系统获取，并支持所有 iOS 版本-从最旧到最新。当然，它也可以用于解析 iTunes 备份和第三方工具（如 Elcomsoft iOS Forensic Toolkit）创建的物理镜像。

Magnet AXIOM 最好的功能之一是它能够在获取过程完成之前即时开始处理提取数据，这样您就不必等待获取过程完成才能开始取证分析。

# Magnet AXIOM 的功能

以下是 Magnet AXIOM 的功能：

+   支持逻辑和文件系统（对越狱设备）获取

+   支持加密和非加密的 iTunes 备份

+   恢复了 500 多种工件类型

+   与其他流行的移动取证工具（如 Cellebrite UFED 和 XRY）兼容

+   包括内置的 SQLite 和 plist 查看器

+   创建所谓的**便携式案例**，以便您可以与队友和第三方共享整套数据

+   可以生成多种流行格式的报告，如 Microsoft Excel、PDF 和 HTML

# 使用 Magnet AXIOM 进行逻辑获取和分析

正如您可能记得的那样，现代 iOS 设备最常见的获取方式是逻辑类型。以下是您如何使用 Magnet AXIOM 获取 iOS 设备的方法：

1.  首先创建一个新案例：

![](img/c5d1fbc9-8565-42dc-97e4-f002ab701a43.png)

创建新案例

1.  由于我们处理的是 iOS 设备，我们将选择**移动**选项：

![](img/9bb9de17-c30b-4b75-a713-0f6f43f6dfef.png)

选择证据来源

1.  有许多选项可供选择，但在我们的情况下，**iOS**选项是正确的：

![](img/c8307714-3cc7-40d5-ae64-58c93c1b66e9.png)

选择证据来源

1.  有三种获取证据的选项-我们可以选择已获取的图像（例如 iTunes 备份或第三方工具获取的文件系统图像），从设备中提取数据，或使用 GrayKey 进行获取。让我们选择第二个选项：

![](img/1fdfa17f-8d00-4697-bf4b-6be439edad46.png)

选择获取证据

1.  我们的设备已被识别并准备好进行成像。如果您没有看到您的设备，请使用**未知**选项：

![](img/35fa4cba-5cbc-45f9-96bd-b17d97bb7aa1.png)

选择设备

1.  有两种提取类型-快速和完整。如果您要获取的设备已越狱，则只有完整选项可用。在我们的情况下，不是：

![](img/a266db5b-e1ca-440a-9e25-6dfc2339ae6f.png)

选择图像类型

1.  您将被提示输入备份密码。正如您可能记得的那样，这样可以获取更多数据，因此强烈建议这样做：

![](img/0dcfbb9c-16e0-4c0c-acf8-56af7da401ed.png)

加密备份

1.  在开始获取和处理之前，您可以选择感兴趣的关键词，使用 Magnet.AI 对聊天进行分类，或配置动态应用程序查找器等：

![](img/63f0434f-027a-4d98-b09a-e7be703234bc.png)

处理细节

动态应用程序查找器是 Magnet IEF 和 AXIOM 的功能，可以在图像中找到潜在的移动聊天应用程序数据库。您可以在此链接了解更多关于此功能的信息：[`www.magnetforensics.com/mobile-forensics/using-dynamic-app-finder-to-recover-more-mobile-artifacts/`](https://www.magnetforensics.com/mobile-forensics/using-dynamic-app-finder-to-recover-more-mobile-artifacts/)

1.  您可以从这里自定义**移动工件**。例如，如果您只对聊天工件感兴趣，最好只选择这些类型的工件，因为它们会缩短处理时间：

![](img/1914a8c3-221f-4921-a314-1d0360abb809.png)

选择移动工件

1.  点击“分析证据”按钮将启动获取和分析过程：

![](img/f047bee1-653a-4e57-aeee-fa0e8998083b.png)

成像证据来源

1.  Magnet AXIOM 有两个窗口-处理和检查。第一个可用于监视获取和处理证据来源的过程，而第二个可用于分析提取和解析的数据。正如我们之前提到的，您可以在处理阶段结束之前开始分析。您只需要在 Magnet Examine 中点击 LOAD NEW RESULTS：

![](img/d74dc9a1-a755-4aff-800c-386899e13f69.png)

加载新结果

1.  一旦处理阶段结束，您可以在 Magnet Examine 的 MOBILE 部分找到解析的数据：

![](img/58e6b0ce-8ae5-44f5-95ad-2b3d51bb5c55.png)

移动部分

但是，当然，它不会包括一切；还有其他有价值的部分，您可以在其中找到从 iOS 设备中提取的证据，例如 CHAT，MEDIA 和 DOCUMENTS。

# 使用 Belkasoft Evidence Center

Belkasoft Evidence Center 是另一个流行的数字取证工具，能够执行 iOS 设备的获取和分析。与 AXIOM 一样，它可用于计算机，移动和云取证。

Belkasoft Evidence Center 最好的功能之一是其处理受损 iTunes 备份的能力。因此，如果您有一个没有任何工具能够处理的备份，请尝试 Belkasoft Evidence Center；根据我们的经验，它将成功处理它。

# Belkasoft Evidence Center 的功能

以下是 Belkasoft Evidence Center 的功能：

+   支持逻辑和文件系统（用于越狱设备）获取

+   支持加密和非加密的 iTunes 备份

+   支持受损的 iTunes 备份

+   恢复了 700 多种工件类型

+   与其他流行的移动取证工具一起使用，如 Cellebrite UFED 和 XRY

+   包括内置的 SQLite 和 plist 查看器

+   包括一个免费的脚本模块 BelkaScript，允许检查员编写自己的脚本来自动执行一些常见任务

+   可以生成几种流行格式的报告，如 Microsoft Excel，PDF 和 HTML

# 使用 Belkasoft Evidence Center 进行逻辑获取和分析

由于备份处理和分析是 Belkasoft Evidence Center 最好的功能之一，我们将在这里为您介绍这个过程：

1.  让我们从创建一个新案例开始：

![](img/6aa17baa-7801-4e41-8351-cf3b850eca3b.png)

创建新案例

1.  这里有多个选项-您可以处理先前获取的图像，例如 iTunes 备份，或选择首先从设备中提取数据。让我们从逻辑获取开始。选择移动选项：

![](img/04537589-c3f3-4e2b-9ce7-0400d718b69d.png)

选择数据源

1.  由于我们正在处理 iOS 设备，选择苹果。您将看到可用设备的列表：

![](img/6ad8756b-1b6e-4695-b3c4-e3d684c01055.png)

可用于获取的设备

1.  我们的设备没有越狱，因此我们的选择要么是逻辑获取，要么是 iTunes 备份：

![](img/a1d86b0d-b0b9-4a32-9431-62d5b29e244d.png)

选择获取方法

1.  一旦获取完成，您可以选择感兴趣的工件。确保只选择与 iOS 相关的工件；这将减少处理时间：

![](img/92b4563d-aa6c-4fdd-874d-7e6bf20cd3b0.png)

选择数据类型

1.  一旦处理完成，提取的工件将显示在概述选项卡中：

![](img/4d94b99e-1797-4af6-9e31-2953dd0eb62a.png)

概述选项卡

1.  最后，如果您想浏览法证图像的文件系统，请使用文件系统选项卡：

![](img/9b1a69d4-7ec9-4435-93b9-f75ec3845539.png)

文件系统选项卡

其他可用的选项卡也可能很有用-Dashboard 选项卡显示您当前正在处理的案例的所有可用信息，任务管理器选项卡允许您监视处理进度，搜索结果选项卡显示您的关键字搜索结果。

# 使用 Elcomsoft Phone Viewer

Elcomsoft Phone Viewer 是一款工具，能够解析和查看不仅来自 iOS 设备，还来自 BlackBerry 10 和 Windows Phone 设备的提取数据。它提供对逻辑和文件系统图像的只读、法庭可靠的访问，以及对从云端提取的数据的访问。

# Elcomsoft Phone Viewer 的特点

以下是 Elcomsoft Phone Viewer 的特点：

+   分析在线活动，包括浏览历史记录、书签和打开的标签页。

+   提供对同步数据、密码和用户数据的访问，包括消息、通话记录和联系人。

+   它对多媒体文件进行分类，以便您更容易了解照片是通过消息接收的还是使用手机相机拍摄的。

+   聚合来自不同来源的位置数据。

+   支持逻辑图像、文件系统图像以及 iTunes 和 iCloud 备份。

# 使用 Elcomsoft Phone Viewer 进行文件系统分析

Elcomsoft Phone Viewer 不支持设备获取，但可以解析和帮助您查看使用 Elcomsoft iOS Forensic Toolkit、Elcomsoft Cloud eXtractor 或几乎任何其他能够获取 iOS 设备的工具提取的数据。

要查看以前使用 Elcomsoft iOS Forensic Toolkit 创建的文件系统图像，请按照以下步骤操作：

1.  启动 Elcomsoft Phone Viewer 并选择适当的数据源。在我们的情况下，这是 iOS 设备图像：

![](img/4e3e89dc-52aa-4480-b1b3-2623253f9e8d.png)

选择数据源

1.  选择要导入的文件和要解析的文物：

![](img/f5eb285a-2f96-4873-9400-b3da4f144885.png)

选择数据类型

1.  等待提取过程完成：

![](img/35cb6e79-5408-457c-a6be-b6e5db7efbe4.png)

提取过程

因此，您将获得设备的信息，以及分成多个类别的解析文物：

![](img/4cea1620-5a95-4f4f-8318-9b4166f643e8.png)

解析文物

现在，您可以轻松查看、过滤和导出可能对您的检查有兴趣的任何数据 - 您所需做的就是点击相应的图标。

# 摘要

取证工具对于检查员来说非常有帮助，因为它们不仅节省时间，而且使整个过程变得更加容易。然而，并非每个人都有足够大的预算来购买商业工具以获取 iOS 数据。虽然存在用于获取数据的免费工具，但支持可能有限，并且可能需要多次提取才能获得与商业工具相同数量的数据。

对于越狱设备，iOS 设备可以通过 SSH 连接到取证工作站进行实时检查，这是一些工具获取必要数据的方法。然而，这并不是推荐给新手移动取证的方法。出于这种目的，本章向您介绍了几种可用的 iOS 取证工具，并包括您需要遵循的步骤来执行获取和分析。

您应该进一步验证和了解可能作为调查一部分使用的每个工具。我们建议您获取具有已知数据的测试设备，以确保没有遗漏任何内容，证据没有被篡改，并且方法能够让您访问感兴趣的数据。

下一章将介绍 Android 取证，并涵盖 Android 平台的基本概念。