# 第六章：Android 生命周期

在本章中，我们将熟悉 Android 应用程序的生命周期。计算机程序有生命周期这个想法一开始可能听起来很奇怪，但很快就会有意义。

生命周期是所有 Android 应用程序与 Android 操作系统交互的方式。就像人类的生命周期使他们能够与周围的世界互动一样，我们别无选择，只能与 Android 生命周期互动，并且必须准备处理许多不可预测的事件，如果我们希望我们的应用程序能够生存下来。

我们将探讨应用程序从创建到销毁经历的生命周期阶段，以及这如何帮助我们知道*何时*根据我们想要实现的目标放置我们的 Kotlin 代码。

在本章中，我们将探讨以下主题：

+   Android 应用程序的生活和时代

+   覆盖过程和`override`关键字

+   Android 生命周期的阶段

+   我们究竟需要了解和做些什么来编写我们的应用程序

+   生命周期演示应用程序

+   Android 代码的结构，以及为下一章深入学习 Kotlin 编码做准备

让我们开始学习 Android 生命周期。

# Android 应用程序的生活和时代

我们已经谈到了我们代码的结构；我们知道我们可以编写类，在这些类中我们有函数，这些函数包含我们的代码，完成任务。我们也知道当我们想要函数中的代码运行（也就是说，被**执行**）时，我们通过使用它的名称**调用**该函数。

此外，在第二章中，*Kotlin、XML 和 UI 设计师*，我们了解到 Android 在应用程序准备启动之前调用`onCreate`函数。当我们输出到 logcat 窗口并使用`Toast`类向用户发送弹出消息时，我们就看到了这一点。

在本章中，我们将研究我们编写的每个应用程序的生命周期中发生的事情；也就是说，当它启动、结束和中间阶段。我们将看到的是，每次运行时，Android 都会与我们的应用程序进行多次交互。

## Android 如何与我们的应用程序交互

Android 通过调用包含在`Activity`类中的函数与我们的应用程序交互。即使该函数在我们的 Kotlin 代码中不可见，它仍然会在适当的时间被 Android 调用。如果这看起来毫无意义，那么请继续阅读。

您是否曾经想过为什么`onCreate`函数之前有一个不寻常的`override`关键字？考虑以下代码行：

```kt
override fun onCreate(…
```

当我们重写`onCreate`等函数时，我们是在告诉 Android 当你调用`onCreate`时，请使用我们重写的版本，因为我们在其中有一些代码需要执行。

此外，您可能会记得`onCreate`函数中不寻常的第一行代码：

```kt
super.onCreate(savedInstanceState)
```

这告诉 Android 在继续使用我们重写的版本之前调用`onCreate`的原始版本。

还有许多其他函数，我们可以选择性地重写它们，它们允许我们在 Android 应用程序的生命周期内的适当时间添加我们的代码。就像`onCreate`在应用程序显示给用户之前被调用一样，还有其他在其他时间被调用的函数。我们还没有看到它们或重写它们，但它们存在，它们被调用，它们的代码被执行。

我们需要了解和理解 Android 在需要时调用的*我们*应用程序的函数，因为这些函数控制着我们代码的生死。例如，如果我们的应用程序允许用户输入重要提醒，然后在输入提醒的一半时，他们的手机响了，我们的应用程序消失了，数据（也就是用户的重要提醒）就消失了？

了解我们的应用程序的生命周期何时、为什么以及哪些功能 Android 将调用是至关重要的，而且幸运的是，这是相当简单的。然后我们可以理解我们需要重写哪些功能来添加我们自己的代码，以及在哪里添加定义我们应用程序的真正功能（代码）。

让我们来研究一下 Android 的生命周期。然后我们可以深入了解 Kotlin 的方方面面，明确我们编写的代码应该放在哪里。

# Android 生命周期的简化解释

如果你曾经使用过 Android 设备，你可能已经注意到它的工作方式与许多其他操作系统有很大不同。例如，你可以在设备上使用一个应用程序，也许在查看 Facebook 上的人们在做什么。

然后，你收到一封电子邮件通知，你点击通知来阅读它。在阅读邮件的过程中，你可能会收到 Twitter 通知，因为你正在等待某个关注者的重要消息，所以你中断了邮件阅读并触摸屏幕切换到 Twitter。

阅读完推特后，你想玩《愤怒的小鸟》；然而，在第一次射击的一半时，你突然想起了 Facebook 的帖子。所以，你退出《愤怒的小鸟》并点击 Facebook 图标。

你可能会在恢复 Facebook 时恰好回到离开它的地方。之后，你可以回到阅读邮件，决定回复推特，或者开始一个全新的应用程序。

所有这些来回需要操作系统进行相当多的管理，并且独立于各个应用程序本身。

例如，就我们刚刚讨论的内容而言，Windows PC 和 Android 之间的区别是显著的。在 Android 中，尽管用户决定使用哪个应用程序，但操作系统决定何时关闭（或销毁）应用程序以及我们用户的数据（例如假设的笔记）。我们在编写应用程序时需要考虑这一点；仅仅因为我们可能编写代码来处理用户的输入并不意味着 Android 会允许代码执行。

## 生命周期阶段的神秘

Android 系统有许多不同的阶段，任何给定的应用程序都可能处于其中之一。根据阶段，Android 系统决定用户如何查看应用程序，或者是否根本不查看。

Android 有这些阶段，以便它可以决定哪个应用程序正在使用，并且可以为应用程序分配正确数量的资源，例如内存和处理能力。

此外，当用户与设备进行交互（例如触摸屏幕）时，Android 必须将该交互的详细信息传递给正确的应用程序。例如，在《愤怒的小鸟》中进行拖动和释放动作意味着射击，但在消息应用中可能意味着删除短信。

我们已经提出了当用户退出我们的应用程序来接听电话时会丢失他们的进度、数据或重要笔记的问题。

Android 有一个系统，简化一下以便解释，意味着 Android 设备上的每个应用程序都处于以下阶段之一：

+   正在创建

+   开始

+   恢复

+   运行

+   暂停

+   停止

+   被销毁

这个阶段列表希望看起来是合乎逻辑的。例如，用户按下 Facebook 应用程序图标，应用程序正在创建；然后，它被启动。到目前为止，一切都很简单，但列表中的下一个阶段是恢复。

这并不像一开始看起来那么不合逻辑。如果我们能暂时接受应用程序在启动后恢复，那么随着我们的继续，一切都会变得清晰起来。

在恢复之后，应用程序正在运行。这时，Facebook 应用程序控制着屏幕，并且拥有更多的系统内存和处理能力，并且正在接收用户输入的详细信息。

那么，我们切换从 Facebook 应用到邮件应用的例子呢？

当我们点击去阅读我们的电子邮件时，Facebook 应用程序将进入**暂停**阶段，然后是**停止**阶段，而电子邮件应用程序将进入**被创建**阶段，然后是**恢复**，然后是**运行**。

如果我们决定重新访问 Facebook，就像在前面的情景中一样，Facebook 应用程序可能会跳过**被创建**直接进入**恢复**，然后再次**运行**（很可能在我们离开它的确切位置）。

请注意，随时，Android 都可以决定**停止**然后**销毁**一个应用程序，在这种情况下，当我们再次运行应用程序时，它将需要在第一个阶段**被创建**。

因此，如果 Facebook 应用程序长时间不活动，或者*愤怒的小鸟*需要太多系统资源，以至于 Android**销毁**了 Facebook 应用程序，那么我们之前阅读的确切帖子的体验可能会有所不同。关键是应用程序及其与生命周期的交互控制了用户的体验。

如果所有这些开始变得令人困惑，那么你会高兴地知道提到这些阶段的唯一原因是因为以下原因：

+   你知道它们存在

+   我们偶尔需要与它们交互

+   当我们做的时候，我们将一步一步地进行

# 我们如何处理生命周期阶段

当我们编写应用程序时，我们如何与这种复杂性进行交互？好消息是，当我们创建第一个项目时自动生成的 Android 代码大部分都是为我们做的。

正如我们所讨论的，我们并不只是看到处理这种交互的函数，但我们有机会覆盖它们并在需要时向该阶段添加我们自己的代码。

这意味着我们可以继续学习 Kotlin 并制作 Android 应用程序，直到我们遇到偶尔需要在其中一个阶段做一些事情的情况。

### 注意

如果我们的应用程序有多个活动，它们将各自拥有自己的生命周期。这并不一定会使事情复杂化，总的来说，这将使事情对我们更容易。

以下列表提供了 Android 提供的用于管理生命周期阶段的函数的快速解释。为了澄清我们对生命周期函数的讨论，它们被列在我们一直在讨论的相应阶段旁边。然而，正如你将看到的，函数名称本身清楚地表明了它们在哪里适用。

在列表中，还有对为什么我们可能在每个阶段使用每个函数进行交互的简要解释或建议。随着我们在书中的进展，我们将遇到大部分这些函数；当然，我们已经见过`onCreate`：

+   `onCreate`：当 Activity*被创建*时，将执行此函数。在这里，我们为应用程序准备好一切，包括 UI（例如调用`setContentView`）、图形和声音。

+   `onStart`：当应用程序处于*启动*阶段时，将执行此函数。

+   `onResume`：此函数在`onStart`之后运行，但也可以在 Activity 在先前暂停后恢复时（最合乎逻辑的）进入。我们可能会从应用程序被中断时重新加载先前保存的用户数据（例如重要的笔记），例如通过电话呼叫或用户运行另一个应用程序。

+   `onPause`：当我们的应用程序*暂停*时会发生这种情况。在这里，我们可能会保存未保存的数据（例如笔记），这些数据可以在`onResume`中重新加载。当另一个 UI 元素显示在当前 Activity 的顶部（例如弹出对话框）或 Activity 即将停止时（例如用户导航到不同的 Activity）时，Activity 总是转换到暂停状态。

+   `onStop`：这与*停止*阶段有关。这是我们可能会撤消`onCreate`中所做的一切的地方，例如释放系统资源或将信息写入数据库。如果我们到达这里，Activity 很可能很快就会被销毁。

+   `onDestroy`：这是当我们的 Activity 最终被*销毁*时发生的。在这个阶段没有回头路。这是我们有序拆除应用程序的最后机会。如果 Activity 达到这个阶段，它将需要在下次使用应用程序时从头开始经历生命周期阶段。

以下图表显示了函数之间执行的可能流程：

![我们如何处理生命周期阶段](img/B12806_06_03.jpg)

所有函数的描述及其相关阶段应该很直接。唯一真正的问题是：关于运行阶段呢？正如当我们在其他函数和阶段中编写代码时所看到的，`onCreate`、`onStart`和`onResume`函数将准备应用程序，然后保持运行阶段。然后，`onPause`、`onStop`和`onDestroy`函数将在此之后发生。

现在我们可以通过一个迷你应用程序来观察这些生命周期函数的实际运行情况。我们将通过重写它们并为每个函数添加一个`Log`消息和一个`Toast`消息来实现这一点。这将直观地演示我们的应用程序经历的各个阶段。

# 生命周期演示应用程序

在本节中，我们将进行一个快速实验，以帮助我们熟悉应用程序使用的生命周期函数，并让我们有机会玩弄更多的 Kotlin 代码。

按照以下步骤开始一个新项目，然后我们可以添加一些代码：

1.  开始一个新项目，并选择**Basic Activity**项目模板；这是因为在这个项目中，我们还将研究控制应用程序菜单的函数，而**Empty Activity**选项不会生成菜单。

1.  将其命名为**Lifecycle Demo**。代码在下载包的`Chapter06/Lifecycle Demo`文件夹中，如果您希望参考或复制粘贴它。

1.  保持其他设置与我们所有示例应用程序中的设置相同。

1.  等待 Android Studio 生成项目文件，然后通过在编辑器上方的**MainActivity**标签上单击左键来打开代码编辑器中的`MainActivity.kt`文件（如果默认情况下没有为您打开）。

对于这个演示，我们只需要`MainActivity.kt`文件，因为我们不会构建用户界面。

## 编写生命周期演示应用程序

在`MainActivity.kt`文件中，找到`onCreate`函数，并在闭合大括号（`}`）之前添加两行代码，标志着`onCreate`函数的结束：

```kt
    Toast.makeText(this, "In onCreate", 
                Toast.LENGTH_SHORT).show()

    Log.i("info", "In onCreate")
```

### 提示

请记住，您需要使用*Alt* + *Enter*键盘组合两次来导入`Toast`和`Log`所需的类。

在`onCreate`函数的闭合大括号（`}`）之后，留出一行空白，并添加以下五个生命周期函数及其包含的代码。请注意，我们添加重写的函数的顺序并不重要；无论我们以何种顺序输入它们，Android 都会按正确的顺序调用它们：

```kt
override fun onStart() {
  // First call the "official" version of this function
  super.onStart()

  Toast.makeText(this, "In onStart",
        Toast.LENGTH_SHORT).show()

  Log.i("info", "In onStart")
}

override fun onResume() {
  // First call the "official" version of this function
  super.onResume()

  Toast.makeText(this, "In onResume",
              Toast.LENGTH_SHORT).show()

  Log.i("info", "In onResume")
}

override fun onPause() {
  // First call the "official" version of this function
  super.onPause()

  Toast.makeText(this, "In onPause", 
               Toast.LENGTH_SHORT).show()

  Log.i("info", "In onPause")
}

override fun onStop() {
  // First call the "official" version of this function
  super.onStop()

  Toast.makeText(this, "In onStop", 
              Toast.LENGTH_SHORT).show()

  Log.i("info", "In onStop")
}

override fun onDestroy() {
  // First call the "official" version of this function
  super.onDestroy()

  Toast.makeText(this, "In onDestroy", 
              Toast.LENGTH_SHORT).show()

  Log.i("info", "In onDestroy")
}
```

首先，让我们谈谈代码本身。请注意，函数名称都对应于我们在本章早些时候讨论过的生命周期函数及其相关阶段。请注意，所有函数声明之前都有`override`关键字。另外，请注意每个函数内的第一行代码是`super.on...`。

以下详细解释了正在发生的事情：

+   Android 在我们已经讨论过的各个时间调用我们的函数。

+   `override`关键字表明这些函数替换或重写了作为 Android API 的一部分提供的函数的原始版本。请注意，我们看不到这些被替换的函数，但它们存在，如果我们不重写它们，Android 将调用这些原始版本而不是我们自己的版本。

+   `super.on...`代码是每个重写函数内的第一行代码，然后调用这些原始版本。因此，我们不仅仅是重写这些原始函数以添加我们自己的代码；我们还调用它们，它们的代码也会被执行。

### 注意

对于急切的读者，`super`关键字是用于超类。随着我们在本书中的进展，我们将更多地探讨函数重写和超类。

最后，您添加的代码将使每个函数输出一条`Toast`消息和一条`Log`消息。然而，输出的消息会有所不同，可以通过双引号（`""`）之间的文本看出。输出的消息将清楚地表明是哪个函数产生了它们。

## 运行生命周期演示应用程序

现在我们已经查看了代码，我们可以玩玩我们的应用程序，并从发生的事情中了解生命周期：

1.  在设备或模拟器上运行应用程序。

1.  观察模拟器的屏幕，您将看到以下内容依次出现为`Toast`消息：**在 onCreate**，**在 onStart**和**在 onResume**。

1.  注意 logcat 窗口中的以下消息；如果有太多消息，请记住可以通过将**日志级别**下拉菜单设置为**信息**来过滤它们：

```kt
 info:in onCreate
 info:in onStart
 info:in onResume

```

1.  现在在模拟器或设备上点击返回按钮。注意，您会按照以下确切顺序收到以下三条`Toast`消息：**在 onPause**，**在 onStop**和**在 onDestroy**。验证我们在 logcat 窗口中是否有匹配的输出。

1.  接下来，运行另一个应用程序。也许可以运行第一章中的 Hello World 应用程序，*使用 Android 和 Kotlin 入门*（但任何应用程序都可以），通过在模拟器或设备屏幕上点击其图标来运行。

1.  现在尝试在模拟器上打开任务管理器。

### 提示

如果您不确定如何在模拟器上执行此操作，可以参考第三章中的内容，*探索 Android Studio 和项目结构*，以及在模拟器上使用真实设备部分。

1.  现在您应该能够在设备上看到最近运行的所有应用程序。

1.  点击生命周期演示应用程序，注意通常的三条启动消息会显示；这是因为我们的应用程序以前被销毁。

1.  现在再次点击任务管理器按钮，切换到 Hello World 应用程序。注意，这一次只显示**在 onPause**和**在 onStop**消息。验证我们在 logcat 窗口中是否有匹配的输出；这应该告诉我们应用程序**没有**被销毁。

1.  现在，再次使用任务管理器按钮，切换到生命周期演示应用程序。您会看到只显示**在 onStart**和**在 onResume**消息，表明不需要`onCreate`就可以再次运行应用程序。这是预期的，因为应用程序以前并没有被销毁，而只是停止了。

接下来，让我们谈谈我们运行应用程序时看到的情况。

## 检查生命周期演示应用程序的输出

当我们第一次启动生命周期演示应用程序时，我们看到调用了`onCreate`，`onStart`和`onResume`函数。然后，当我们使用返回按钮关闭应用程序时，调用了`onPause`，`onStop`和`onDestroy`函数。

此外，我们从我们的代码中知道，所有这些函数的原始版本也被调用，因为我们在每个重写的函数中首先使用`super.on...`代码调用它们。

我们应用程序行为的怪癖出现在我们使用任务管理器在应用程序之间切换时，当从生命周期演示应用程序切换时，它并没有被销毁，因此当再次切换回来时，不需要运行`onCreate`。

### 注意

**我的 Toast 在哪里？**

开头的三条和结尾的三条`Toast`消息由操作系统排队，并且函数在它们显示时已经完成。您可以通过再次运行实验来验证这一点，并看到所有三条启动和关闭日志消息在第二条`Toast`消息甚至显示之前就已经输出。然而，`Toast`消息确实加强了我们对顺序的了解，尽管不是时间上的了解。

当您按照前面的步骤进行操作时，可能会得到略有不同的结果。可以肯定的是，当我们的应用在成千上万台不同的设备上由数百万不同的用户运行时，这些用户对与其设备交互的偏好也不同，Android 将在不可预测的时间调用生命周期函数。

例如，当用户通过按下主页按钮退出应用程序时会发生什么？如果我们依次打开两个应用程序，然后使用返回按钮切换到先前的应用程序，那会销毁还是只是停止应用程序？当用户在其任务管理器中有数十个应用程序，并且操作系统需要销毁一些先前仅停止的应用程序时，我们的应用程序会成为受害者吗？

当然，您可以在模拟器上测试所有前面的场景。但结果只对您测试的一次有效。不能保证每次都会表现出相同的行为，当然也不会在每个不同的 Android 设备上表现出相同的行为。

最后，有一些好消息；解决所有这些复杂性的方法是遵循一些简单的规则：

+   设置您的应用程序，以便在`onCreate`函数中准备运行。

+   在`onResume`函数中加载用户的数据。

+   在`onPause`函数中保存用户的数据。

+   在`onDestroy`函数中整理您的应用程序，并使其成为一个良好的 Android 公民。

+   在本书中，有几个场合我们可能想要使用`onStart`和`onStop`，要注意一下。

如果我们遵循前面的规则，我们会发现，在本书的过程中，我们可以简单地不再担心生命周期，让 Android 来处理它。

还有一些其他函数我们也可以重写；所以，让我们来看看它们。

# 一些其他重写的函数

您可能已经注意到，在使用基本活动模板的所有项目代码中，还有另外两个自动生成的函数。它们是`onCreateOptionsMenu`和`onOptionsItemSelected`。许多 Android 应用程序都有弹出菜单，因此在使用基本活动模板时，Android Studio 会默认生成一个，包括使其工作的代码概述。

您可以在项目资源管理器中的`res/menu/menu_main.xml`中查看描述菜单的 XML。XML 代码的关键行如下：

```kt
<item
      android:id="@+id/action_settings"
      android:orderInCategory="100"
      android:title="@string/action_settings"
      app:showAsAction="never" />
```

这描述了一个带有**设置**文本的菜单**项**。如果您运行使用基本活动模板构建的任何应用程序，您将会看到如下截图中所示的按钮：

![一些其他重写的函数](img/B12806_06_01.jpg)

如果您点击按钮，您可以看到它的操作如下：

![一些其他重写的函数](img/B12806_06_02.jpg)

那么，`onCreateOptionsMenu`和`onOptionsItemSelected`函数是如何产生这些结果的呢？

`onCreateOptionsMenu`函数使用以下代码行从`menu_main.xml`文件加载菜单：

```kt
menuInflater.inflate(R.menu.menu_main, menu)
```

它是由`onCreate`函数的默认版本调用的，这就是为什么我们没有看到它发生。

### 注意

我们将在第十七章中使用弹出菜单，*数据持久性和共享*，在我们的应用程序的不同屏幕之间进行切换。

当用户点击菜单按钮时，将调用`onOptionsItemSelected`函数。该函数处理当项目被选中时会发生什么。现在，什么都不会发生；它只是返回`true`。

随意向这些函数添加`Toast`和`Log`消息，以测试我刚刚描述的顺序和时间。

现在我们已经了解了 Android 生命周期的工作原理，并且已经介绍了一些可重写的函数来与这个生命周期进行交互，我们最好学习一下 Kotlin 的基础知识，这样我们就可以编写一些更有用的代码放入这些函数中，并且编写我们自己的函数。

# Kotlin 代码的结构-重新访问

我们已经看到，每次创建新的 Android 项目时，我们也会创建一个新的**包**；这是我们编写的代码的一种容器。

我们还学习了并玩耍了**类**。我们已经从 Android API 中导入并直接利用了类，比如`Log`和`Toast`。我们还使用了`AppCompatActivity`类，但方式与`Log`和`Toast`不同。你可能还记得，到目前为止我们所有项目的第一行代码，在`import`语句之后，使用了`:`符号来继承一个类：

```kt
class MainActivity : AppCompatActivity() {
```

当我们继承一个类时，与仅仅导入它不同，我们正在使它成为我们自己的。事实上，如果你再看一下代码行，你会看到我们正在创建一个新的类，用一个新的名字`MainActivity`，但是基于 Android API 中的`AppCompatActivity`类。

### 注意

`AppCompatActivity`是`Activity`的修改版本。它为较旧版本的 Android 提供了额外的功能，否则这些功能将不存在。关于`Activity`的所有讨论，比如生命周期，同样适用于`AppCompatActivity`。如果名称以`...Activity`结尾，也没关系，因为我们讨论过的和将要讨论的一切同样适用。我通常会简单地将这个类称为`Activity`。

我们可以总结我们对类的使用如下：

+   我们可以导入类来使用它们

+   我们可以继承类来使用它们并扩展它们的功能

+   我们最终可以制作自己的类（并且很快会这样做）

我们自己的类，以及其他人编写的类，都是我们代码的构建模块，类中的函数包装了功能代码 - 也就是执行工作的代码。

我们可以在扩展的类中编写函数，就像我们在第二章中所做的`topClick`和`bottomClick`一样，*Kotlin，XML 和 UI 设计师*。此外，我们重写了其他人编写的类中已经存在的函数，比如`onCreate`和`onPause`。

然而，我们在这些函数中放入的唯一代码是使用`Toast`和`Log`进行了几次调用。现在我们准备用 Kotlin 迈出更多的步伐。

# 总结

在本章中，我们学到了不仅我们可以调用我们的代码；操作系统也可以调用我们重写的函数中包含的代码。通过向各种重写的生命周期函数添加适当的代码，我们可以确保在正确的时间执行正确的代码。

现在我们需要做的是学习如何编写更多的 Kotlin 代码。在下一章中，我们将开始专注于 Kotlin，并且因为我们已经在 Android 上有了很好的基础，所以练习和使用我们学到的一切都不会有问题。