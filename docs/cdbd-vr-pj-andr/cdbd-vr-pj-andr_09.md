# 第九章 音乐可视化

> “'看音乐，听舞蹈'，”著名的俄罗斯裔编舞家、美国芭蕾舞之父乔治·巴兰钦说道。

我们不打算提升艺术形式的水平，但或许将手机上的播放列表可视化会很有趣。在这个项目中，我们将创建 3D 动画抽象图形，以音乐的节奏起舞。您可能熟悉 2D 音乐可视化，但在 VR 中会是什么样子呢？要获得灵感，可以尝试使用短语*geometry wars*在 Google 上搜索图像，例如 XBox 的经典游戏！

可视化应用程序从 Android 音频系统接收输入并显示可视化效果。在这个项目中，我们将利用 Android 的`Visualizer`类，让应用程序捕获当前播放音频的一部分，而不是完整的高保真音乐细节，而是足够进行可视化的低质量音频内容。

在这个项目中，我们将：

+   设置新项目

+   构建名为 VisualizerBox 的 Java 类架构

+   从手机音频播放器中捕获波形数据

+   构建几何可视化

+   构建基于纹理的可视化

+   捕获 FFT 数据并构建 FFT 可视化

+   添加迷幻轨迹模式

+   支持多个并发可视化

此项目的源代码可以在 Packt Publishing 网站和 GitHub 上找到，网址为[`github.com/cardbookvr/visualizevr`](https://github.com/cardbookvr/visualizevr)（每个主题作为单独的提交）。

# 设置新项目

要构建此项目，我们将使用我们在第五章中创建的 RenderBox 库，*RenderBox 引擎*。您可以使用您自己的库，或者从本书提供的可下载文件或我们的 GitHub 存储库中获取副本（使用标记为`after-ch8`的提交 - [`github.com/cardbookvr/renderboxlib/releases/tag/after-ch8`](https://github.com/cardbookvr/renderboxlib/releases/tag/after-ch8)）。有关如何导入`RenderBox`库的更详细描述，请参阅第五章的最后一节，*在未来项目中使用 RenderBox*。要创建新项目，请执行以下步骤：

1.  打开 Android Studio，创建一个新项目。让我们将其命名为`VisualizeVR`，并针对**Android 4.4 KitKat (API 19)**使用**空活动**。

1.  为`renderbox`、`common`和`core`包创建新模块，使用**文件**|**新建模块**|**导入.JAR/.AAR 包**。

1.  将模块设置为应用程序的依赖项，使用**文件**|**项目结构**。

1.  根据第二章中的说明编辑`build.gradle`文件，*骨架硬纸板项目*，以便编译针对 SDK 22。

1.  更新`/res/layout/activity_main.xml`和`AndroidManifest.xml`，如前几章所述。

1.  将`MainActivity`编辑为`class MainActivity extends CardboardActivity implements IRenderBox`，并实现接口方法存根（*Ctrl* + *I*）。

我们可以继续在`MainActivity`中定义`onCreate`方法。该类现在具有以下代码：

```kt
public class MainActivity extends CardboardActivity implements IRenderBox {
    private static final String TAG = "MainActivity";CardboardView cardboardView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        cardboardView = (CardboardView) findViewById(R.id.cardboard_view);
        cardboardView.setRenderer(new RenderBox(this, this));
        setCardboardView(cardboardView);
    }
    @Override
    public void setup() {
    }
    @Override
    public void preDraw() {
        // code run beginning each frame
    }
    @Override
    public void postDraw() {
        // code run end of each frame
    }
}
```

您可以将一个立方体临时添加到场景中，以确保一切设置正确。将其添加到`setup`方法中，如下所示：

```kt
    public void setup() {
        new Transform()
            .setLocalPosition(0,0,-7)
            .setLocalRotation(45,60,0)
            .addComponent(new Cube(true));
    }
```

如果您记得，`Cube`是添加到`Transform`的`Component`。`Cube`定义了其几何形状（例如，顶点）。`Transform`定义了其在 3D 空间中的位置、旋转和缩放。

您应该能够在 Android 设备上单击**运行'app'**而没有编译错误，并看到立方体和硬纸板分屏视图。

# 捕获音频数据

使用 Android 的`Visualizer`类（[`developer.android.com/reference/android/media/audiofx/Visualizer.html`](http://developer.android.com/reference/android/media/audiofx/Visualizer.html)），我们可以以指定的采样率检索当前播放的音频数据的一部分。您可以选择捕获波形和/或频率数据：

+   **波形**：这是表示音频振幅采样系列的单声道音频波形字节数组，或**脉冲编码调制**（**PCM**）数据

+   **频率**：这是表示音频频率采样的**快速傅立叶变换**（**FFT**）字节数组

数据限制为 8 位，因此对于播放而言并不有用，但对于可视化来说足够了。您可以指定采样率，尽管它必须是 2 的幂。

掌握了这些知识，我们现在将继续实施一个架构，捕获音频数据并使其可用于您可以构建的可视化渲染器。

# VisualizerBox 架构

音乐可视化器通常看起来非常酷，尤其是一开始。但是一段时间后，它们可能会显得太重复，甚至无聊。因此，在我们的设计中，我们将构建一个能够排队一些不同可视化的能力，然后在一段时间后从一个切换到另一个。

为了开始我们的实施，我们将定义一个可扩展的架构结构，让我们在开发新的可视化时能够扩展。

然而，即使在那之前，我们必须确保应用程序有权限使用我们需要的 Android 音频功能。将以下指令添加到`AndroidManifest.xml`中：

```kt
    <!-- Visualizer permissions -->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
```

请记住，`RenderBox`库最初是在第五章中开发的，*RenderBox 引擎*，允许`MainActivity`将大部分图形和 Cardboard VR 工作委托给`RenderBox`类和相关类（`Component`，`Material`等）。我们将在此基础上构建一个类似的设计模式，建立在`RenderBox`之上。`MainActivity`可以实例化特定的可视化，然后将工作委托给`VisualizerBox`类。

`VisualizerBox`类将提供回调函数给 Android 的`Visualizer`类。让我们首先定义这个的骨架实现。创建一个`VisualizerBox`Java 类，如下所示：

```kt
public class VisualizerBox {
    static final String TAG = "VisualizerBox";
    public VisualizerBox(final CardboardView cardboardView){
    }
    public void setup() {
    }
    public void preDraw() {
    }
    public void postDraw() {
    }
}
```

将`VisualizerBox`集成到`MainActivity`中，在类的顶部添加一个`visualizerBox`变量。在`MainActivity`中，添加以下行：

```kt
    VisualizerBox visualizerBox;
```

在`onCreate`中进行初始化：

```kt
        visualizerBox = new VisualizerBox(cardboardView);
```

此外，在`MainActivity`中，调用每个`IRenderBox`接口方法的相应版本：

```kt
    @Override
    public void setup() {
        visualizerBox.setup();
    }
    @Override
    public void preDraw() {
        visualizerBox.preDraw();
    }
    @Override
    public void postDraw() {
        visualizerBox.postDraw();
    }
```

很好。现在我们将设置`VisualizerBox`，让您构建和使用一个或多个可视化。因此，首先让我们在`Visualization.java`文件中定义抽象的`Visualization`类，如下所示：

```kt
public abstract class Visualization {
    VisualizerBox visualizerBox;            //owner

    public Visualization(VisualizerBox visualizerBox){
        this.visualizerBox = visualizerBox;
    }
    public abstract void setup();
    public abstract void preDraw();
    public abstract void postDraw();
}
```

现在我们有了一个机制来为应用程序创建各种可视化实现。在我们继续编写其中一个之前，让我们还提供与`VisualizerBox`的集成。在`VisualizerBox`类的顶部，添加一个变量到当前的`activeViz`对象：

```kt
    public Visualization activeViz;
```

然后，从接口方法中调用它：

```kt
    public void setup() {
        if(activeViz != null)
            activeViz.setup();
    }
    public void preDraw() {
        if(activeViz != null)
            activeViz.preDraw();
    }
    public void postDraw() {
        if(activeViz != null)
            activeViz.postDraw();
    }
```

当然，我们甚至还没有使用 Android 的`Visualizer`类，也没有在屏幕上渲染任何东西。接下来会有。

现在，让我们为可视化创建一个占位符。在项目中创建一个名为`visualizations`的新文件夹。右键单击您的 Java 代码文件夹（例如`java/com/cardbookvr/visualizevr/`），转到**新建** | **包**，并将其命名为`visualizations`。然后，右键单击新的`visualizations`文件夹，转到**新建** | **Java 类**，并将其命名为`BlankVisualization`。然后，将其定义为`extends Visualization`，如下所示：

```kt
public class BlankVisualization extends Visualization {
    static final String TAG = "BlankVisualization";
    public BlankVisualization(VisualizerBox visualizerBox) {
        super(visualizerBox);
    }
    @Override
    public void setup() {
    }
    @Override
    public void preDraw() {
    }
    @Override
    public void postDraw() {
    }
}
```

我们将能够将其用作特定可视化器的模板。每种方法的目的都相当不言自明：

+   `setup`：这个方法初始化可视化的变量、转换和材料

+   `preDraw`：此代码在每帧开始时执行；例如，使用当前捕获的音频数据

+   `postDraw`：此代码在每帧结束时执行

现在让我们给这个骨架添加一些内容。

# 波形数据捕获

如前所述，Android 的`Visualizer`类让我们定义回调来捕获音频数据。这些数据有两种格式：波形和 FFT。我们现在将仅添加波形数据到`VisualizerBox`类中。

首先，定义我们将用于捕获音频数据的变量，如下所示：

```kt
    Visualizer visualizer;
    public static int captureSize;
    public static byte[] audioBytes;
```

使用 API，我们可以确定可用的最小捕获大小，然后将其用作我们的捕获样本大小。

然后，在构造函数中初始化它们如下。首先，实例化一个 Android`Visualizer`。然后设置要使用的捕获大小，并分配我们的缓冲区：

```kt
    public VisualizerBox(final CardboardView cardboardView){
        visualizer = new Visualizer(0);
        captureSize = Visualizer.getCaptureSizeRange()[0];
        visualizer.setCaptureSize(captureSize);
        // capture audio data
        // Visualizer.OnDataCaptureListener captureListener = ...
        visualizer.setDataCaptureListener(captureListener, Visualizer.getMaxCaptureRate(), true, true);
        visualizer.setEnabled(true);
    }
```

我们希望出于各种原因使用最小尺寸。首先，它会更快，而在虚拟现实中，速度至关重要。其次，它将我们的 FFT 样本（稍后讨论）组织成更少的桶。这很有帮助，因为每个桶可以在更广泛的频率范围内捕捉更多的活动。

### 注意

请注意，我们在定义捕获监听器的地方留下了一个注释，然后在可视化器中设置它。确保你启用了可视化器作为始终监听。

首先编写仅用于波形数据的`captureListener`对象。我们定义并实例化一个实现`Visualizer.OnDataCaptureListener`的新匿名类，并为其提供一个名为`onWaveFormDataCapture`的函数，该函数接收波形字节并将其存储到我们的`Visualization`代码中（即将推出）：

```kt
        // capture audio data
        Visualizer.OnDataCaptureListener captureListener = new Visualizer.OnDataCaptureListener() {
            @Override
            public void onWaveFormDataCapture(Visualizer visualizer, byte[] bytes, int samplingRate) {
                audioBytes = bytes;
            }
            @Override
            public void onFftDataCapture(Visualizer visualizer, byte[] bytes, int samplingRate) {
            }
        };
```

界面仍然要求我们提供一个`onFftDataCapture`方法，但我们暂时将其留空。

现在我们准备为这个项目添加一些图形。

# 一个基本的几何可视化

对于我们的第一个可视化，我们将创建一个基本的均衡器波形图形。它将是一个由一系列根据音频波形数据进行缩放的立方体组成的矩形块。我们将使用内置的`Cube`组件，已经在`RenderBox`库中的基本顶点颜色照明材质。

在`visualizations/`文件夹中，创建一个名为`GeometricVisualization`的新的 Java 类，并开始如下：

```kt
public class GeometricVisualization extends Visualization {
    static final String TAG = "GeometricVisualization";
    public GeometricVisualization(VisualizerBox visualizerBox) {
        super(visualizerBox);
    }
}
```

在类的顶部，声明一个立方体变换的`Transform`数组和相应的`RenderObjects`数组：

```kt
    Transform[] cubes;
    Cube[] cubeRenderers;
```

然后，在`setup`方法中初始化它们。我们将分配立方体数组，对齐并缩放为相邻的一组块，创建一个波浪状块的 3D 表示。`setup`方法可以实现如下：

```kt
    public void setup() {
        cubes = new Transform[VisualizerBox.captureSize / 2];
        cubeRenderers = new Cube[VisualizerBox.captureSize / 2];

        float offset = -3f;
        float scaleFactor = (offset * -2) / cubes.length;
        for(int i = 0; i < cubes.length; i++) {
            cubeRenderers[i] = new Cube(true);
            cubes[i] = new Transform()
                    .setLocalPosition(offset, -2, -5)
                    .addComponent(cubeRenderers[i]);
            offset += scaleFactor;
        }
    }
```

现在在每一帧上，我们只需要根据音频源中当前的波形数据（在`VisualizerBox`中获取）修改每个立方体的高度。实现`preDraw`方法如下：

```kt
    public void preDraw() {
        if (VisualizerBox.audioBytes != null) {
            float scaleFactor = 3f / cubes.length;
            for(int i = 0; i < cubes.length; i++) {
                cubes[i].setLocalScale(scaleFactor, VisualizerBox.audioBytes[i] * 0.01f, 1);
            }
        }
    }

    public void postDraw() {
    }
```

我们还需要为`postDraw`实现添加一个存根。然后，实例化可视化并使其成为活动状态。在`MainActivity`中，在`onCreate`的末尾，添加以下代码行：

```kt
        visualizerBox.activeViz = new GeometricVisualization(visualizerBox);
```

现在我们只需要这些。

在手机上播放一些音乐。然后运行应用程序。你会看到类似这样的东西：

![基本几何可视化](img/B05144_09_01.jpg)

正如你所看到的，我们在场景中保留了单位立方体，因为它有助于澄清发生了什么。每个音频数据都是一个薄的“切片”（或者是一个扁平的立方体），其高度随音频值的变化而变化。如果你正在查看前一个屏幕图像的彩色版本，你会注意到可视化立方体的彩色面就像孤立的立方体，因为它们使用相同的对象和材质进行渲染。

这个可视化是使用音频波形数据动态修改 3D 几何的一个非常基本的例子。让你的想象力奔放，创造属于你自己的。音频字节可以控制任何变换参数，包括比例、位置和旋转。记住我们在一个 3D 虚拟现实空间中，你可以使用所有这些——把你的东西四处移动，上下移动，甚至在你的身后。我们有一些基本的原始几何形状（立方体、球体、平面、三角形等）。但你也可以使用音频数据来参数化生成新的形状和模型。此外，你甚至可以集成前一章的`ModelObject`类来加载有趣的 3D 模型！

在下一个主题中，我们将看看如何在基于纹理的材质着色器中使用音频波形数据。

# 基于 2D 纹理的可视化

第二个可视化也将是基本的示波器类型显示波形数据。然而，以前我们使用音频数据来缩放 3D 切片立方体；这一次，我们将使用一个着色器，在 2D 平面上渲染它们全部，使用音频数据作为输入。

我们的`RenderBox`库允许我们定义新的材质和着色器。在以前的项目中，我们构建了使用位图图像进行纹理映射的材质，以便在渲染时将其渲染到几何图形上。在这个项目中，我们将使用音频字节数组来绘制四边形，使用字节值来控制设置更亮颜色的位置。（请注意，`Plane`类是在第七章中添加到`RenderBox`库中的，*360 度画廊*。）

## 纹理生成器和加载器

首先，让我们生成一个纹理结构来保存我们的纹理数据。在`VisualizerBox`类中，添加以下方法来设置 GLES 中的纹理。我们不能使用我们正常的纹理流程，因为它是设计为直接从图像数据中分配纹理。我们的数据是一维的，所以使用`Texture2D`资源可能看起来有点奇怪，但我们将高度设置为一个像素：

```kt
    public static int genTexture(){
        final int[] textureHandle = new int[1];
        GLES20.glGenTextures(1, textureHandle, 0);
        RenderBox.checkGLError("VisualizerBox GenTexture");
        if (textureHandle[0] != 0) {
            // Bind to the texture in OpenGL
            GLES20.glBindTexture(GLES20.GL_TEXTURE_2D, textureHandle[0]);
            // Set filtering
            GLES20.glTexParameteri(GLES20.GL_TEXTURE_2D, GLES20.GL_TEXTURE_MIN_FILTER, GLES20.GL_NEAREST);
            GLES20.glTexParameteri(GLES20.GL_TEXTURE_2D, GLES20.GL_TEXTURE_MAG_FILTER, GLES20.GL_NEAREST);
        }
        if (textureHandle[0] == 0){
            throw new RuntimeException("Error loading texture.");
        }
        return textureHandle[0];
    }
```

然后添加`setup`的调用，包括一个静态变量来保存生成的纹理句柄：

```kt
    public static int audioTexture = -1;

    public void setup() {
        audioTexture = genTexture();
        if(activeViz != null)
            activeViz.setup();
    }
```

现在我们可以从音频字节数据中填充纹理。在 Android 的`Visualizer`监听器中，在`onWaveFormDataCapture`方法中添加一个`loadTexture`的调用：

```kt
            public void onWaveFormDataCapture(Visualizer visualizer, byte[] bytes, int samplingRate){
                audioBytes = bytes;
                loadTexture(cardboardView, audioTexture, bytes);
            }
```

让我们按照以下方式定义`loadTexture`。它将音频字节复制到一个新的数组缓冲区，并将其传递给 OpenGL ES，使用`glBindTexture`和`glTexImage2D`调用。

（参考[`stackoverflow.com/questions/14290096/how-to-create-a-opengl-texture-from-byte-array-in-android`](http://stackoverflow.com/questions/14290096/how-to-create-a-opengl-texture-from-byte-array-in-android)。）：

```kt
    public static void loadTexture(CardboardView cardboardView, final int textureId, byte[] bytes){
        if(textureId < 0)
            return;
        final ByteBuffer buffer = ByteBuffer.allocateDirect(bytes.length * 4);
        final int length = bytes.length;
        buffer.order(ByteOrder.nativeOrder());
        buffer.put(bytes);
        buffer.position(0);
        cardboardView.queueEvent(new Runnable() {
            @Override
            public void run() {
                GLES20.glBindTexture(GLES20.GL_TEXTURE_2D, textureId);
                GLES20.glTexImage2D(GLES20.GL_TEXTURE_2D, 0, GLES20.GL_LUMINANCE, length, 1, 0,
                        GLES20.GL_LUMINANCE, GLES20.GL_UNSIGNED_BYTE, buffer);
            }
        });
    }
```

## 波形着色器

现在是时候编写着色器程序了，这些程序将决定在`Material`类中需要设置的参数和属性，以及其他内容。

如果需要，为着色器创建一个资源目录，`res/raw/`。然后，创建`waveform_vertex.shader`和`waveform_fragment.shader`文件。定义如下。

`waveform_vertex.shader`文件与我们之前使用的`unlit_tex_vertex`着色器相同。严格来说，我们可以重用这个文件，并在`createProgram`函数中指定它的资源，但是除非你明确遵循某种模式，否则最好定义单独的着色器文件。

文件：`res/raw/waveform_vertex.shader`：

```kt
uniform mat4 u_MVP;
attribute vec4 a_Position;
attribute vec2 a_TexCoordinate;
varying vec2 v_TexCoordinate;
void main() {
   // pass through the texture coordinate
   v_TexCoordinate = a_TexCoordinate;
   // final point in normalized screen coordinates
   gl_Position = u_MVP * a_Position;
}
```

对于`waveform_fragment`着色器，我们添加了一个固定颜色（`u_Color`）和阈值宽度（`u_Width`）的变量。然后，添加一些逻辑来决定当前正在渲染的像素的*y*坐标是否在样本的`u_Width`范围内。

文件：`res/raw/waveform_fragment.shader`

```kt
precision mediump float;        // default medium precision
uniform sampler2D u_Texture;    // the input texture
varying vec2 v_TexCoordinate;   // interpolated texture coordinate per fragment
uniform vec4 u_Color;
uniform float u_Width;
// The entry point for our fragment shader.
void main() {
    vec4 color;
    float dist = abs(v_TexCoordinate.y - texture2D(u_Texture, v_TexCoordinate).r);
    if(dist < u_Width){
        color = u_Color;
    }
    gl_FragColor = color;
}
```

## 基本波形材质

现在我们为着色器定义`Material`类。创建一个名为`WaveformMaterial`的新的 Java 类，并将其定义如下：

```kt
public class WaveformMaterial extends Material {
    private static final String TAG = "WaveformMaterial";
}
```

为纹理 ID、边框、宽度和颜色添加材质变量。然后，添加着色器程序引用和缓冲区的变量，如下所示：

```kt
    static int program = -1; //Initialize to a totally invalid value for setup state
    static int positionParam;
    static int texCoordParam;
    static int textureParam;
    static int MVPParam;
    static int colorParam;
    static int widthParam;

    public float borderWidth = 0.01f;
    public float[] borderColor = new float[]{0.6549f, 0.8392f, 1f, 1f};

    FloatBuffer vertexBuffer;
    FloatBuffer texCoordBuffer;
    ShortBuffer indexBuffer;
    int numIndices;
```

现在我们可以添加一个构造函数。正如我们之前看到的，它调用了一个`setupProgram`辅助方法，该方法创建着色器程序并获取对其参数的引用：

```kt
    public WaveformMaterial() {
        super();
        setupProgram();
    }

    public static void setupProgram() {
        if(program > -1) return;
        //Create shader program
        program = createProgram( R.raw.waveform_vertex, R.raw.waveform_fragment );
        RenderBox.checkGLError("Bitmap GenTexture");

        //Get vertex attribute parameters
        positionParam = GLES20.glGetAttribLocation(program, "a_Position");
        RenderBox.checkGLError("Bitmap GenTexture");
        texCoordParam = GLES20.glGetAttribLocation(program, "a_TexCoordinate");
        RenderBox.checkGLError("Bitmap GenTexture");

        //Enable them (turns out this is kind of a big deal ;)
        GLES20.glEnableVertexAttribArray(positionParam);
        RenderBox.checkGLError("Bitmap GenTexture");
        GLES20.glEnableVertexAttribArray(texCoordParam);
        RenderBox.checkGLError("Bitmap GenTexture");

        //Shader-specific parameters
        textureParam = GLES20.glGetUniformLocation(program, "u_Texture");
        MVPParam = GLES20.glGetUniformLocation(program, "u_MVP");
        colorParam = GLES20.glGetUniformLocation(program, "u_Color");
        widthParam = GLES20.glGetUniformLocation(program, "u_Width");
        RenderBox.checkGLError("Waveform params");
    }
```

同样，我们添加一个`setBuffers`方法，供`RenderObject`组件（`Plane`）调用：

```kt
    public WaveformMaterial setBuffers(FloatBuffer vertexBuffer, FloatBuffer texCoordBuffer, ShortBuffer indexBuffer, int numIndices) {
        //Associate VBO data with this instance of the material
        this.vertexBuffer = vertexBuffer;
        this.texCoordBuffer = texCoordBuffer;
        this.indexBuffer = indexBuffer;
        this.numIndices = numIndices;
        return this;
    }
```

添加`draw`代码，它将从`Camera`组件中调用，以渲染在缓冲区中准备的几何图形（通过`setBuffers`）。`draw`方法如下所示：

```kt
    @Override
    public void draw(float[] view, float[] perspective) {
        GLES20.glUseProgram(program);

        // Set the active texture unit to texture unit 0.
        GLES20.glActiveTexture(GLES20.GL_TEXTURE0);

        // Bind the texture to this unit.
        GLES20.glBindTexture(GLES20.GL_TEXTURE_2D, VisualizerBox.audioTexture);

        // Tell the texture uniform sampler to use this texture in //the shader by binding to texture unit 0.
        GLES20.glUniform1i(textureParam, 0);

        Matrix.multiplyMM(modelView, 0, view, 0, RenderObject.model, 0);
        Matrix.multiplyMM(modelViewProjection, 0, perspective, 0, modelView, 0);
        // Set the ModelViewProjection matrix for eye position.
        GLES20.glUniformMatrix4fv(MVPParam, 1, false, modelViewProjection, 0);

        GLES20.glUniform4fv(colorParam, 1, borderColor, 0);
        GLES20.glUniform1f(widthParam, borderWidth);

        //Set vertex attributes
        GLES20.glVertexAttribPointer(positionParam, 3, GLES20.GL_FLOAT, false, 0, vertexBuffer);
        GLES20.glVertexAttribPointer(texCoordParam, 2, GLES20.GL_FLOAT, false, 0, texCoordBuffer);

        GLES20.glDrawElements(GLES20.GL_TRIANGLES, numIndices, GLES20.GL_UNSIGNED_SHORT, indexBuffer);

        RenderBox.checkGLError("WaveformMaterial draw");
    }
```

还有一件事；让我们提供一个销毁现有材质的方法：

```kt
    public static void destroy(){
        program = -1;
    }
```

## 波形可视化

现在我们可以创建一个新的可视化对象。在`visualizations/`文件夹下，创建一个名为`WaveformVisualization`的新的 Java 类，并将其定义为`extends Visualization`：

```kt
public class WaveformVisualization extends Visualization {
    static final String TAG = "WaveformVisualization";
    public WaveformVisualization(VisualizerBox visualizerBox) {
        super(visualizerBox);
    }
    @Override
    public void setup() {
    }
    @Override
    public void preDraw() {
    }
    @Override
    public void postDraw() {
    }
}
```

声明我们将要创建的`Plane`组件的变量：

```kt
    RenderObject plane;
```

在`setup`方法中创建如下。将材质设置为新的`WaveformMaterial`，并将其位置设置为靠左：

```kt
    public void setup() {
        plane = new Plane().setMaterial(new WaveformMaterial()
                .setBuffers(Plane.vertexBuffer, Plane.texCoordBuffer, Plane.indexBuffer, Plane.numIndices));

        new Transform()
                .setLocalPosition(-5, 0, 0)
                .setLocalRotation(0, 90, 0)
                .addComponent(plane);
    }
```

现在在`MainActivity`的`onCreate`中，用这个替换以前的可视化：

```kt
        visualizerBox.activeViz = new WaveformVisualization(visualizerBox);
```

当您运行项目时，您会得到这样的可视化：

![波形可视化](img/B05144_09_02.jpg)

# FFT 可视化

对于下一个可视化，我们将引入使用 FFT 数据（而不是波形数据）。与前一个示例一样，我们将从数据动态生成纹理，并编写材质和着色器来渲染它。

## 捕获 FFT 音频数据

首先，我们需要将数据捕获添加到我们的`VisualizerBox`类中。我们将首先添加我们需要的变量：

```kt
    public static byte[] fftBytes, fftNorm;
    public static float[] fftPrep;
    public static int fftTexture = -1;
```

我们需要分配 FFT 数据数组，并且为此我们需要知道它们的大小。我们可以询问 Android `Visualizer` API 它能够给我们多少数据。现在，我们将选择最小的大小，然后分配数组如下：

```kt
    public VisualizerBox(final CardboardView cardboardView){
        . . .
        fftPrep = new float[captureSize / 2];
        fftNorm = new byte[captureSize / 2];
        ...
```

捕获 FFT 数据类似于捕获波形数据。但是在保存之前，我们将对其进行一些预处理。根据 Android `Visualizer` API 文档，([http://developer.android.com/reference/android/media/audiofx/Visualizer.html#getFft(byte[]](http://developer.android.com/reference/android/media/audiofx/Visualizer.html#getFft(byte[])) `getFfT`函数提供以下指定的数据：

+   捕获是 8 位幅度 FFT；覆盖的频率范围是 0（DC）到`getSamplingRate()`返回的采样率的一半

+   捕获返回与捕获大小的一半加一相等的频率点的实部和虚部

### 注意

请注意，只有实部返回给第一个点（DC）和最后一个点（*采样频率/2*）。

返回的字节数组中的布局如下：

+   *n*是`getCaptureSize()`返回的捕获大小

+   `Rfk`和`Ifk`分别是第*k*频率分量的实部和虚部

+   如果`Fs`是`getSamplingRate()`返回的采样频率，则第*k*频率为：*(k*Fs)/(n/2)*

同样，我们将把传入的捕获数据准备成一个在 0 到 255 之间的归一化值数组。我们的实现如下。在`OnDataCaptureListener`实例中的`onWaveFormDataCapture`方法之后立即添加`onFftDataCapture`声明：

```kt
            @Override
            public void onFftDataCapture(Visualizer visualizer, byte[] bytes, int samplingRate) {
                fftBytes = bytes;
                float max = 0;
                for(int i = 0; i < fftPrep.length; i++) {
                    if(fftBytes.length > i * 2) {
                        fftPrep[i] = (float)Math.sqrt(fftBytes[i * 2] * fftBytes[i * 2] + fftBytes[i * 2 + 1] * fftBytes[i * 2 + 1]);
                        if(fftPrep[i] > max){
                            max = fftPrep[i];
                        }
                    }
                }
                float coeff = 1 / max;
                for(int i = 0; i < fftPrep.length; i++) {
                    if(fftPrep[i] < MIN_THRESHOLD){
                        fftPrep[i] = 0;
                    }
                    fftNorm[i] = (byte)(fftPrep[i] * coeff * 255);
                }
                loadTexture(cardboardView, fftTexture, fftNorm);
            }
```

请注意，我们的算法使用`MIN_THRESHOLD`值为 1.5 来过滤掉不重要的值：

```kt
    final float MIN_THRESHOLD = 1.5f;
```

现在在`setup()`中，用生成的纹理初始化`fftTexture`，就像我们对`audioTexture`变量做的那样：

```kt
    public void setup() {
        audioTexture = genTexture();
 fftTexture = genTexture();
        if(activeViz != null)
            activeViz.setup();
    }
```

## FFT 着色器

现在我们需要编写着色器程序。

如果需要，为着色器创建一个资源目录`res/raw/`。`fft_vertex.shader`与之前创建的`waveform_vertext.shader`相同，因此可以直接复制它。

对于`fft_fragment`着色器，我们添加了一些逻辑来决定当前坐标是否正在渲染。在这种情况下，我们没有指定宽度，只是渲染所有低于该值的像素。从某种角度来看，我们的波形着色器是一条线图（实际上是一个散点图），而我们的 FFT 着色器是一个条形图。

文件：`res/raw/fft_fragment.shader`

```kt
precision mediump float;        // default medium precision
uniform sampler2D u_Texture;    // the input texture

varying vec2 v_TexCoordinate;   // interpolated texture coordinate per fragment
uniform vec4 u_Color;

void main() {
    vec4 color;
    if(v_TexCoordinate.y < texture2D(u_Texture, v_TexCoordinate).r){
        color = u_Color;
    }
    gl_FragColor = color;
}
```

## 基本 FFT 材质

`FFTMaterial`类的代码与`WaveformMaterial`类的代码非常相似。因此，为了简洁起见，只需将该文件复制到一个名为`FFTMaterial.java`的新文件中。然后，修改如下。

确保类名和构造方法名称现在读作`FFTMaterial`：

```kt
public class FFTMaterial extends Material {
    private static final String TAG = "FFTMaterial";
    ...

    public FFTMaterial(){
    ...
```

我们决定将`borderColor`数组更改为不同的色调：

```kt
    public float[] borderColor = new float[]{0.84f, 0.65f, 1f, 1f};
```

在`setupProgram`中，确保您引用了`R.raw.fft_vertex`和`R.raw.fft_fragment`着色器：

```kt
        program = createProgram( R.raw.fft_vertex, R.raw.fft_fragment);
```

然后，确保正在设置适当的特定于着色器的参数。这些着色器使用`u_Color`（但没有`u_Width`变量）：

```kt
    //Shader-specific parameters
    textureParam = GLES20.glGetUniformLocation(program, "u_Texture");
    MVPParam = GLES20.glGetUniformLocation(program, "u_MVP");
    colorParam = GLES20.glGetUniformLocation(program, "u_Color");
    RenderBox.checkGLError("FFT params");
```

现在，在`draw`方法中，我们将使用`VisualizerBox.fftTexture`值进行绘制（而不是`VisualizerBox.audioTexture`），因此将调用`GLES20.glBindTexture`更改如下：

```kt
GLES20.glBindTexture(GLES20.GL_TEXTURE_2D, VisualizerBox.fftTexture);
```

确保`colorParam`参数已设置（但与`WaveformMaterial`类不同，这里没有宽度参数）：

```kt
GLES20.glUniform4fv(colorParam, 1, borderColor, 0);
```

## FFT 可视化

现在我们可以为 FFT 数据添加可视化。在`visualizations/`文件夹中，将`WaveformVisualization.java`文件复制到一个名为`FFTVisualization.java`的新文件中。确保它定义如下：

```kt
    public class FFTVisualization extends Visualization {
```

在它的`setup`方法中，我们将创建一个`Plane`组件，并使用`FFTMaterial`类对其进行纹理处理，如下所示（还要注意修改位置和旋转值）：

```kt
    public void setup() {
        plane = new Plane().setMaterial(new FFTMaterial()
                .setBuffers(Plane.vertexBuffer, Plane.texCoordBuffer, Plane.indexBuffer, Plane.numIndices));

        new Transform()
                .setLocalPosition(5, 0, 0)
                .setLocalRotation(0, -90, 0)
                .addComponent(plane);
    }
```

现在在`MainActivity`的`onCreate`中，用这个替换以前的可视化：

```kt
visualizerBox.activeViz = new FFTVisualization(visualizerBox);
```

当你运行这个项目时，我们得到了一个像这样的可视化，旋转并定位到右边：

![FFT 可视化](img/B05144_09_03.jpg)

这个简单的例子说明了 FFT 数据将音频的空间频率分离成离散的数据值。即使不理解底层的数学（这是非常复杂的），通常只需要知道数据随着音乐的变化和流动。我们在这里使用它来驱动纹理映射。FFT 也可以像我们在第一个例子中使用波形数据一样，用来驱动场景中 3D 对象的属性，包括位置、比例和旋转，以及参数化定义的几何形状。事实上，它通常是更好的数据通道。每个条形图对应一个单独的频率范围，因此您可以指定某些对象对高频率和低频率做出响应。

# 迷幻轨迹模式

如果你渴望致幻的模拟，我们将在我们的可视化中引入一个“迷幻轨迹模式”！这个实现被添加到了`RenderBox`库本身。如果你正在使用已完成的`RenderBox`库，那么只需在你的应用程序中切换到这个模式。例如，在`MainActivity`的`setup()`中，在最后添加以下代码行：

```kt
        RenderBox.mainCamera.trailsMode = true;
```

要在你的`RenderBox`库的副本中实现它，打开 Android Studio 中的那个项目。在`Camera`类（`components/Camera.java`文件）中添加`public boolean trailsMode`：

```kt
    public boolean trailsMode;
```

然后，在`onDrawEye`中，我们不再擦除新帧的屏幕，而是在整个帧上绘制一个全屏四边形，带有 alpha 透明度，从而留下上一帧的幽灵般的淡化图像。每个后续帧都会被更多的半透明黑色覆盖，导致它们随着时间的推移而淡出。定义颜色值如下：

```kt
public static float[] customClearColor = new float[]{0,0,0,0.05f};
```

然后，修改`onDrawEye`，使其如下所示：

```kt
    public void onDrawEye(Eye eye) {
 if(trailsMode) {
 GLES20.glEnable(GLES20.GL_BLEND);
 GLES20.glBlendFunc(GLES20.GL_SRC_ALPHA, GLES20.GL_ONE_MINUS_SRC_ALPHA);
 customClear(customClearColor);
 GLES20.glEnable(GLES20.GL_DEPTH_TEST);
 GLES20.glClear(GLES20.GL_DEPTH_BUFFER_BIT);
 } else {
            GLES20.glEnable(GLES20.GL_DEPTH_TEST);
            GLES20.glClear(GLES20.GL_COLOR_BUFFER_BIT | GLES20.GL_DEPTH_BUFFER_BIT);
        }

        ...
```

`customClear`方法跳过清除调用，保留上一帧的颜色。相反，它只是绘制一个半透明的全屏黑色四边形，略微变暗每一帧的“旧”图像。在我们这样做之前，相机需要一个着色器程序来绘制全屏的纯色。

`fullscreen_solid_color_vertex.shader` 如下所示：

```kt
attribute vec4 v_Position;

void main() {
   gl_Position = v_Position;
}
```

`fullscreen_solid_color_fragment.shader` 如下所示：

```kt
precision mediump float;
uniform vec4 u_Color;

void main() {
    gl_FragColor = u_Color;
}
```

现在回到`Camera`组件。我们设置程序并定义一个全屏四边形网格，缓冲区和其他变量。首先，我们定义我们需要的变量：

```kt
    static int program = -1;
    static int positionParam, colorParam;
    static boolean setup;
    public static FloatBuffer vertexBuffer;
    public static ShortBuffer indexBuffer;
    public static final int numIndices = 6;
    public boolean trailsMode;

    public static final float[] COORDS = new float[] {
            -1.0f, 1.0f, 0.0f,
            1.0f, 1.0f, 0.0f,
            -1.0f, -1.0f, 0.0f,
            1.0f, -1.0f, 0.0f
    };
    public static final short[] INDICES = new short[] {
            0, 1, 2,
            1, 3, 2
    };
    public static float[] customClearColor = new float[]{0,0,0,0.05f};
```

然后，定义一个设置程序的方法：

```kt
    public static void setupProgram(){
        if(program > -1)    //This means program has been set up //(valid program or error)
            return;
        //Create shader program
        program = Material.createProgram(R.raw.fullscreen_solid_color_vertex, R.raw.fullscreen_solid_color_fragment);

        //Get vertex attribute parameters
        positionParam = GLES20.glGetAttribLocation(program, "v_Position");

        //Enable vertex attribute parameters
        GLES20.glEnableVertexAttribArray(positionParam);

        //Shader-specific parameters
        colorParam = GLES20.glGetUniformLocation(program, "u_Color");

        RenderBox.checkGLError("Fullscreen Solid Color params");
    }
```

定义一个分配缓冲区的方法：

```kt
    public static void allocateBuffers(){
        setup = true;
        vertexBuffer = RenderObject.allocateFloatBuffer(COORDS);
        indexBuffer = RenderObject.allocateShortBuffer(INDICES);
    }
```

然后，从`Camera`初始化器中调用这些方法：

```kt
    public Camera(){
        transform = new Transform();
 setupProgram();
 allocateBuffers();
    }
```

最后，我们可以实现`customClear`方法：

```kt
    public static void customClear(float[] clearColor){
        GLES20.glUseProgram(program);
        // Set the position buffer
        GLES20.glVertexAttribPointer(positionParam, 3, GLES20.GL_FLOAT, false, 0, vertexBuffer);
        GLES20.glUniform4fv(colorParam, 1, clearColor, 0);
        GLES20.glDrawElements(GLES20.GL_TRIANGLES, numIndices, GLES20.GL_UNSIGNED_SHORT, indexBuffer);
    }
```

重新构建`RenderBox`模块，并将库文件复制回`VisualizeVR`项目。不要忘记将`trailsMode`设置为`true`！

现在当你运行这个应用程序时，它看起来很迷幻和酷！

![迷幻轨迹模式](img/B05144_09_04.jpg)

# 多个同时的可视化

现在我们有了一系列的可视化，我们可以增强应用程序，使其能够同时运行多个可视化，并在它们之间进行切换。

为了支持多个并发的可视化，用`visualizations`列表替换`VisualizerBox`中的`activeViz`变量。

```kt
public List<Visualization> visualizations = new ArrayList<Visualization|();
```

然后，在使用它的每个`VisualizerBox`方法中循环列表。我们总是希望设置所有这些，但只绘制（`preDraw`，`postDraw`）活动的可视化：

```kt
    public void setup() {
        audioTexture = genTexture();
        fftTexture = genTexture();
        for (Visualization viz : visualizations) {
            viz.setup();
        }
    }
    public void preDraw() {
        for (Visualization viz : visualizations) {
            viz.preDraw();
        }
    }
    public void postDraw() {
        for (Visualization viz : visualizations) {
            viz.postDraw();
        }
    }
```

我们可以在`MainActivity`中控制场景。修改`MainActivity`类的`onCreate`方法来填充`visualizations`列表，如下所示：

```kt
visualizerBox = new VisualizerBox(cardboardView);
visualizerBox.visualizations.add( new GeometricVisualization(visualizerBox));
visualizerBox.visualizations.add( new WaveformVisualization(visualizerBox));
visualizerBox.visualizations.add( new FFTVisualization(visualizerBox));
```

运行项目，我们有一个充满可视化的 3D 场景！

![多个同时可视化](img/B05144_09_05.jpg)

# 随机可视化

我们可以通过随着时间的推移添加和删除可视化来在可视化之间切换。在以下示例中，我们从一个活动可视化开始，然后每隔几秒切换一个随机可视化。

首先，在抽象的`Visualization`类中添加一个`activate`方法，它接受一个布尔类型的`enabled`参数。布尔类型的`active`变量是只读的：

```kt
    public boolean active = true;
    public abstract void activate(boolean enabled);
```

其实现将取决于具体的可视化。`RenderBox`库提供了一个`enabled`标志，用于渲染对象。最容易的是那些实例化单个`Plane`组件的可视化，比如`WaveformVisualization`和`FFTVisualization`。对于这些，添加以下代码：

```kt
    @Override
    public void activate(boolean enabled) {
        active = enabled;
        plane.enabled = enabled;
    }
```

对于`GeometricVisualization`类，我们可以启用（和禁用）每个组件立方体：

```kt
    @Override
    public void activate(boolean enabled) {
        active = enabled;
        for(int i = 0; i < cubes.length; i++) {
            cubeRenderers[i].enabled = enabled;
        }
    }
```

现在我们可以在`MainActivity`类中控制这个。

从不活动的`visualizations`开始。将此初始化添加到`MainActivity`的`setup()`中：

```kt
        for (Visualization viz : visualizerBox.visualizations) {
            viz.activate(false);
        }
```

在`MainActivity`的`preDraw`中，我们将检查当前时间（使用`RenderBox`库的`Time`类），并在每 3 秒后切换一个随机可视化。首先，在类的顶部添加一些变量：

```kt
    float timeToChange = 0f;
    final float CHANGE_DELAY = 3f;
    final Random rand = new Random();
```

现在我们可以修改`preDraw`来检查时间并修改`visualizations`列表：

```kt
    public void preDraw() {
        if (Time.getTime() > timeToChange) {
            int idx = rand.nextInt( visualizerBox.visualizations.size() );
            Visualization viz = visualizerBox.visualizations.get(idx);
            viz.activate(!viz.active);
            timeToChange += CHANGE_DELAY;
        }
        visualizerBox.preDraw();
    }
```

类似的时间控制结构（或增量时间）可以用于实现许多种动画，比如改变可视化对象的位置、旋转和/或比例，或者随着时间的推移演变几何本身。

# 进一步的增强

我们希望我们给了你一些工具，让你开始制作自己的音乐可视化。正如我们在本章中所建议的，选项是无限的。不幸的是，空间限制我们在这里编写更多有趣的代码。

+   **动画**：我们对每个可视化应用了最简单的变换：简单的位置、比例，也许是 90 度的旋转。当然，位置、旋转和比例可以进行动画处理，即与音乐协调更新每一帧，或者独立于音乐使用`Time.deltaTime`。东西可以在你周围虚拟飞来飞去！

+   **高级纹理和着色器**：我们的着色器和数据驱动纹理是最基本的：基本上渲染与音频字节值对应的单色像素。音频数据可以输入到更复杂和有趣的算法中，以生成新的图案和颜色，或者用于变形预加载的纹理。

+   **纹理映射**：项目中的纹理材料只是映射到一个平面上。嘿，伙计，这是虚拟现实！将纹理映射到一个全景照片或其他几何图形上，完全沉浸你的用户。

+   **渲染到纹理**：我们的轨迹模式对于这些可视化看起来还不错，但对于任何足够复杂的东西可能会变得一团糟。相反，您可以将其专门用于纹理平面的表面。设置 RTs 是复杂的，超出了本书的范围。基本上，您向场景引入另一个摄像机，指示 OpenGL 将后续绘制调用渲染到您创建的新表面，并将该表面用作要渲染到其上的对象的纹理缓冲区。RT 是一个强大的概念，可以实现反射和游戏内安全摄像头等技术。此外，您可以对表面应用变换，使轨迹看起来飞向远处，这是传统可视化器（如 MilkDrop）中的一种受欢迎的效果。

+   **参数几何**：音频数据可以用来驱动定义和渲染各种复杂度的 3D 几何模型。想想分形、晶体和 3D 多面体。参考 Goldberg 多面体（参考[`schoengeometry.com/`](http://schoengeometry.com/)）和神圣几何（参考[`www.geometrycode.com/sacred-geometry/`](http://www.geometrycode.com/sacred-geometry/)）以获取灵感。

## 社区邀请

我们邀请您与本书的其他读者和 Cardboard 社区分享您自己的可视化效果。一种方法是通过我们的 GitHub 存储库。如果您创建了一个新的可视化效果，请将其作为拉取请求提交到项目[`github.com/cardbookvr/visualizevr`](https://github.com/cardbookvr/visualizevr)，或者创建您自己的整个项目的分支！

# 总结

在本章中，我们构建了一个作为 Cardboard VR 应用程序运行的音乐可视化器。我们设计了一个通用架构，让您可以定义多个可视化效果，将它们插入到应用程序中，并在它们之间进行过渡。该应用程序使用 Android 的`Visualization` API 从手机当前的音频播放器中捕获波形和 FFT 数据。

首先，我们定义了`VisualizerBox`类，负责与 Android 的`Visualizer` API 的活动和回调函数。然后，我们定义了一个抽象的`Visualization`类来实现各种可视化效果。然后，我们将波形音频数据捕获到`VisualizerBox`中，并使用它来参数化地动画化一系列立方体，制作成一个 3D 波浪箱。接下来，我们编写了第二个可视化器；这次使用波形数据动态生成纹理，并使用材质着色器程序进行渲染。最后，我们捕获了 FFT 音频数据，并用它进行了第三个可视化。然后，我们增加了更多有趣的内容，比如迷幻轨迹模式和多个并发的可视化效果，随机进行过渡。

我们承认，视觉示例非常简单，但希望它们能激发您的想象力。我们挑战您构建自己的 3D 虚拟现实音乐可视化，也许利用了这个项目中的技术以及本书中的其他内容。

# 前往未来

我们希望您喜欢这个介绍和通过 Cardboard 虚拟现实开发的旅程。在整本书中，我们探索了 Google Cardboard Java SDK，OpenGL ES 2.0 图形和 Android 开发。我们涉及了许多 VR 最佳实践，并看到了在移动平台上进行低级图形开发的局限性。但是，如果您跟随我们的步伐，您已经成功地实现了一个合理的通用 3D 图形和 VR 开发库。您创建了各种各样的 VR 应用程序，包括应用程序启动器、太阳系模拟、360 度媒体画廊、3D 模型查看器和音乐可视化器。

自然地，我们期待 Cardboard Java SDK 从此时开始发生变化、演变和成熟。没有人真正知道未来会发生什么，甚至包括谷歌在内。然而，我们现在站在一个崭新未来的悬崖边。预测未来的最好方法就是帮助创造它。现在轮到你了！
