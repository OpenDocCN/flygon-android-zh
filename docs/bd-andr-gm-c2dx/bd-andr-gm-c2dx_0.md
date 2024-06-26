# 前言

Cocos2d-x 是最常使用的开源游戏框架。它得到了微软对其移动和桌面平台官方支持，其小巧的核心运行速度比其他框架快，使得它能在低端 Android 设备上表现出色。目前，它由一个活跃的开源开发社区维护，该社区由原始 Cocos2d for iPhone 的作者和触控科技领导。

这本入门书籍将指导你从零开始创建一个简单的二维 Android 游戏。在这个过程中，你将学习 Cocos2d-x C++跨平台游戏框架的基础知识，如何处理精灵，为游戏添加物理效果，播放声音，显示文本，使用粒子系统生成逼真的爆炸效果，以及如何使用 Java Native Interface (JNI)添加原生 Android 功能。

# 这本书涵盖的内容

第一章，*配置你的开发环境*，逐步指导你配置 Cocos2d-x 及其所有先决条件。

第二章，*图形*，介绍了如何处理背景、精灵，以及如何使用精灵表提升性能来动画化它们。

第三章，*理解游戏物理*，展示了基于 Chipmunk 的新 Cocos2d-x 物理引擎的基础知识，该引擎在 Cocos2d-x 3.0 版本中引入。我们将创建基于物理的物体，为它们添加重力，并检测碰撞。

第四章，*用户输入*，我们在这里为游戏添加交互功能，使其能够通过触摸监听器和加速度计与用户互动。

第五章，*处理文本和字体*，证明了处理文本对于游戏开发至关重要。无论你的游戏复杂性如何，你都有可能显示信息，有时需要使用外文字符集。这一章展示了如何使用简单的 TrueType 字体和更具风格的位图字体，使你的游戏看起来更专业。

第六章，*音频*，说明了玩游戏时的情感部分来自于音乐和音效。在这一章中，你将学习如何使用 CocosDenshion 音频引擎为你的游戏添加背景音乐和音效，该音频引擎自原始 Cocos2d iPhone 游戏引擎以来一直存在。这一章还涵盖了如何使用新的音频引擎播放媒体，并突出了它们之间的主要区别。

第七章，*创建粒子系统*，说明了如何使用内置的粒子系统引擎创建逼真的爆炸、火焰、雪和雨效果。这一章还展示了当你需要定制效果时，如何创建自己的粒子系统，使用最受欢迎的工具。

第八章，*添加原生 Java 代码*，在你需要为 Cocos2d-x 游戏活动内部创建和调用 Android 特定行为时为你提供帮助。我们通过使用 Android 平台可用的 Java 原生接口（JNI）机制来实现这一点。

# 你需要这本书的内容

为了跟随本书的叙述并能够重现所有步骤，你需要一台装有 Windows 7 或更高版本操作系统的 PC，任何 Linux 发行版或运行 OS X 10.10 Yosemite 的 Mac。我们将在书中使用的许多工具都是可以免费下载的。我们解释了如何下载和安装它们。

# 本书适合的读者

这本书是为那些在游戏编程方面几乎没有经验，但具备 C++编程语言知识，并且愿意以非常全面的方式创建他们的第一款 Android 游戏的人编写的。

# 约定

在这本书中，你会发现多种文本样式，这些样式区分了不同类型的信息。以下是一些样式示例及其含义的解释。

文本中的代码字、文件夹名、文件名、文件扩展名、路径名、虚拟 URL 和用户输入如下所示："为了向我们的游戏添加加速度计支持，我们首先将在`HelloWorldScene.h`头文件中添加以下方法声明。"

代码块设置如下：

```java
void HelloWorld::movePlayerByTouch(Touch* touch, Event* event)
{
  Vec2 touchLocation = touch->getLocation();
  if(_sprPlayer->getBoundingBox().containsPoint(touchLocation)){
    movePlayerIfPossible(touchLocation.x);
  }
}
```

当我们希望引起你对代码块中某个特定部分的注意时，相关的行或项目会以粗体显示：

```java
   Size screenSize = glview->getFrameSize();
   Size designSize(768, 1280);
   std::vector<std::string> searchPaths;   
   searchPaths.push_back("sounds");

```

任何命令行输入或输出都如下编写：

```java
cocos new MyGame -p com.your_company.mygame -l cpp -d NEW_PROJECTS_DIR

```

**新术语**和**重要词汇**以粗体显示。你在屏幕上看到的内容，例如菜单或对话框中的单词，在文本中如下所示："点击**下一步**按钮，你会进入下一个屏幕。"

### 注意

警告或重要注意事项会像这样出现在一个框中。

### 提示

技巧和诀窍会像这样出现。

# 读者反馈

我们始终欢迎读者的反馈。告诉我们你对这本书的看法——你喜欢或可能不喜欢的内容。读者的反馈对我们来说很重要，它帮助我们开发出你真正能从中获得最大收益的图书。

要向我们发送一般反馈，只需发送电子邮件至`<feedback@packtpub.com>`，并在邮件的主题中提及书名。

如果你在一个主题上有专业知识，并且有兴趣撰写或为一本书做出贡献，请查看我们在[www.packtpub.com/authors](http://www.packtpub.com/authors)上的作者指南。

# 客户支持

既然你现在拥有了 Packt 的一本书，我们有一系列的事情可以帮助你从购买中获得最大收益。

## 下载示例代码

你可以从你在[`www.packtpub.com`](http://www.packtpub.com)的账户下载你所购买的所有 Packt 图书的示例代码文件。如果你在其他地方购买了这本书，可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)注册，我们会直接将文件通过电子邮件发送给你。

## 错误更正

尽管我们已经尽力确保内容的准确性，但错误仍然会发生。如果你在我们的书中发现错误——可能是文本或代码中的错误——我们会非常感激你能向我们报告。这样做，你可以让其他读者免受挫折，并帮助我们在后续版本中改进这本书。如果你发现任何错误更正，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择你的书，点击**错误更正提交表单**链接，并输入你的更正详情。一旦你的更正被验证，你的提交将被接受，并且更正将在我们网站的相应标题下的错误更正部分上传或添加到现有的错误更正列表中。任何现有的错误更正可以通过在[`www.packtpub.com/support`](http://www.packtpub.com/support)选择你的标题来查看。

## 盗版

网络上的版权材料盗版问题在所有媒体中持续存在。在 Packt，我们非常重视保护我们的版权和许可。如果你在互联网上以任何形式遇到我们作品非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

如发现疑似盗版材料，请通过`<copyright@packtpub.com>`联系我们，并提供相关链接。

我们感谢你帮助保护我们的作者，以及我们为你提供有价值内容的能力。

## 问题

如果你在这本书的任何方面遇到问题，可以通过`<questions@packtpub.com>`联系我们，我们将尽力解决。
