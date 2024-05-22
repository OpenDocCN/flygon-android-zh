# 第八章：性能和内存优化

优化是任何开发周期中最重要的任务之一。尤其是对于游戏来说是不可避免的。游戏优化显著提高了性能。通过优化，可以针对更多的硬件平台。

您已经了解到安卓支持一系列硬件平台。每个平台都有单独的配置。通过优化硬件资源的使用，游戏可以在更多的硬件平台上运行。这种技术也可以应用于视觉质量。并非所有设备都具有相同的显示质量，因此为低分辨率优化资源可以节省大量的存储空间以及运行时的堆内存。

在编程中，开发人员经常编写中间代码，然后忘记对其进行优化。这可能导致大量的性能损失，甚至导致游戏崩溃。

我们将通过以下主题讨论安卓游戏开发中各种优化的范围：

+   安卓游戏中的优化领域

+   性能和内存管理之间的关系

+   安卓中的内存管理

+   安卓中的处理段

+   不同的内存段

+   内存优化的重要性

+   性能优化

+   增加帧率

+   性能优化的重要性

+   常见的优化错误

+   最佳优化实践

# 安卓游戏中的优化领域

我们都知道在任何开发项目中都需要优化。在游戏开发的情况下，这一事实仍然如此。在游戏开发项目中，流程始于有限的资源和设计。开发后，预期游戏能在尽可能多的设备上以最高质量运行。为了实现这一点，内存和性能优化变得必不可少。因此，让我们讨论以下四个优化领域：

+   资源优化

+   设计优化

+   内存优化

+   性能优化

## 资源优化

资源优化基本上是优化艺术、声音和数据文件。

### 艺术优化

我们已经讨论了许多优化技术和工具。在这里，我们将讨论艺术优化的必要性。

艺术在游戏中在视觉上是最重要的部分。提高艺术品的显示质量会增加处理和存储成本。

大纹理占用大量内存。然而，将艺术放大以适应更大分辨率的屏幕会影响视觉质量。因此，必须取得平衡。此外，各种安卓设备对纹理大小有各种限制。此外，着色器在较大纹理上的工作需要更多时间。

开发人员常犯的一个错误是对完全不透明的纹理使用 alpha 信息。这会显著增加纹理大小。

艺术资源可以根据艺术风格进行优化。许多开发人员使用单色纹理而不是渐变色。单色信息可以容纳在 8 位像素数据中。这再次节省了磁盘空间和处理时间。

尽管存在这些优化范围，开发人员可能不会全部使用，以增加灵活性，从而在不花费太多时间进行优化的情况下创建高质量的视觉艺术。

### 声音优化

声音是游戏中另一个重要的资源。音频可以被压缩以节省空间和精力。在安卓游戏行业中，一种常见做法是对长音频文件使用压缩格式。

在运行时压缩和解压文件需要时间。因此，如果 SFX 被压缩，动态使用可能会成为问题。它可能会触发显著且可见的卡顿。开发人员喜欢对 SFX 使用未压缩格式，并对长时间连续播放的声音（如背景音乐）使用压缩格式。

### 数据文件优化

有时，游戏开发人员使用单独的数据文件来创建灵活的项目结构，以便与外部工具交互或获得更好的数据接口。这些文件通常以文本、XML、JSON 或二进制格式存在。开发人员可能会创建自己的二进制模型数据格式。

如果使用正确的算法，二进制数据可以快速处理。数据优化并没有太多技术性。然而，开发人员始终需要检查数据量和总文件大小。

## 设计优化

设计优化用于增加游戏的可扩展性、质量体验、灵活性和耐久性。主要方法是围绕核心游戏概念重组或修改游戏参数。

让我们从功能的角度将这一部分分为两部分：

+   游戏设计优化

+   技术设计优化

### 游戏设计优化

游戏在游戏设计优化阶段可能与最初的想法完全不同。设计优化是基于某些任务进行的。开发人员需要找到不同的方式来传达基本的游戏理念。然后，他们可以选择最佳的方式，进行一些分析。

游戏设计应该足够灵活，以适应运行时变化，以改善整体体验并增加用户数量。高度优化的游戏设计足够高效，可以预测用户行为、各种设备上的游戏性能，甚至货币化。

游戏控制系统设计必须足够优化，以便轻松完成所有任务。游戏控制应该易于发现和理解。对于 Android 触摸设备，控件的放置也非常重要。

### 技术设计优化

技术设计优化仅限于开发周期。它设置了项目结构、程序结构、开发平台依赖等。

技术设计文档还规定了游戏的范围和规模。这些规范有助于在设备上顺利运行游戏，因为硬件平台已经在技术设计文档中涵盖。

这是一个预开发过程。这份文件需要处理一些假设。这些假设应该足够优化，以便在实时情况发生时进行演变。

技术设计还可以在开发过程中处理以下任务。通过优化这些任务，实施和执行将更加容易：

+   程序架构

+   系统架构

+   系统特性

+   定义的依赖关系

+   影响

+   风险分析

+   假设

所有这些任务都可以优化，以便更轻松地进行更好的开发周期，游戏将更加精致，并且性能更高。

## 内存优化

内存优化对于任何软件开发过程都是必需的。内存根据硬件配置有其物理限制，但游戏和应用程序不能为每个设备单独制作。

在技术设计中，应该提到游戏在所有目标硬件平台上的内存使用范围。现在，游戏占用的内存超出预期是非常常见的情况，最终导致游戏崩溃。开发人员会收到内存溢出异常。

为了避免这种情况，有两个主要的事情需要注意：

+   保持内存峰值在定义范围内

+   不要在内存中保留不必要的数据加载

Android 使用分页和映射来管理内存使用。不幸的是，它不提供内存交换。Android 知道在哪里找到分页数据并相应地加载。

以下是一些优化 Android 游戏内存的技巧。

### 不要在运行时创建不必要的对象

开发人员经常在循环内创建中间数据对象。这会留下内存印记，供垃圾收集器收集。以下是一个例子：

```kt
//Let's have an integer array list and fill some data
List<int> intListFull = new ArrayList<int>();
//Fill data
for( int i = 0; i < 10; ++ i)
{
  intListFull.add(i);
}

// No we can have two different approach to print all 
// values as debug log.
// Approach 1: not optimized code
for ( int i = 0; i < intListFull.size() ; ++ i)
{
  int temp = intListFull.get(i);
  Log.d("EXAMPLE CODE", "value at " + i + " is " + temp);
}
// List size will be calculated in each cycle, temp works 
//as auto variable and create one memory footprint in each 
//loop. Garbage collector will have to clear the memory. 

// Approach 2: optimized code
int dataCount = intListFull.size();
int temp;
for ( int i = 0; i < dataCount ; ++ i)
{
  temp = intListFull.get(i);
  Log.d("EXAMPLE CODE", "value at " + i + " is " + temp);
}
// only two temporary variable introduced to reduce a foot 
//print in each loop cycle.
```

### 尽可能使用基本数据类型

用户定义的数据类型比原始数据类型占用更多的内存空间。声明一个整数所占用的空间比将整数嵌入类中要少。在 Android 中，如果开发人员使用`Integer`类而不是`int`，数据大小会增加四倍。

对于 Android 编译器（32 位），`int`消耗 4 字节（32 位），而`Integer`消耗 16 字节（128 位）。

对于现代 Android 设备，有限使用这种数据类型可能对内存没有显著的影响。然而，大量使用非原始数据类型可能会导致大量的内存块，直到开发人员或垃圾收集器释放内存。

因此，开发人员应避免使用`enum`，而是使用静态最终的`int`或`byte`。`enum`作为用户定义的数据类型，比原始数据类型占用更多的内存。

### 不要使用未管理的静态对象

在旧版 Android 中，静态对象不会自动销毁是一个常见问题。开发人员过去需要手动管理静态对象。在新版 Android 中，这个问题已经不存在。然而，在游戏中创建许多静态对象并不是一个好主意，因为静态对象的寿命等于游戏的寿命。它们会长时间地直接阻塞内存。

使用太多静态对象可能导致内存异常，最终导致游戏崩溃。

### 不要创建不必要的类或接口

每个类或接口在其实例中都有一些额外的绑定空间。模块化编程方法要求在编码结构中尽可能多地进行分解。这与类或接口的数量成正比。这被认为是一种良好的编程实践。

然而，这对内存使用有影响。更多的类消耗更多的内存空间来存储相同数量的数据。

### 使用最小可能的抽象

许多开发人员在多个层次上使用抽象以获得更好的编程结构。限制自定义库的某一部分并仅提供选择性的 API 非常有用。在游戏开发方面，如果开发人员只开发游戏，那么抽象的使用并不是非常必要的。

抽象导致更多的指令，直接导致更多的处理时间和更多的内存使用。因此，即使抽象有时可能很方便，开发人员在开发游戏时使用抽象之前应该三思而后行。

例如，一个游戏可能有一组不同的敌人。在这种情况下，创建一个单一的敌人接口，并为不同的敌人对象实现它，有助于创建一个简单和方便的程序层次结构。然而，不同的敌人可能有完全不同的属性。因此，抽象的使用将取决于游戏设计。无论情况如何，如果开发人员使用抽象，那么它将始终增加要在运行时处理的指令集。

### 对服务进行检查

服务对于在后台完成一个任务非常有用，但在处理和内存方面非常昂贵。除非必要，开发人员不应该保持服务运行。自动管理服务生命周期的最佳方法是使用`IntentService`，它在工作完成后将结束。对于其他服务，开发人员有责任确保在任务完成后调用`stopService`或`stopSelf`。

这个过程对游戏开发非常有效，因为它积极支持用户和开发人员之间的动态交流。

### 优化位图

位图是游戏中最重的资源。在游戏开发中，大部分堆内存都被位图使用。因此，优化位图可以显著优化运行时堆内存的使用。

通常，将位图加载到内存所需的内存由以下公式给出：

*BitmapSize = BitmapWidth * BitmapHeight * bytePerPixel*

例如，如果以`ARGB_8888`格式（4 字节）加载一个 480 x 800 大小的位图，内存将如下：

*位图大小 = 480 x 800 x 4 = 1536000 字节〜1.5mb*

在 Android 中，格式可以是以下类型之一：

+   `ARGB_8888`（4 字节）

+   `RGB_565`（2 字节）

+   `ARGB_4444`（2 字节）（在 API 级别 13 中已弃用）

+   `ALPHA_8`（1 字节）

每个位图将根据前述公式占用内存。因此，建议根据需要将位图加载到内存中，以避免不必要的堆使用。

### 释放不必要的内存块

就像我们之前讨论释放内存一样，同样的方法也可以应用于任何对象。任务完成后，实例应设置为 null，以便垃圾收集器可以识别并释放分配的内存。

在游戏状态机中，类结构应提供一个接口来释放实例化对象的内存。可能会出现这样的情况，其中一些成员对象已完成其任务，而另一些仍在使用中，因此等待整个类实例被释放是一个坏主意。开发人员应选择性地释放未使用对象的内存，而不删除类实例。

### 使用 zipalign 和 ProGuard 等外部工具

ProGuard 工具通过删除未使用的代码和使用安全和编码的命名结构重命名类、字段和方法，有效地缩小、优化和混淆代码。ProGuard 可以使代码更加紧凑，这直接影响 RAM 的使用。

在游戏开发中，开发人员经常使用许多第三方库，这些库可能已经使用 ProGuard 预编译。在这些情况下，开发人员必须配置 ProGuard 以排除这些库。还可以保护代码库免受盗窃。

zipalign 可用于重新对齐发布的 APK。这进一步优化 APK，以使用更少的空间并具有更紧凑的大小。通常，大多数 APK 构建框架会自动提供 zipalign。但是，开发人员可能需要在少数情况下手动使用它。

## 性能优化

性能意味着游戏在目标平台上运行的流畅程度，并在整个游戏过程中保持良好的 FPS。在 Android 游戏的情况下，我们已经了解到各种硬件配置的广泛范围。在所有设备上保持相同的性能实际上是不可能的。这就是开发人员选择目标硬件和最低硬件配置的原因，以确保游戏的性能足够好以发布。但是，期望也因设备而异。

在实际开发约束中，性能优化受限于目标硬件集。因此，内存在开发过程中有自己的优化空间。

从编程角度来看，性能优化可以通过更加关注编写和构造代码来实现：

+   尽量每个任务使用最少的对象

+   尽量减少浮点数的使用

+   尽量减少抽象层

+   尽可能使用增强循环

+   避免为内部使用的变量使用 getter/setter

+   对常量使用 static final

+   尽量减少内部类的使用

### 尽量每个任务使用最少的对象

创建不必要的对象会增加处理开销，因为它们必须在新的内存段中初始化。多次使用相同对象执行相同任务要快得多。以下是一个例子：

```kt
public class Example
{
  public int a;
  public int b;

  public int getSum()
  {
    return (a + b);
  }
}
//Lets have a look on un-optimized code
// Here one object of Example class is instantiating per loop //cycle  // Same is freed and re-instantiated
public class ExecuterExample
{
  public ExecuterExample()
  {
    for ( int i = 0; i < 10; ++ i)
    {
      Example test = new Example();
      test.a = i;
      test.b = i + 1;
      Log.d("EXAMPLE", "Loop Sum: " + test.getSum());
    }
  }
}
// Optimized Code would look like this
// Here only one instance will be created for entire loop
public class ExecuterExample
{
  public ExecuterExample()
  {
    Example test = new Example();
    for ( int i = 0; i < 10; ++ i)
    {
      test.a = i;
      test.b = i + 1;
      Log.d("EXAMPLE", "Loop Sum: " + test.getSum());
    }
  }
}
```

### 尽量减少浮点数的使用

在机器级语言中，不存在整数或浮点数。它总是表示真或假的位（在技术语言中为 0 和 1）。因此，整数可以直接由一组位表示，但浮点数需要额外的处理开销。

直到某个时间点，编程语言中没有使用浮点数。后来，转换出现了，并且引入了额外的处理要求。

### 尽量减少抽象层

很明显，抽象需要每个层面额外的处理。因此，随着抽象层的增加，处理变得更慢。

### 尽可能使用增强循环

在数组和列表解析的情况下，增强的`for`循环比通常的传统`for`循环要快得多，因为它没有迭代变量系统，可以直接访问每个数组或列表元素。

这是一个非增强循环的例子：

```kt
int[] testArray = new int[] {0, 1, 2, 3, 5};
for (int i = 0; i < testArray.length; ++ i)
{
  Log.d("EXAMPLE", "value is " + testArray[i]);
}
```

这是一个增强循环的例子：

```kt
int[] testArray = new int[] {0, 1, 2, 3, 5};
for (int value : testArray)
{
  Log.d("EXAMPLE", "value is " + value);
}
```

### 避免内部使用变量的 getter/setter

使用 getter 和 setter 来访问或更改对象的任何内部元素的状态。在高级推理中，它不遵循数据封装的基本概念。然而，在 Android 游戏开发中广泛使用 getter 和 setter。

在许多情况下，开发人员从类对象内部使用 getter 和 setter。这不必要地增加了处理时间，导致性能下降。因此，开发人员应尽量少使用 getter 和 setter，并确保它们不被内部使用。

### 使用静态 final 来定义常量

常量在运行时不应更改。对于全局常量，数据直接与类对象相关联。因此，我们需要解析类对象才能访问它。

使用静态是一个很好的主意，可以摆脱这个额外的进程。使用静态常量可以显著增加元素的可访问性。然而，开发人员也需要检查内存使用情况。

### 尽量少使用内部类

每个内部类都会增加一层处理。有时，为了以高效和可读的方式构建代码库，使用内部类是很好的。然而，这会增加处理开销。因此，开发人员应尽量少使用内部类以优化性能。

# 性能和内存管理之间的关系

在 Android 游戏开发中，性能和内存优化经常相互冲突。为了保持游戏的视觉质量，必须使用更好的艺术资源，这最终会增加内存开销和性能滞后。

优化内存需频繁进行内存操作，导致性能下降。为了提高性能，对象必须能够顺利处理。显然，两者都不能达到极端水平。

在它们之间取得平衡是优化整个游戏以顺利运行而不耗尽内存的唯一方法。

# Android 中的内存管理

让我们讨论一下 Android 中的内存管理系统。它直接影响游戏开发过程。在 Android 中，游戏被视为应用程序。开发人员经常在游戏的运行时和最小化状态下遇到内存问题。有三个主要主题需要讨论以了解工作原理：

+   应用程序共享内存

+   内存分配和释放

+   应用程序内存分配

## 应用程序共享内存

Android 使用 Linux 内核，Linux 使用“共享”页面在运行进程或服务之间共享相同的内存段。例如，Android 经常在进程之间共享“代码”内存。很多时候，外部库和 JVM 的可执行代码内存可以安全地在进程之间共享，而不会创建死锁。数据页面可以在进程之间暂时共享，直到一个进程修改了共享内存。

Android 为每个应用程序或进程分配专用内存。这称为私有内存。同一进程也可能使用共享内存。Android 会自动设置一个上限，取决于两者的总和，以确定进程或应用程序何时会被终止，特别是在后台运行时。这个上限称为**比例设置大小**（**PSS**）：

![应用程序共享内存](img/B05069_08_01.jpg)

如果一个应用的 PSS 很高，那么这个进程可能被 Android 杀死的可能性很高。特别是如果应用程序依赖一些后台活动或服务来执行某些任务，这种情况可以通过编程方式处理以保持内存使用情况，开发人员必须确保游戏在任何时候使用的内存尽可能少，特别是当应用程序进入后台时。在后台释放不再需要的内存和对象，并在进入后台时断开不再需要的任何共享内存可能是一个不错的主意。这将减少您的应用程序被 Android 系统意外杀死的机会。

## 内存分配和释放

Android 内存管理系统为每个应用程序定义了一个虚拟上限，即逻辑堆大小。如果有必要，可以增加这个逻辑堆大小，但前提是有空闲内存可用。然而，这个逻辑堆大小并不是应用程序的实际分配内存。计算得到的 PSS 是在运行时可能变化的实际物理上限，并且存在共享内存依赖。

应用程序内存不能使用比 PSS 更多的物理内存。因此，在达到此限制后，如果应用程序尝试分配更多内存，那么它将收到系统抛出的`OutOfMemoryError`。在紧急情况下，Android 可能会杀死其他空闲或后台进程以为运行中的应用程序腾出内存。在这些情况下，应用程序内存将被释放：

+   如果应用程序退出

+   如果进程变得不活动，而其他进程需要内存

+   如果应用程序因任何原因崩溃

## 应用程序内存分配

Android 为每个应用程序设置了堆大小的硬限制，以维持多任务环境。根据设备的 RAM 容量，确切的堆大小限制因硬件配置而异。如果应用程序达到堆容量并尝试分配更多内存，它将收到`OutOfMemoryError`，并且应用程序将被 Android 杀死。

开发人员需要检查设备上可用内存的数量，然后确定平均目标内存使用量。开发人员可以通过调用`getMemoryClass()`来查询操作系统的内存量。这将返回一个整数，表示应用程序堆可用的 MB 数。

# Android 中的处理段

游戏基本上是功能上的一个应用程序。多个应用程序或游戏可以在 Android 平台上运行。但是，对于游戏来说，一次只有一个游戏是活动的，而其他应用程序在后台运行。

让我们看看 Android 如何处理其应用程序。

## 应用程序优先级

Android 设置了运行应用程序的优先级，并且根据需求可以杀死低优先级的运行应用程序。

每个应用程序都使用一些内存和处理带宽。可能出现多个应用程序同时运行的情况。如果一个新应用程序想要运行，那么 Android 会为新应用程序分配内存和处理带宽。如果没有足够的带宽或可用的处理，那么 Android 会杀死一个或多个优先级较低的运行中的应用程序。

Android 通过以下状态设置优先级：

+   活动进程

+   可见进程

+   活动服务

+   后台进程

+   空进程![应用程序优先级](img/B05069_08_02.jpg)

### 活动进程

活动进程基本上是指与平台频繁通信并在前台运行的进程。当必要时，这个进程是 Android 中最后一个被杀死的。

活动进程满足以下标准：

+   它在前台运行

+   它是可见的

+   至少有一个 Android 活动正在运行

+   它与用户界面积极交互

+   所有事件处理程序处于活动状态

### 可见进程

这个过程基本上是一个活动的进程，不在前台，也不与用户界面交互。这是 Android 平台的第二高优先级。

这个过程的标准如下：

+   它在后台运行

+   它有可见的活动

+   它不与用户界面交互

+   UI 事件处理程序不活动

+   进程事件处理程序是活动的

### 活动服务

活动服务是支持进行中的进程而没有可见界面的服务。Android 将首先终止这些服务，然后才是实际的活动进程。

此服务遵循以下标准：

+   它没有可见的界面

+   它支持或为相应的活动进程工作

+   它在后台运行

### 后台进程

后台进程基本上是最小化或不活动的进程。这些进程在屏幕上不可见。这些进程的线程不运行，但应用程序状态保存在内存中。这些进程容易被处理器终止。这些进程在中断后可以恢复。

这些是不活动/最小化的进程。它们保留在内存中。应用程序保持暂停状态。

### 空进程

空进程也称为空进程。空进程实际上是空的。它在内存中不保存任何应用程序数据或状态。这个进程有最高的优先级，以便被操作系统终止。

## 应用程序服务

Android 应用服务是实际应用程序过程的一部分。这些服务可以在父进程内部和外部运行。

让我们澄清关于服务的两个常见误解：

+   服务不是一个独立的进程

+   服务不是线程

事实上，服务是应用程序过程的一部分，而不是独立的进程。服务不是线程。它们是在后台运行的进程的一部分，即使主应用程序处于暂停状态，它们也会继续运行。

服务旨在执行单个任务，不会回调父应用程序。这就是为什么它们可以在应用程序关闭后继续运行。

### 服务生命周期

服务由父应用程序进程启动，如下所示：

```kt
Context.startService();
```

启动后，服务开始在后台执行单个任务。任务完成后，服务可以停止自己。例如，简单的文件下载服务在成功下载任务后会停止。许多游戏开发者在他们的游戏中使用这些功能来提高用户体验。

这些服务可以与一个或多个进程绑定以实现交互。应用程序可以向绑定的服务发送请求并获得响应，从而创建服务器-客户端架构。但这些绑定的服务在最后一个应用程序组件绑定到服务之前有限的生命周期。

## 资源处理

Android 有自己的资源处理结构。它有一些预定义的资源类型：

+   可绘制资源

+   布局资源

+   颜色资源

+   菜单资源

+   Tween 动画资源

+   其他资源

### 可绘制资源

所有可绘制资源都属于这个类别，包括帧动画。Android 提供了专门用于所有可绘制资源的`res/drawable/`项目路径。所有位图、各种 XML 和预定帧动画都可以放在这里。

这些可以通过`R.drawable`类访问。

### 布局资源

所有定义的布局都属于这个类别。Android 提供了专门用于所有布局文件的`res/layout/`项目路径。布局对于定义应用程序 UI 非常有用。

这些可以通过`R.layout`类访问。

### 颜色资源

颜色资源基本上是一个颜色列表，随着适用对象视图的改变而改变。Android 将其存储在层次结构中的`res/color/`文件夹中。

这些可以通过`R.color`类访问。

### 菜单资源

所有菜单内容都可以在这里定义。Android 提供了专门用于所有**drawable**资源的`res/menu/`项目路径。

这些可以通过`R.menu`类访问。

### Tween 动画资源

所有 Tween 动画资源都属于这个类别。Android 提供了专门用于所有 Tween 动画资源的`res/anim/`项目路径。

这些可以通过`R.anim`类访问。

### 其他资源

所有其他资源都放在`res/values/`文件夹中。许多开发人员在这个类别下定义了样式的字符串。

这些可以通过`R.values`类访问。

# 不同的内存段

在应用程序运行时，根据行为使用了三种主要的内存段：

+   堆栈内存

+   堆内存

+   寄存器内存

## 堆栈内存

所有自动变量和处理过程中的运行时分配将存储在堆栈内存段中。垃圾收集器在使用后释放内存。因此，与堆栈内存相关联的没有手动内存管理过程。

然而，大量使用自动变量也可能导致内存错误。这就是为什么我们已经讨论过为什么最小化不必要的自动变量声明是必要的原因。

堆栈内存也用于执行程序指令。每个指令都被解析为一个单独的操作，并由解释器放入堆栈中。然后，使用递归过程来执行所有指令堆栈并返回结果。

让我们看看堆栈内存如何处理对象和基本类型：

```kt
public class ExampleClass
{
  public ExampleClass()
  {
    int bitMapCount = 0;  // primitive type
    Bitmap testBmp = BitmapFactory.decodeFile("bitmap path"); // Object loading
    bitMapCount = 1;
  }
}
```

在这个例子中，`bitMapCount`是一个`int`局部变量，直接存储在堆栈中。这个变量使用的内存将在作用域结束后立即被释放。

然而，`testBmp`是一个位图对象，将被分配在堆中，但引用将存储在堆栈中。当程序指针离开作用域时，引用将被自动删除，垃圾收集器可以识别为`testBmp`分配的堆内存具有零引用，并释放这个内存段。

## 堆内存

堆内存是存储所有类实例和数组实例的段。JVM 在实例化任何对象时分配这个内存。

垃圾收集器在应用程序运行时不会自动操作这个内存段。开发人员有责任在使用后释放内存。在 Android 的情况下，只有在运行应用程序中没有对内存段的引用时，垃圾收集器才会释放内存。

游戏资源是存储在这个内存段中的主要元素。艺术是其中最重要的资产。因此，优化位图对堆内存的使用有直接影响。开发人员经常为资产分配内存并且不会打破引用。这导致内存块在整个运行时期被占用。

以下是一个例子：

```kt
// create a bitmap in a class constructor having global
// scope with public access
public class ExampleClass
{
  public Bitmap testBmp;
  public ExampleClass()
  {
    testBmp = BitmapFactory.decodeFile("bitmap path");
  }
}
```

在这个例子中，位图的内存将在使用后仍然被占用，直到`ExampleClass`实例在内存中。解释器没有立即释放内存段的指令，因为`testBmp`仍然引用了分配给位图的内存。

我们可以通过一些修改以以下方式优化这个问题：

```kt
public class ExampleClass
{
  public Bitmap testBmp;
  public ExampleClass()
  {
    testBmp = BitmapFactory.decodeFile("bitmap path");
  }
  // create a method to free memory allocated for the
  // bitmap after use
  public void unloadBitmap()
  {
    testBmp = null;
  }
}
```

在这种情况下，在使用位图后调用`unloadBitmap()`将从`testBmp`中删除加载的位图的引用。因此，垃圾收集器将找到这个内存位置为零引用内存，并释放它以供其他分配使用。

## 寄存器内存

在 Android 开发中，开发人员不必担心寄存器内存。寄存器直接与处理器相关联，处理器将最重要和经常使用的数据存储在这个内存段中。

寄存器内存是任何应用程序运行时使用的最快的内存段。

# 内存优化的重要性

无论游戏如何，外观如何，设计得多好，如果游戏在目标平台上无法运行，那么它就不会成功。我们已经知道 Android 有各种硬件配置。

硬件的主要变化是特定于处理器和内存。在处理器方面，这取决于其速度和质量。在内存或 RAM 方面，只取决于容量。

即使在今天，Android 设备的 RAM 也可以从 512MB 到 4GB 不等。内存优化应该始终以设计为目标的最低 RAM 为目标。因此，内存优化对于在最小可用 RAM 上运行游戏非常重要。

有时，开发人员将峰值使用量限制在目标内存限制内。然而，他们在测试设备上执行，这大部分时间并不反映实时情况。总是存在误差范围。因此，并不总是真实情况下游戏在一定的 RAM 限制下运行时，总是能提供相同的内存。这就是内存优化发挥重要作用的地方。它在创建游戏在实时情况下运行的缓冲范围方面起到了很大作用。

可能存在这样的情况，即使应用程序不需要所需的 RAM 量，也会耗尽内存。这清楚地表明应用程序存在内存泄漏问题。内存泄漏是游戏开发中最常见的问题之一。正确优化内存有助于解决这个问题。

内存优化的另一个方面是增加游戏保持在后台的概率。当应用程序进入后台时，如果 Android 需要为其他前台应用程序释放内存空间，可能会杀死应用程序。内存优化确保应用程序在运行时占用尽可能少的内存。因此，对于使用较少内存的应用程序，可以将状态数据保存在缓存中更长时间。

许多游戏在后台使用游戏服务。如果应用程序不活动，那么服务也有可能被操作系统杀死。

# 优化整体性能

早些时候，我们仅从编程角度讨论了性能优化。让我们讨论 Android 游戏性能优化的其他方面。

开发人员可以通过以下几点从设计到开发优化性能：

+   选择基本分辨率

+   定义可移植范围

+   程序结构

+   管理数据库

+   管理网络连接

## 选择基本分辨率

从 Android 游戏开发的角度来看，选择基本分辨率可能是最重要的设计决策。基本分辨率定义了图形或视觉元素的比例。开发人员选择的分辨率越大，存储和处理时间就越多。基本分辨率还负责存储位图的质量和颜色信息。相对较低的分辨率不需要在可见资产中包含许多细节，这可以优化位图数据。然而，随着分辨率的增加，需要更多的数据来保留细节。最终，这对处理有重要影响。

随着技术的进步，Android 设备的分辨率越来越大。因此，开发人员现在选择更大的分辨率来支持更高范围的设备。

## 定义可移植范围

这也是设计阶段的优化。在这个阶段，开发人员需要决定支持的硬件平台范围。这包括各种配置。我们已经知道，Android 设备范围包括在内存、处理速度、图形质量等方面存在大量变化。

如果范围支持类似设备的可移植范围，那么优化变得更容易。然而，这并不适用于大多数游戏开发情况。通常，开发人员应将优化分为三个部分：

+   低性能设备

+   平均性能设备

+   高性能设备

因此，理想情况下，应该有三层优化来正确定义可移植范围。

## 程序结构

程序结构是另一个非常重要的技术设计决策，对于性能和内存优化都很重要。这包括我们已经讨论过的所有编程优化参数。

此外，程序层次结构对性能也很重要。通常，开发人员会创建不必要的中间调用来解析多个层。一些单例类在这里有助于显著优化性能。正确的游戏状态机设计也有助于优化性能。

## 管理数据库

有许多游戏主要是数据驱动的。在这种情况下，需要正确管理数据库。

例如，一个问答游戏必须在数据库中维护一个问题库，以避免频繁更新游戏构建。数据库查询需要时间来执行，因为中间还有一个网络层。因此，游戏层发送查询到数据库。然后，数据库获取数据，相应地绑定并发送回游戏。然后，游戏必须解绑接收到的数据以使用它。使用最少的查询调用是最小化性能开销的唯一方法。使用更快的数据库也有助于游戏表现良好。

## 管理网络连接

现代游戏已经发展到多人游戏和服务器控制机制，这减少了频繁更新游戏构建的工作。在这两种情况下，网络连接需要以适当的方式实现。目前主要有两种类型的多人游戏架构：

+   回合制多人游戏

+   实时多人游戏

相对来说，管理回合制多人游戏系统比实时多人游戏系统要容易。还有另一种名为异步多人游戏的多人游戏模式。

每个网络调用都会导致性能延迟，因为游戏依赖于来自服务器的数据。因此，客户端-服务器架构需要优化，以实现以下目标：

+   较少的延迟时间

+   较少的层处理

+   较少的对服务器的 ping 次数

# 增加帧率

性能优化的最终目标是提高帧率。高帧率自动提供流畅的游戏体验。然而，开发人员必须确保帧率效果在游戏中以流畅和效果的形式可见。

对于当前的移动游戏行业来说，2D 游戏或中等规模的 3D 游戏的平均 FPS 为 60 被认为是高性能。另一方面，大型 3D 游戏可能认为平均 FPS 为 30-35 是良好的性能。

具有更高 FPS 的高性能游戏为改善用户体验提供了进一步的视觉效果。这直接影响货币化。

# 性能优化的重要性

正如我们刚刚讨论的，性能优化直接影响帧率，而帧率又直接影响游戏体验。然而，性能优化还有其他重要性：

+   由于非优化的程序，游戏可能会崩溃或进入无响应状态

+   性能优化也直接影响内存

+   性能优化可以扩大支持的硬件平台范围

# 常见的优化错误

游戏行业现在是增长最快的行业之一。为了跟上速度并在市场上站稳脚跟，许多公司计划缩短开发周期并限制优化。在这种情况下，开发人员通常会有意或无意地犯以下错误：

+   编程错误

+   设计错误

+   错误的数据结构

+   错误使用游戏服务

## 编程错误

编程是一个手动的过程，犯错是人之常情。因此，显而易见，没有错误和完全优化的游戏编码。然而，程序员可以通过几种方式最小化错误，以获得优化的游戏代码库。让我们讨论在 Android 开发游戏时程序员犯的主要错误。

程序员经常创建许多临时变量并忘记跟踪它们。通常，这些变量会占用不必要的内存并增加处理调用。

排序在游戏开发中被广泛使用。有几种排序算法。大多数情况下，开发者选择方便的技术而不是高效的技术。对于大型数组或列表，这可能会导致严重的流程延迟。

为了方便访问，使用太多静态实例也是一个不良的实践。使用静态可能会加快处理速度，但制作太多静态实例并不是一个好主意，因为它会在其生命周期内占用大量内存空间。许多程序员甚至忘记手动释放这些内存。

创建抽象层并广泛使用它们会使流程变慢。然而，这通常是一个良好的编程实践，但对于游戏编程来说，只在有限的情况下有所帮助。

方便的循环使用是游戏中另一个不良的编程实践。有几种处理循环的方法。程序员应该首先确定哪种方法最适合算法。

游戏编程大多是关于逻辑开发而不是技术。为某些任务建立完美的逻辑可能需要时间。许多游戏程序员并不考虑执行一个任务的多种方式。大多数情况下，这会留下很大的优化空间未被发掘。

## 设计错误

设计师在定义硬件范围和游戏范围时经常犯错误。这两个因素都是创建优化游戏设计的非常重要的因素。

另一个错误是将目标分辨率定错。目标分辨率直接影响艺术资源的大小。定错目标分辨率会导致不必要的缩放，增加额外的处理开销。

## 错误的游戏数据结构

数据结构是游戏编程中不可避免的一部分。Android 支持动态数组初始化。然而，许多开发人员更喜欢使用列表来存储数据。列表比数组慢得多。只有在绝对必要时才应该使用列表。

为数据驱动的游戏找到完美的数据结构是开发者的责任。适当的技术设计应该包括数据结构模型及其使用。

## 错误使用游戏服务

服务在某些时候非常有用。在现代游戏行业中，服务用于数据的下载/上传，推送通知，游戏中的深度链接，或者服务器连接。然而，服务会带来巨大的处理和内存消耗成本。运行服务也会导致大量的能耗。

因此，只有在没有其他办法时才应该强制使用服务。

# 最佳优化实践

有一些定义明确且合乎逻辑的优化技术可供选择。我们将讨论与 Android 游戏开发相关的主要范围和领域：

+   游戏设计约束

+   游戏开发优化

+   游戏数据结构模型

+   使用游戏资源

+   处理缓存数据

## 设计约束

定义目标硬件平台并承认其限制总是最佳实践。技术设计可以根据它来构建开发约束。

可扩展性和可移植性也应该在设计游戏时确定。这应该给开发者一个初步的平台限制以及其他约束。我们已经讨论了设计优化。在进入开发之前，所有这些部分都应该进行评估。

在设计游戏时，需要固定目标屏幕大小和分辨率，并创建适合多种分辨率的布局。这是因为 Android 有许多不同的屏幕大小，正如前面所讨论的。

选择最低的 Android 版本和目标 Android 版本在构建开发项目时给开发者带来了优势，因为支持的 API 级别和平台功能已经定义好了。

## 开发优化

这是优化中最重要的部分之一。以下是一些成功进行优化开发过程的提示：

+   使用尽可能多的安卓提供的文件夹结构，以便项目可扩展性。

+   根据安卓提供的 dpi 列表使用资源格式。

+   开发人员应避免缩放图像。这有效地减少了内存和处理开销。

+   对于多种用途使用精灵也是创建动画的良好实践。

+   平铺技术在减少内存消耗方面非常有用。

+   覆盖`onDraw()`方法始终是刷新旧渲染管道并使用系统化绘制顺序的良好实践。

+   尽可能使用基于 XML 的布局；然而，游戏对于这个安卓功能的使用范围非常有限。

## 数据结构模型

数据结构是游戏程序设计中不可避免的部分，无论游戏的规模如何。每个游戏都会处理各种目的的数据，如排序、搜索、存储等。

有许多数据结构模型可用于各种操作。每种操作都有其优点和缺点。开发人员必须根据需求选择最有效的方法。

让我们以数组和链表之间的数据存储比较为例。实际上，链表比数组更灵活和动态。然而，这种特性会导致处理速度较慢和内存消耗较高的代价。

开发人员可能并不总是需要存储动态数据。例如，如果需要存储板球队数据，那么数组就足够了，因为每边总是有 11 名球员，并且在游戏过程中不能修改。在这种特定情况下，这将使过程比使用链表更快更高效。

在另一种情况下，对于射击游戏，开发人员无法预测用户在游戏过程中可能发射的子弹数量。因此，队列数据结构将是最有效的，以处理所有发射的子弹。

类似地，堆栈和树结构可以在适当的情况下选择。对于排序和搜索算法也可以采用相同的方法。

## 资产使用技术

我们已经对游戏资产进行了分类。现在让我们从优化技术和最佳实践的角度来讨论它们。

### 艺术资产

一组艺术资产可以应用单独的优化技术。艺术资产是游戏的面孔。因此，必须确保视觉足够吸引人，以开始游戏。

正如我们已经讨论过的，更好的艺术资产会消耗内存和性能。然而，这可以最小化到一定程度。有几种艺术资产优化工具。然而，使用不合适的工具可能会导致数据丢失，最终导致视觉质量不佳。

艺术在视觉质量方面不应妥协。通常，艺术家开发的资产在游戏中不能完美反映，因为优化不当。

我们已经讨论了艺术资产应该如何制作。现在，让我们假设一些艺术只使用 8 位数据空间作为原始格式，但是导出时是 24 位格式。那么，开发人员可以使用工具将资产优化为典型的 8 位格式，而不影响视觉质量。

这个规则也适用于完全不透明的资产。开发人员可以摆脱透明信息，以获得优化的艺术资产。

### 音频资产

音频资产也是独立的资产。音频已经成为扩展用户体验的非常重要的资产。音频配置可以根据频率、位深度和压缩技术的广泛范围而变化。每种配置变化都有不同的处理和内存消耗水平。

因此，声音优化也是优化过程中非常重要的一部分。在安卓游戏开发行业中的常见做法是为 SFX 和音乐文件选择两种不同的音频格式。

开发人员通常忽视的一件事是音频信息数据。一些安卓设备有特定的频率上限，但当使用更多频率时，声音通常会更好。因此，确定安卓游戏声音的上限是一个技术设计层面的步骤。因此，每个声音都应该在接近范围内制作。

声音设计师需要在限制内保持质量。通过这种方式，音频资产可以在开发时进行优化。

### 其他资产

除了艺术和音频，游戏中可能还会使用其他数据资产。数据格式可以是任何形式，如二进制、文本、XML、JSON 或自定义。自定义格式基本上与二进制格式相同，只是加了一些加密。

在游戏开发中，将数据集分开使用是一种常见做法。单独的数据集有助于构建项目结构，并灵活地使用相同的代码来产生不同的输出。通常，开发人员更新数据源以更新完整的游戏体验，而无需创建新的 APK。这在长期内减少了开发时间，以便维护游戏并进行简单的更新。

从优化的角度来看，这些数据源应该被优化到足够快速地处理并且不消耗太多内存。然而，读写外部文件需要时间。通常，二进制文件是处理速度最快且大小最小的。然而，在读取二进制数据后，必须对其进行解析以在游戏中使用，这最终会增加处理时间。

最常用的数据格式是 XML 和 JSON。安卓库支持它们两者，包括通用解析器。开发人员可以在不进行额外处理的情况下获得可用的数据。然而，数据可以根据游戏的要求在游戏过程中进行操作。

## 处理缓存数据

缓存是一个类似于 RAM 的内存段，但比传统 RAM 更快的功能。处理器可以更快地访问这个段。因此，从逻辑上讲，缓存应该只存储经常使用的数据。

处理缓存数据的最佳方式是监控应用程序内存的使用情况。通常，操作系统应至少有 10%的空闲内存可用。经测试，一个应用程序可以使用平均 2%的总空闲内存。

然而，开发人员无法在技术上控制缓存。他们只能确保最常用的元素被优化得足够完美，以便执行器自动为它们使用缓存内存。

# 总结

优化是任何软件开发中最重要的任务之一，尤其是在游戏开发中，逻辑编程占据了技术编程的主导地位。对于技术编程来说，有大量的优化工具和技术可供使用，因为它有最常见的算法来实现。然而，在游戏编程的情况下，每个游戏玩法都需要不同的算法集。在许多情况下，还需要单独制作人工智能算法。因此，程序员很有可能需要找到一种有效的方法来优化新编写的算法。

我们已经讨论了在安卓游戏开发中所有可能的优化范围。技术优化是强制性的，因为它有固定的指导方针要遵循。然而，逻辑开发将取决于游戏算法及其要求。因此，对于游戏开发人员来说，优化安卓游戏是额外的努力。

有时，开发人员会过度优化游戏。这是不推荐的。过度优化通常会降低游戏的质量。因此，在技术设计时，应声明优化情况。

大多数大规模开发过程都有一个单独定义的优化任务集。一些开发者选择开发动态优化过程。这意味着开发者在不同阶段、不同规模上优化游戏。这两种过程都是有效的，但第一种在逻辑上更合理，因为定义一个单独的任务总是会给出整体优化的暂定时间。这有助于更好地管理整个游戏开发过程。

所有优化过程都经过测试阶段的验证。所有设计、工程和艺术工作都在游戏开发的这一阶段进行测试。我们将在本书的下一章更深入地讨论测试。