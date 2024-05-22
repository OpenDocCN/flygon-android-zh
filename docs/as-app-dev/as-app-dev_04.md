# 第四章：使用代码编辑器

你已经创建了你的第一个项目，并且知道如何浏览不同的文件夹、子文件夹和文件。是时候开始编程了！你有没有想过能够更高效地编程？如何加快你的开发过程？你想要学习有用的快捷键，例如一次性注释多行，查找和替换字符串，或者在方法调用中快速移动不同的参数吗？

在本章中，我们将学习如何使用代码编辑器以及如何自定义它，以便在编程时感觉更舒适。了解代码编辑器的基本特性是值得的，以提高开发人员的工作效率。我们将了解代码补全和代码生成。最后，我们将学习一些有用的快捷键和热键，以加快我们的开发过程。

这是我们将在本章中介绍的主题：

+   自定义代码编辑器

+   代码补全

+   代码生成

+   查找相关内容

+   有用的快捷键

# 编辑器设置

要打开编辑器设置，请导航至**文件** | **设置**，在**IDE 设置**部分，选择**编辑器**菜单。此屏幕显示编辑器的一般设置。我们建议检查默认未选中的两个选项：

+   **使用 Ctrl + 鼠标滚轮更改字体大小（缩放）**：此选项允许我们使用鼠标滚轮更改编辑器的字体大小，就像在其他程序（如网页浏览器）中所做的那样。

+   **鼠标移动时显示快速文档**：如果我们选中此选项，当我们将鼠标悬停在一段代码上并等待 500 毫秒，一个小对话框将显示关于该代码的快速文档。当我们再次移动鼠标时，对话框会自动消失，但如果我们将鼠标移动到对话框内，就可以详细查看文档。这非常有用，例如，阅读一个方法的功能及其参数，而无需导航到该方法处。![编辑器设置](img/5273OS_04_01.jpg)

有更多设置分布在七个类别中：

+   **智能键**：配置在打字时自动执行的操作，例如添加关闭括号、引号或标签；或者在我们按下*Enter*键时缩进行。

+   **外观**：配置编辑器的外观。我们建议检查默认未选中的以下两个选项：

    +   **显示行号**：在编辑器的左侧边缘显示行号。当我们调试或检查日志时，这可能非常有用。

    +   **显示方法分隔符**：在视觉上将类中的方法分隔开。

+   **颜色与字体**：更改字体和颜色。有许多选项和元素需要配置（关键词、数字、警告、错误、注释、字符串等）。我们可以将配置保存为方案。

+   **编辑器标签**：配置编辑器标签。我们建议选择**用星号标记修改过的标签**选项，以便轻松识别已修改但未保存的文件。

+   **代码折叠**：代码折叠选项允许我们折叠或展开代码块。它非常适用于隐藏我们未编辑的代码块，简化代码视图。我们可以通过编辑器中的图标或使用**代码** | **折叠**菜单来折叠或展开这些块。![编辑器设置](img/5273OS_04_02.jpg)

+   **代码补全**：配置代码补全选项。下一节将详细探讨代码补全。

+   **自动导入**：配置当我们粘贴使用当前类中没有导入的类的代码时，编辑器的表现。默认情况下，这样做时会出现一个弹出窗口以添加导入命令。如果我们勾选了**即时添加明确的导入**选项，导入命令将会自动添加，无需我们干预。![编辑器设置](img/5273OS_04_03.jpg)

# 代码补全

代码补全通过建议列表和自动完成代码来帮助我们快速编写代码。

基本代码补全是我们在输入时出现的建议列表。如果未显示列表，请按*Ctrl* + 空格键打开它。

![代码补全](img/5273OS_04_04.jpg)

继续输入，从列表中选择一个命令，然后按*Enter*或双击将其添加到你的代码中。

如果我们正在编写的代码是一个表达式，但我们希望以否定的形式插入表达式，那么从建议列表中选择表达式后，不要按*Enter*或双击它，而是按感叹号键（*!*）。表达式将以否定形式添加。

另一种类型的代码补全是**智能类型代码补全**。如果我们正在输入一个调用带有`String`参数的方法的命令，那么只会建议`String`对象。这种智能补全出现在赋值语句的右侧部分、方法调用的参数、返回语句或变量初始化器中。要打开智能建议列表，请按*Ctrl* + *Shift* + 空格键。

要显示这两种建议列表之间的区别，请在你的代码中创建两个不同类的对象，`String`和`int`。然后调用带有`String`参数的方法，例如`Log`类的`i`方法。在输入`String`参数时，注意下一个屏幕截图显示的基本建议列表（*Ctrl* + 空格键）和下一页显示的智能类型建议列表（*Ctrl* + *Shift* + 空格键）之间的区别。

![代码补全](img/5273OS_04_05.jpg)

在前一个屏幕截图中显示的第一个列表中，尽管`int`对象与`parameter`类不匹配，但两个对象都被建议。在下面屏幕截图中显示的第二个列表中，只建议`String`对象。

![代码补全](img/5273OS_04_06.jpg)

代码补全最后一个实用功能是**语句补全**。输入一个语句，按下*Ctrl* + *Shift* + *Enter*，注意结尾的标点符号是如何自动添加的。如果在输入关键字`if`后按下这些键，会添加括号和括号以完成条件语句。此快捷键也可用于完成方法声明。开始输入一个方法，并在输入左括号或输入方法参数后按下*Ctrl* + *Shift* + *Enter*。将添加右括号和括号以完成方法规范。

# 代码生成

要在类中生成代码块，导航到**代码** | **生成**或按下快捷键*Alt* + *Insert*。我们可以生成构造函数、getter 和 setter 方法、`equals`和`toString`方法、重写或委托方法。

另一种生成代码的方法是用一些语句（`if`、`if`/`else`、`while`、`for`、`try`/`catch`等）包围我们的代码。选择一行代码并导航到**代码** | **环绕以**或按下*Ctrl* + *Alt* + *T*。

第三种选项是插入代码模板。导航到**代码** | **插入实时模板**以打开可用模板的对话框。这些模板可以插入用于遍历集合、数组、列表等的代码；用于打印格式化字符串的代码、抛出异常的代码，或者添加静态和最终变量的代码。在对话框的左侧边缘，每个模板都有一个前缀，因此在编辑器中输入前缀并按下*Tab*键，代码模板会自动添加。

尝试在主活动的`onCreate`方法末尾输入`inn`并按下*Tab*。将出现一个条件块。在这个新块中，输入`soutm`并再次按下*Tab*。结果如下所示。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    if (savedInstanceState != null) {
 System.out.println("savedInstanceState = [" + savedInstanceState + "]");
 }
 }
```

# 导航代码

直接导航到声明或类型声明的方法是按下*Ctrl*并点击显示为链接的符号。此选项也可以从**导航** | **声明**访问。

从编辑器的左侧边缘我们可以导航到方法的层次结构。在属于方法层次结构的方法声明旁边，有一个图标表示一个方法是否正在实现接口方法、实现抽象类方法、重写超类方法，或者相反，一个方法是否被其他后代实现或重写。

点击这些图标以导航到层次结构中的方法。此选项也可以通过**导航** | **超级方法**或**导航** | **实现**访问。通过打开我们第一个项目的主活动(`MainActivity.java`)来测试它。

![导航代码](img/5273OS_04_07.jpg)

与代码导航相关的另一个有用工具是自定义区域的使用。一个**自定义区域**只是你想要分组并为其命名的一段代码。例如，如果一个类有很多方法，我们可以创建一些自定义区域来分配方法。一个区域有一个名称或描述，并且可以使用代码折叠来折叠或展开。

要创建自定义区域，我们可以使用代码生成。选择代码片段，导航到**代码** | **环绕以**，并选择以下两个选项之一：

+   **<editor-fold…> 注释**

+   **region…endregion 注释**

它们都创建一个区域，但使用不同的样式。

当我们使用自定义区域时，可以通过**导航** | **自定义区域**菜单进行导航。

其他导航选项可以从**导航**菜单访问：

+   **类**/**文件**/**符号**：通过名称查找类、文件或符号。

+   **行**：通过行号转到代码行。

+   **最后编辑位置**：导航到最近的更改点。

+   **测试**：导航到当前类的测试。

+   **文件结构**：打开一个对话框，显示文件结构。打开我们主活动的文件结构，观察结构是如何呈现的，显示方法列表，指示元素类型的图标，或指示元素可见性的图标。![导航代码](img/5273OS_04_08.jpg)

+   **文件路径**：打开一个对话框，显示编辑器中打开文件的完整路径。

+   **类型层次结构**：打开一个对话框，显示选定对象的类型层次结构。

+   **方法层次结构**：打开一个对话框，显示选定方法的方法层次结构。

+   **调用层次结构**：打开一个对话框，显示选定方法的调用层次结构。

+   **下一个高亮错误**：导航到下一个错误。

+   **上一个高亮错误**：导航到上一个错误。

+   **下一个方法**：导航到下一个方法。

+   **上一个方法**：导航到上一个方法。

# 有用的操作

以下是一些有用的快捷键：

+   *Ctrl* + *W*：根据语法选择表达式。多次按这些键以扩展选择。相反的命令是*Ctrl* + *Shift* + *W*。

+   *Ctrl* + */*：注释选中代码的每一行。要使用块注释，请按*Ctrl* + *Shift* + */*。

+   *Ctrl* + *Alt* + *I*：缩进选中的代码。在完成编写代码块或方法后清理代码时很有用。

+   *Ctrl* + *Alt* + *O*：优化导入，移除未使用的并重新排序其余的。

+   *Shift* + *Ctrl* + 方向键：将选中的代码移动到另一行。

+   *Alt* + 方向键：在编辑器的打开标签页之间切换。

+   *Ctrl* + *F*：在编辑器的活动标签页中查找字符串。

+   *Ctrl* + *R*：替换编辑器活动标签页中的字符串。

+   *Ctrl* + *A*：选择打开文件中的所有代码。

+   *Ctrl* + *D*：复制选中的代码并将其粘贴到代码末尾。如果没有选中任何代码，则会复制整行并在新行中粘贴。

+   *Ctrl* + *Y*：删除整行，且不留下任何空行。

+   *Ctrl* + *Shift* + *U*：切换大小写。

+   *Tab*：移动到下一个参数。

# 总结

在本章结束时，用户应该学会一些有用的技巧和操作，以便最大限度地利用代码编辑器。我们现在知道了如何使用代码补全、代码生成以及加快不同操作的一些快捷键。我们还定制了代码编辑器，现在可以开始编程了。

在下一章中，我们将开始使用布局创建我们的第一个用户界面。我们将学习如何使用图形向导创建布局以及如何通过文本视图编辑 XML 布局文件来创建布局。我们将创建第一个应用程序，一个使用文本视图组件的经典*Hello World*示例。我们还将学习如何准备我们的应用程序以适应多种屏幕尺寸，并使它们适应不同的设备方向。最后，我们将了解 UI 主题以及如何处理事件。