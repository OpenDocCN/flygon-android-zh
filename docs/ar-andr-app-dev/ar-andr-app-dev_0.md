# 前言

增强现实技术将物理世界与虚拟世界融合，产生魔幻效果，并将应用从屏幕带到你的手中。增强现实技术彻底重新定义了广告、游戏以及教育的方式；它将成为移动应用开发者需要掌握的技术。本书使你能够实际在 Android 上实现基于传感器和计算机视觉的增强现实应用。了解实施增强现实应用程序的理论基础和实践细节。通过动手实践，你可以快速开发和部署新颖的增强现实应用。

# 本书涵盖的内容

第一章，*增强现实概念与工具*，介绍两种主要的增强现实方法：基于传感器和基于计算机视觉的增强现实。

第二章，*观察世界*，介绍构建增强现实应用程序的第一步基础：在设备上捕捉和显示真实世界。

第三章，*叠加世界*，帮助你使用 JMonkeyEngine 将高保真的 3D 模型覆盖在物理世界上。

第四章，*在世界中定位*，提供使用传感器和 GPS 实现你自己的增强现实浏览器的基本构建块。

第五章，*与好莱坞相同 - 在物理对象上的虚拟现实*，为你讲解基于计算机视觉的 AR 中 Vuforia^(TM) SDK 的强大功能。

第六章，*使其互动 - 创建用户体验*，解释如何让增强现实应用具有互动性。具体来说，你将学习如何开发射线拣选、基于接近度的互动以及基于 3D 动作手势的互动。

第七章，*进一步阅读与技巧*，介绍更多高级技术，以提升任何 AR 应用程序的开发。

# 你需要为这本书准备的内容

如果你想要为 Android 开发增强现实应用，你可以与常规 Android 开发者共享大部分工具。特别是，你可以利用广泛支持的**Android 开发者工具包**（**ADT Bundle**）。这包括：

+   Eclipse **集成开发环境**（**IDE**）

+   用于 Eclipse 的**Android 开发者工具**（**ADT**）插件

+   针对目标设备的 Android 平台（可以下载其他平台）

+   配备最新系统镜像的 Android 模拟器

除了许多 Android 开发环境共有的这个标准包之外，你还需要：

+   **JMonkeyEngine** (**JME**)，版本 3 或更高版本的快照

+   **高通® Vuforia^(TM)** **软件开发包**（**Vuforia^(TM)**），版本 2.6 或更高

+   **安卓原生开发工具包**（**Android NDK**），版本 r9 或更高

# 本书的目标读者

如果您是 Android 移动应用开发者，并且想要使用增强现实技术将移动应用开发提升到下一个层次，这本书就是为您准备的。我们假设您熟悉 Android 开发工具和部署。如果您有使用 Android 外部库的经验，这将非常有用，因为我们会用到 JMonkeyEngine 和 Vuforia^(TM) SDK。如果您已经使用过 Android N，这很棒，但不是必须的。

# 约定

在这本书中，您会发现多种文本样式，用于区分不同类型的信息。以下是一些样式示例及其含义的解释。

文本中的代码字、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名会以下面的形式显示："最后，您在`CameraPreview`类的`onSurfaceChanged()`方法中注册了`Camera.PreviewCallback`接口的实现。"

代码块设置如下： 

```java
public static Camera getCameraInstance() {
  Camera c = null;
  try {
    c = Camera.open(0);
  } catch (Exception e) { ...  }
  return c;
}
```

**新术语**和**重要词汇**会用粗体显示。您在屏幕上看到的词，比如菜单或对话框中的，会在文本中以这样的形式出现："在弹出菜单中，选择**运行方式 | 1 安卓应用**。"

### 注意

警告或重要信息会以这样的框显示。

### 小贴士

提示和技巧会像这样出现。

# 读者反馈

我们始终欢迎读者的反馈。告诉我们您对这本书的看法——您喜欢或可能不喜欢的内容。读者的反馈对我们来说非常重要，它帮助我们开发出您真正能从中获益最多的书籍。

如果您想要给我们发送一般性反馈，只需发送电子邮件至`<feedback@packtpub.com>`，并在邮件的主题中提及书名。

如果您在某个主题上有专业知识，并且有兴趣撰写或为书籍做贡献，请查看我们在[www.packtpub.com/authors](http://www.packtpub.com/authors)上的作者指南。

# 客户支持

既然您已经拥有了 Packt 的一本书，我们有许多方法可以帮助您充分利用您的购买。

## 下载示例代码

您可以从您的账户[`www.packtpub.com`](http://www.packtpub.com)下载您购买的所有 Packt 书籍的示例代码文件。如果您在别处购买了这本书，可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)注册，我们会直接将文件通过电子邮件发送给您。您也可以在[`github.com/arandroidbook/ar4android`](https://github.com/arandroidbook/ar4android)找到代码文件。

## 勘误

尽管我们已经竭尽全力确保内容的准确性，但错误仍然在所难免。如果你在我们的书中发现了一个错误——可能是文本或代码中的错误——我们非常感激你能向我们报告。这样做可以避免其他读者产生困扰，并帮助我们改进本书后续版本。如果你发现任何勘误信息，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择你的书籍，点击**勘误提交表单**链接，并输入你的勘误详情。一旦你的勘误信息被核实，你的提交将被接受，并且勘误信息将被上传到我们的网站，或添加到该标题勘误部分现有的勘误列表中。任何现有的勘误信息可以通过选择你的标题，在[`www.packtpub.com/support`](http://www.packtpub.com/support)进行查看。

## 盗版

互联网上版权资料的盗版问题在所有媒体中持续存在。在 Packt，我们非常重视保护我们的版权和许可。如果你在任何形式下，在互联网上遇到我们作品的非法副本，请立即提供我们该位置地址或网站名称，以便我们可以寻求补救措施。

如果发现疑似盗版资料，请通过`<copyright@packtpub.com>`联系我们，并提供一个链接。

我们感谢你帮助保护我们的作者，以及我们为你提供有价值内容的能力。

## 问题

如果你在这本书的任何方面遇到问题，可以通过`<questions@packtpub.com>`联系我们，我们将尽力解决。