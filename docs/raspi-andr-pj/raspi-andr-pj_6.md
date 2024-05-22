# 第六章：车载树莓派

在本章中，我们将继续在树莓派上使用蓝牙来跟踪我们汽车的位置和数据。本章将涵盖以下部分：

+   查找汽车位置

+   使用您的 Android 设备作为访问点

+   收集汽车数据

+   将数据发送到云端

+   把所有东西放在一起

# 查找汽车位置

在本章中，我们将从我们的汽车收集发动机数据，但如果我们也能收集一些位置数据，事情将变得更加有趣。为此，我们将连接一个 USB GPS 接收器到树莓派，并通过这个设备接收我们的位置。我们将使用市场上最便宜的接收器之一，如下图所示：

![查找汽车位置](img/image00138.jpeg)

Globalsat BU-353 GPS 接收器

将 GPS 连接到树莓派后，您可以发出`lsusb`命令以查看它是否已正确注册。我的系统上此命令的输出如下，这里`Prolific`是 GPS 适配器：

```kt
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp.
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp.
Bus 001 Device 004: ID 148f:5370 Ralink Technology, Corp. RT5370 Wireless Adapter
Bus 001 Device 005: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port
Bus 001 Device 006: ID 0a5c:21e8 Broadcom Corp.

```

我们需要安装的下一件事是一个 GPS 守护程序，它从适配器接收位置信息：

```kt
sudo apt-get install gpsd gpsd-clients python-gps

```

您可能需要重新启动以启动守护程序。否则，您可以发出以下命令立即使其工作：

```kt
sudo gpsd /dev/ttyUSB0 -F /var/run/gpsd.sock

```

安装脚本甚至为我们提供了一个工具，通过基于文本的窗口查看当前 GPS 位置和范围内的卫星：

```kt
cgps –s

```

### 提示

GPS 接收器在室外或靠近窗户的地方可以最好地工作。

我系统上`cgps`命令的输出以及我的位置在以下截图中显示：

![查找汽车位置](img/image00139.jpeg)

从 cgps –s 命令的输出

在这里，您可以看到我特别在我的视野中拥有的 GPS 卫星，以及我的纬度和经度以及其他通过 GPS 系统可用的有用信息。

### 提示

如果您从`cgps`命令中收到超时错误，您需要使用以下命令重新启动 GPS 守护程序：

```kt
sudo killall gpsd
sudo gpsd /dev/ttyUSB0 -F /var/run/gpsd.sock

```

即使您已经重新启动了树莓派，如果您仍然收到此超时错误，则可以将以下命令放入**crontab**中，但是，还有一个更好的地方可以放置这些命令，稍后将进行描述：

```kt
@reboot sudo killall gpsd
@reboot sudo gpsd /dev/ttyUSB0 -F /var/run/gpsd.sock

```

还可以从 Python 程序中以编程方式获取位置信息。我们稍后将利用这种可能性。但是现在，以下 Python 代码在名为`getgps.py`的文件中测试 Python `gps`库：

```kt
#! /usr/bin/python

from gps import *
import math

gpsd = gps(mode=WATCH_ENABLE) #starting the stream of info

count = 0
while count < 10:  # wait max 50 seconds
    gpsd.next()
    if gpsd.fix.latitude != 0 and not math.isnan(gpsd.fix.latitude) :
        print gpsd.fix.latitude,gpsd.fix.longitude
        break
    count = count + 1
    time.sleep(5)
```

这个小程序唯一的作用是在有报告时输出 GPS 位置。我们可以使用`python getgps.py`命令来调用它。

# 收集汽车数据

为了收集汽车数据，我们将使用大多数汽车上都有的标准**车载** **诊断**（**OBD**）接口，欧洲称为 OBD-II 或 EOBD。这些是用于连接到汽车 OBD 端口的等效标准；您还可以从该端口读取有关汽车的诊断数据和故障代码。

### 注意

1996 年，OBD-II 规范对所有在美国制造和销售的汽车都是强制性的。欧盟在 2001 年跟随步伐，要求所有在欧盟销售的汽油车辆都必须使用 EOBD，随后在 2003 年要求所有柴油车辆也要使用 EOBD。2010 年，HDOBD（重型）规范对在美国销售的某些特定商用（非乘用车）发动机也是强制性的。甚至中国在 2008 年也跟随步伐，到那时，中国的一些轻型车辆被环保局要求实施 OBD。

在大多数汽车上，OBD 接口位于方向盘下方。在 2008 年的丰田 Aygo 上，它位于方向盘下方的右侧。一些汽车制造商没有标准的端口连接。因此，您可能需要购买额外的 OBD 转换电缆。汽车上的端口看起来像这样：

![收集汽车数据](img/image00140.jpeg)

汽车上的 OBD 连接

我们将连接一个**ELM327**-蓝牙发送器到这个 OBD 连接，以及连接到树莓派的上一章中的蓝牙适配器，让这两者进行通信。ELM327 是由**ELM Electronics**生产的一个用于翻译车载诊断（OBD）接口的程序化微控制器。ELM327 命令协议是最流行的 PC 到 OBD 接口标准之一。你可以在亚马逊上以不同价格购买到具有不同性能的这些硬件。我拥有的这个是由**Goliton**生产的：

![收集汽车数据](img/image00141.jpeg)

ELM 327-OBD 蓝牙发送器

从汽车获取数据的最简单方法是使用安卓上的一个应用程序，它可以为你翻译数据。在 Play 商店搜索 OBD，你会找到很多可以连接到 ELM327 并显示汽车数据细节的优秀应用程序。然而，我们想要比这更有趣。

## 将汽车数据传输到树莓派

要通过 Python 通过蓝牙从树莓派收集汽车数据，我们需要安装一些工具。运行以下更新命令来下载与蓝牙相关的软件包。请注意，我假设你已经安装了**新的 Raspbian**。相同的软件包也在之前的章节中安装过：

```kt
sudo apt-get install bluetooth bluez-utils blueman python-serial python-wxgtk2.8 python-wxtools wx2.8-i18n libwxgtk2.8-dev git-core --fix-missing

```

### 提示

你很可能现在就坐在车里工作。如果你正在努力想弄清楚如何连接到互联网，你可以随时使用你的安卓设备作为热点，并使用我们稍后在本章中需要的 Wi-Fi 适配器连接到互联网。

将树莓派连接到 Wi-Fi 网络的方法之前已经介绍过了，但让我们再次了解一下它是如何工作的。

将以下行添加到`/etc/wpa_supplicant/wpa_supplicant.conf`文件中。你需要配置热点以应用 WPA PSK 安全，而不是 PSK2：

```kt
network={
        ssid="YOUR_NETWORKID_FOR_HOTSPOT"
        psk="YOUR_PASSWORD_FOR_HOTSPOT"
}
```

现在，重新启动树莓派，几分钟后，你会发现它自动连接到安卓设备的热点。

再一次，我们可以使用`lsusb`命令来列出连接的 USB 设备。在我的系统上，输出如下所示：

```kt
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp.
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp.
Bus 001 Device 004: ID 148f:5370 Ralink Technology, Corp. RT5370 Wireless Adapter
Bus 001 Device 005: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port
Bus 001 Device 006: ID 0a5c:21e8 Broadcom Corp.

```

`005`设备是我从上一节中重复使用的蓝牙适配器。发出`hcitool scan`命令，看看是否可以连接到连接到汽车的 OBD 蓝牙设备：

```kt
Scanning ...
 00:1D:A5:15:A0:DC       OBDII

```

你也可以看到 OBD 设备的 MAC 地址；记下来，稍后会用到。

### 提示

如果你遇到问题，比如扫描或连接到 OBD，你可以使用以下命令来查看树莓派上连接的蓝牙适配器和`bluetooth`服务的状态：

```kt
hciconfig hci0 
/etc/init.d/bluetooth status 

```

让我们来看看以下命令：

```kt
/etc/init.d/bluetooth restart 

```

上述命令用于重新启动`bluetooth`服务。

现在，我们需要让`pi`用户访问蓝牙设备。编辑`/etc/group`文件，找到包含`bluetooth`文本的行，并在该行的末尾添加`pi`。它需要看起来类似于`bluetooth:x:113:pi`。

现在，使用`rfcomm`命令将树莓派的蓝牙适配器连接到 OBD 蓝牙设备。在连接到 OBD 之前，这个命令应该是你执行的第一件事。你可以在继续使用*Ctrl*+*C*组合键之前挂断：

```kt
sudo rfcomm connect hci0 00:1D:A5:15:A0:DC

```

在这里，你应该使用你自己的 OBD 蓝牙的 MAC 地址，我们之前使用`hcitool scan`命令找到了它。

现在，发出以下蓝牙配对命令，将树莓派与 OBD 配对，并使用 OBD 的 MAC 地址：

```kt
sudo bluez-simple-agent hci0 00:1D:A5:15:A0:DC

```

PIN 通常是`0000`或`1234`：

```kt
RequestPinCode (/org/bluez/2336/hci0/dev_00_1D_A5_15_A0_DC)
Enter PIN Code: 1234
Release
New device (/org/bluez/2336/hci0/dev_00_1D_A5_15_A0_DC)

```

在继续下一个命令之前，我们甚至应该添加`dbus`连接支持：

```kt
sudo update-rc.d -f dbus defaults
sudo reboot

```

让树莓派信任 OBD 设备，以便下次跳过手动配对，使用以下命令：

```kt
sudo bluez-test-device trusted 00:1D:A5:15:A0:DC yes

```

### 提示

如果你遇到任何问题，以下命令将让你测试连接。用你的 OBD 适配器的 MAC 地址替换 MAC 地址：

```kt
sudo l2ping 00:1D:A5:15:A0:DC
```

我们将使用一个名为`pyOBD-pi`的工具来访问 OBD dongle 提供的数据。使用`git`命令下载并启动记录器。这是一个更开发者友好的版本，位于[`github.com/peterh/pyobd`](https://github.com/peterh/pyobd)：

```kt
git clone https://github.com/Pbartek/pyobd-pi
cd pyobd-pi
sudo python ./obd_recorder.py

```

### 提示

不要忘记打开点火开关。还要记得使用即将到来的命令通过蓝牙连接。最好将其放在 crontab 中，否则，您每次重新启动 Pi 时都需要使用它：

```kt
sudo rfcomm connect hci0 00:1D:A5:15:A0:DC &

```

该命令将将数据流量保存到`log`目录。如果出现关于`0100 response:CAN ERROR`的错误，则表示您在协议选择方面存在问题，您只需要编辑`obd_io.py`文件并找到以下行：

```kt
self.send_command("0100")
```

然后，在此之前添加以下代码行：

```kt
self.send_command("ATSP0")  # select auto protocol
wx.PostEvent(self._notify_window, DebugEvent([2,"ATSP0 response:" + self.get_result()]))
```

通过这种方式，我们已经强制选择通信协议。

### 提示

您可能希望在重新启动时运行`init`服务器脚本。您不能将其放在 cronbtab 中，因为在运行时蓝牙或 GPS 可能尚未准备就绪。将命令放在`/etc/rc.local`文件的末尾，而不是在退出行之前：

```kt
sudo killall gpsd
sudo gpsd /dev/ttyUSB0 -F /var/run/gpsd.sock
sudo rfcomm connect hci0 00:1D:A5:15:A0:DC &

```

# 将您的安卓设备用作访问点

在将迄今为止收集的数据发送到云端之前，我们需要将 Pi 连接到互联网。将安卓设备设置为互联网访问点或热点非常简单，可以从设备的设置中完成。然后，我们可以将 Pi 连接到安卓提供的网络。但是，这种设置存在一个主要问题。首先，我们希望能够一直将 Pi 和手机留在车上。一旦汽车启动，我们希望数据能够自动发送，而不想携带 Pi 和手机。但是，如果我们将手机留在车上并将其连接到 12V 电源输出，设备很快就会耗尽电池并关闭。然后，我们需要手动将其打开并再次更改热点设置。我们希望所有这些步骤都能自动进行。因此，我们需要一种方法，可以在连接到电源源时或连接到的电源源，例如车上的 12V 电源输出，在我们启动汽车时唤醒设备。我现在将介绍的技术要求您对安卓设备拥有超级用户权限，这意味着我们需要对设备进行 Rooting。

## Rooting 的替代方法

使用 USB Wi-Fi 3G 调制解调器作为根设备的替代方法，以在汽车中获得互联网访问。请注意，市场上大多数 3G USB 调制解调器都不提供 Wi-Fi 网络。它们只为插入的计算机提供网络访问。我们需要的那种在连接到 USB 电源源时类似于 Wi-Fi 热点。您可以在在线零售商处找到这些产品，如亚马逊或速卖通。我个人使用的产品如下图所示：

![Rooting 的替代方法](img/image00142.jpeg)

USB Wi-Fi 3G 调制解调器

如果您选择使用其中一种，可以跳过本节的其余部分，直接转到下一节。

## Rooting Samsung Galaxy S2

有不同的方法可以对不同的设备进行 Rooting。我将使用市场上最常见的二手安卓设备之一，即三星 Galaxy S2。如果您有其他手机，互联网上有大量关于如何对每个设备进行 Rooting 的资源。最受欢迎的一个位于[`www.androidcentral.com/root`](http://www.androidcentral.com/root)，即**Android Central**网站。

### 注意

请注意，对设备进行 Rooting 将使保修无效。这可能会损坏您的手机，并且不是一个安全的过程。请自担风险。但是这里提供的步骤对我有用。在继续本章的其余部分之前，您应该备份您想要保留的任何文件。

按下*音量减*、*电源*、*主页*按钮，三星设备可以进入恢复模式。按下这些按钮，您将会看到三星标准的恢复屏幕上有一个警告标志。我们应该用另一个恢复程序替换这个标准恢复程序，因为标准恢复只能通过连接到 USB 的计算机来完成，并下载完整的操作系统映像。然而，我们真正需要做的是只用一个给我们超级用户权限的内核来替换内核。我们还要确保我们是从连接到安卓设备的 SD 卡上进行这个操作。这就是为什么我们需要替换三星的默认恢复程序。我们可以再次使用三星提供的恢复操作来完成这个操作。

当您将设备置于此恢复模式时，通过 USB 将其连接到计算机。接下来，我们可以下载一个名为**Odin**的软件，将一个新的恢复工具上传到手机上。它可以从互联网上的许多地方下载，有不同的版本。我们将使用的是名为`ODIN3_v1.85.zip`的版本，它位于[`www.androidfilehost.com/?fid=9390169635556426736`](https://www.androidfilehost.com/?fid=9390169635556426736)。我们需要的另一个文件是一个内核，用来替换现有的内核，帮助我们进行新的恢复操作。这个文件名为**Jeboo Kernel**，可以在[`downloadandroidrom.com/file/GalaxyS2/kernels/JB/jeboo_kernel_i9100_v1-2a.tar`](http://downloadandroidrom.com/file/GalaxyS2/kernels/JB/jeboo_kernel_i9100_v1-2a.tar)找到。

按照手机上恢复屏幕上的指示，按下*音量增*按钮将设备置于下载模式。然后，启动 Odin，并选择新下载的 Jeboo Kernel 作为**PDA**。如果手机正确连接并处于内核下载模式，您应该看到一个标记为黄色的 COM 框：

![三星 Galaxy S2 获取 Root 权限](img/image00143.jpeg)

Odin 显示 Jeboo 为 PDA，并显示连接到`COM11`的设备。点击**开始**上传您选择的新 Jeboo 内核。

在您收到**PASS**通知之前不应该花费太多时间：

![三星 Galaxy S2 获取 Root 权限](img/image00144.jpeg)

Odin 已成功完成

现在，您的手机应该重新启动，您应该在重新启动屏幕上看到一个警告三角形，表示您有一个带有“从 SD 卡恢复”功能的新内核。

下一步是将[`downloadandroidrom.com/file/tools/SuperSU/CWM-SuperSU-v0.99.zip`](http://downloadandroidrom.com/file/tools/SuperSU/CWM-SuperSU-v0.99.zip)上的**CWM Super User**文件保存到 SD 卡并将其连接到设备。现在，关闭设备并再次将其置于恢复模式，这次使用略有不同的按键组合，即*音量增*、*电源*、*主页*。请注意，我们按下*音量增*而不是之前的*音量减*。您将看到一个名为**CWM-based Recovery**的不同恢复屏幕。您可以使用*音量增*和*音量减*键上下滚动。使用*主页*按钮选择**安装 Zip**项目，然后选择**从 SD 卡选择**选项。您应该浏览到您已经下载到 SD 卡上的 CWM Super User ZIP 文件。最后，选择**是**。

重新启动设备，您将看到一个名为**Super User**的新应用程序，这表明您已成功获取 Root 权限。您甚至可以通过在 Google Play 上下载一个 Super User 检查器应用程序来验证您对设备的超级用户访问权限。您将看到一个消息框，询问您是否要授予超级用户权限给任何其他应用程序。

## 连接到电源后启用共享网络

由于我们的手机假设一直停在车上，并且只有在使用车辆时才会上电，因此我们需要找到一种方法，在手机连接到电源时启用 Wi-Fi 共享或热点。但我们可能会遇到两种情况：

+   电池已耗尽，手机在夜间关闭。在这种情况下，我们需要找到一种方法，以便在手机再次上电时打开手机。这发生在我们启动汽车时。当手机成功开机时，我们需要找到一种方法来启用热点。

+   手机仍然有足够的电池来保持其开启，但由于未被使用，热点已被禁用。请注意，使用手机热点的唯一设备是 Pi，如果车辆未被使用，则其将被关闭。当我们再次启动汽车时，手机将从 USB 接触点上电。在这种情况下，我们需要再次启用热点。

### 连接电源时自动重启

当我们将关闭的三星设备连接到电源时，我们将看到一个带有旋转箭头的灰色电池图像。然后，当它开始充电时，我们将看到另一个显示当前充电级别的彩色电池图像。这第二个图像是由一个程序生成的，该程序在关闭的设备开始充电电池时触发。它是手机上`/system/bin/playlpm`中的一个二进制文件。我们将更改此文件为我们自己的脚本以重新启动设备。为了能够编辑此文件，我们需要超级用户权限。这就是为什么我们对手机进行了 root。由于 Android 系统实际上是一个 Linux 操作系统，我们可以在其下运行任何 Linux 命令。我们可以使用一个可以从 Play 商店下载的应用程序来做到这一点，名为**终端模拟器**：

![连接电源时自动重启](img/image00145.jpeg)

终端模拟器应用程序屏幕

现在，发出以下命令来更改`playlpm`文件的内容并使其成为可执行文件。我们还需要重新挂载`/system`目录，以便对其进行写操作：

```kt
mount -o rw,remount /system
mv playlpm playlpmbackup
echo "#!/system/bin/sh" > playlpm
echo "sleep 60" >> playlpm
echo "/system/bin/reboot" >> playlpm
chmod 0755 /system/bin/playlpm
chown root.shell /system/bin/playlpm
mount -o ro,remount /system

```

关闭设备并将其连接到电源。您会看到它在一分钟后自动开机。我们引入了一分钟的延迟，因为如果电池完全放电，它将没有足够的容量来重新启动设备。在这种情况下，我们希望至少等待一分钟，让电池充电到足以重新启动设备。如果充电不足，您可能需要在设备可以自动重新启动之前充电手机。您可以将手机放入恢复模式并开始充电，而无需重新启动手机。

### 自动共享

现在我们可以在连接到电源时重新启动设备。当设备唤醒或连接到电源时，我们还需要启用设备上的共享。市场上已经有应用程序可以做到这一点，但最好的应用程序是收费的。这是我们要为此目的实现我们自己的应用程序的原因之一。另一个原因是这样做很有趣。

我们可以像以前一样在 Android Studio 中创建一个新的应用程序。对于这个应用程序，我们不需要任何`Activity`。

创建一个名为`StartTetheringAtBootReceiver`的新 java 文件，用于`BroadcastReceiver`，并在其中添加以下代码：

```kt
public class StartTetheringAtBootReceiver extends BroadcastReceiver {
   public static void setWifiTetheringEnabled(boolean enable, Context context) {
      WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);

      Method[] methods = 
         wifiManager.getClass().getDeclaredMethods();
       for (Method method : methods) {
        if (method.getName().equals("setWifiApEnabled")) {
            try {
                 method.invoke(wifiManager, null, enable);
             } catch (Exception ex) {
                ex.printStackTrace();
             }
             break;
         }
      }
   }
    @Override
   public void onReceive(Context context, Intent intent) {
      if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction()) || Intent.ACTION_POWER_CONNECTED.equals(intent.getAction())) {
         setWifiTetheringEnabled(true, context);
      }
   }
}
```

这段代码在手机启动或连接到电源时接收广播事件，并使用默认设置在设备上启用共享。如果我们想要更改网络名称或密码，我们需要修改设备上的设置。

将新广播接收器的清单定义添加到`AndroidManifest.xml`中的`application`标记内：

```kt
<receiver
android:name=".StartTetheringAtBootReceiver"
   android:label="StartTetheringAtBootReceiver">
   <intent-filter>
      <action android:name="android.intent.action.BOOT_COMPLETED" />
      <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
   </intent-filter>
</receiver>
```

在`manifest`标记内添加以下权限声明：

```kt
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

现在，将此应用程序安装到您的手机上，并查看每当您重新启动设备或将电源线连接到设备时，共享是否已启用。

我们可以在`MainActivity`中可选地添加一个用于连接的快捷按钮。在`activity_main.xml`文件中，添加以下按钮定义：

```kt
<Button android:text="@string/enable" 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" 
        android:onClick="click"/>
```

接下来，在`MainAcitivty.java`文件中，为按钮定义处理程序：

```kt
public void click(View v) {
   StartTetheringAtBootReceiver
      .setWifiTetheringEnabled(true, this);
}
```

接下来，我们需要将树莓派连接到我们迄今为止创建的热点。之前已经介绍了将树莓派连接到 Wi-Fi 网络，但让我们再次提醒自己这个概念。将以下代码添加到`/etc/wpa_supplicant/wpa_supplicant.conf`文件中。我们可以配置热点应用 WPA PSK 安全而不是 PSK2：

```kt
network={
        ssid="YOUR_NETWORKID_FOR_HOTSPOT"
        psk="YOUR_PASSWORD_FOR_HOTSPOT"
}
```

现在，我们将重新启动树莓派，几分钟后，我们将看到它自动连接到 Android 设备的热点，在热点设置窗口中：

![自动连接](img/image00146.jpeg)

在 Android 的热点设置中显示已连接设备的列表

你一定会想知道为什么我们在这个时候涵盖了这个内容。这是因为为了实现下一节，你很可能需要坐在你的车里，与你车内的树莓派进行通信，在那里你很可能没有比 Android 提供的热点更多的网络访问。现在，如果你从电脑上连接到 Android 上的同一个热点，你将能够使用一个叫做**PuTTY**的工具在 Windows 机器上安装，或者在 Mac 上使用内置的 SSH 终端工具来 SSH 到树莓派。

# 将数据发送到云端

我们将使用 Google Docs 电子表格来保存数据，并使用专门为此目的开发的 Python 库。我们首先通过创建一个 API 密钥来访问 Google 服务来实现这一点。

浏览[`console.developers.google.com/project`](https://console.developers.google.com/project)并为此目的创建一个帐户。当准备好时，您将被引导到 Google Developer Console：

![将数据发送到云端](img/image00147.jpeg)

Google Developer Console 的起始页面

在这里，我们需要在**选择项目**下拉菜单中创建一个新项目。给它一个合适的名字，接受协议，然后点击**创建**。选择新创建的项目，**APIs & auth**，然后从左侧菜单中选择**APIs**。然后，找到并选择**Drive API**，点击**启用 API**按钮。当它被启用后，转到**APIs & auth**下的左侧菜单中的**凭据**：

![将数据发送到云端](img/image00148.jpeg)

在 Google Developer Console 中启用 OAuth 的菜单

在这里，在**OAuth**下，点击**创建新的客户端 ID**按钮。在出现的消息框中，选择**服务帐户**，然后点击**创建客户端 ID**按钮。我们将看到一个框，告诉我们我们已成功为项目创建了**新的公共/私有密钥对**。我们甚至会看到网站已经向我们发送了一个带有凭据的**JSON**文件。对于我创建的虚拟帐户，内容看起来类似于这样：

```kt
{
  "private_key_id": "ed5a741ff85f235167015d99a1adc3033f0e6f9f",
  "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDM9YJ2otxwdhcL\nQJ8ipZOuILkq9dzWDJJgtjSgFUXTJvjgzTDNa2WXGy9p9i4Wuzrj5OJli/M5dMWr\n+CVZCpsfV7Xt7iqkeCEo0dN225HDiAXXMvWKhDsiofau0xLCTFLDnLZFWqAd55ec\naENYQKp6ZEc6dGaA7Kp7O1+7LtEB2a4yqgZIelL6fTSSLQqyV477OS2Dkq+nz5Sz\nRyTexcDWioDNp2vdGadqDfRKsI7ELwgWscaV6jrbHz2uDuC844UnTL4WKMugp1n1\nObTuGDl1gldEIWlk2XSLFkGfY30lYV7XwrUQGgc85AGRwdH7qYrQM3jO4D+6thAH\nETq4qjRRAgMBAAECggEAJjXXHrr6EdVSMnzXriPkRmA/ZSz1AMrTN0iAwx90Jwtq\n9q4KXSGajPM6gaytpvs83WO8eWX/8EQ+3fKjM9hwVwWJG1R9irACrpN/svb4U9W2\nEQqlEC/avngnfyxGoQaNn35F1OQyWaDlePlPJNLZdXvgc5tjyMFWfybwj/sIaCmR\nj5ntV2aY/gCEbe6km7L/LkC3C7CesIWstUGMHCjh2aPeQT+Hpodf23AnLZuSo34j\nB+lSI/RjnDsd0HfazOgaOXa/yK4SliTaMWUBiMSXQcwZZsVp/RL0Ve6W2PSfi092\n+hATaaRnA8zB8fx7PnAltPhFwVr9+jjbYbq+wypoyQKBgQDoLJytaR4wof46MUiL\nMWrXDopi5dG2ofUSXR+JEIThe7yyYepzzdWFL+rXNEzD5X9UcfCodwZ0PKLN3u0t\nZJ5Iq111bxwwZix5uVStRi6stgGaewF6nkDqN8y5TJJgnZB9wSBuG3RvCU4zwXKZ\ngj2+azWme7PSyOHKNODbBd9DkwKBgQDh/e7nct49/Z0Om/+kNJ+NXUjka+S1yF7n\nhL+HZ2WU1gL8iQjXPxnCX1lThw7C4rForH/esOs+f1XMje8NYi7ggslqxoXwFRH6\ny/tuCRaY+e62xmJAxj2o8InsvQQkSM+dtuZiaNq3gCatHKbx2C6SVQal/y3yuR0c\n00adgr6fCwKBgQDSlAvzGIFiWLsNqr+CR+sAbVbExm9EN3bhFgdROONc4+7M2BRe\nvlUoPMLCN9RcZR3syH8fPP1klc6P7N6vqjAJ9yuIJKOrnjA+owKTOjGBQn8HzwMT\nZM+536xWcIXfDWoNNQol887SGt2MAavgYYmA2RpLCq2Zw8tOrFE5NgU+8wKBgQCe\nAiwNy3S0JySu2EevidOcxYJ3ozBwIT6p5Vj81UBjBhdkdnOl+8qI6p3MFvwtKs8b\n/rARBeYU9ncI5Jwl4WYhN5CYhWGUcUb28bRERTp1jxpm1OJRo8ns2vG0gpvourfe\n78i5OdLixklEdGoNYjd9vNE/MuHveZpvUxFmg8m/7QKBgCGVTkOXWLpRxuYT+M+M\n28LBgftHxu0YZdXx8mU9x6LQYG2aFxho7bkEYiEaNYJn51kdNZqzrIHebxT/dh/z\nddd5nR93E6WsPuqstZF4ZhJ+l2m77wmG9u5gfRifrNpc3TK0IswydFPIMNVxMz+d\nl3cdqtiW6rvWSQoHC0brpcYL\n-----END PRIVATE KEY-----\n",
  "client_email": "14902682557-05eecriag0m9jbo50ohnt59sest5694d@developer.gserviceaccount.com",
  "client_id": "14902682557-05eecriag0m9jbo50ohnt59sest5694d.apps.googleusercontent.com",
  "type": "service_account"
}
```

我们可以选择使用 Developer Console 网站上的**生成新的 JSON 密钥**按钮为我们的项目生成新的密钥。

在这个阶段，我们需要使用**生成新的 P12 密钥**按钮生成一个**P12**密钥。这个文件将在以后使用。当我们下载文件时，还会提供一个秘钥，我们需要记下来。下面的截图展示了成功创建 API 密钥后的 Google Developer Console：

![将数据发送到云端](img/image00149.jpeg)

成功创建 API 密钥后的 Google Developer Console

在我们安装 Google Python 库之前，我们需要安装一个叫做`pip`的工具，它将帮助我们安装一个 OAuth 客户端，我们将用它来连接到 Google 服务。使用以下命令来完成这个过程：

```kt
curl -O https://raw.githubusercontent.com/pypa/pip/master/contrib/get-pip.py
sudo python get-pip.py

```

然后，使用这个新的`pip`工具来安装 OAuth 客户端：

```kt
sudo apt-get update
sudo apt-get install build-essential libssl-dev libffi-dev python-dev
sudo pip install --upgrade oauth2client
sudo pip install PyOpenSSL

```

下一步是下载并安装客户端库，以使用以下命令在树莓派上访问 Google Sheets：

```kt
git clone https://github.com/burnash/gspread.git
cd gspread
sudo python setup.py install

```

在开始编码之前，我们需要在[`docs.google.com`](https://docs.google.com)网站上添加一个新的电子表格。在菜单中选择**表格**，使用加号（**+**）号创建一个新表格，并将名称从`Untitled spreadsheet`更改为`CAR_OBD_SHEET`。它应该会自动保存。我们需要与我们生成 OAuth 密钥对时为我们创建的 Google 开发者控制台客户端共享此电子表格。我们将在我们下载的 JSON 文件中找到一个`client_email`字段。我们将与此客户端共享新的电子表格。现在，在 Google Docs 中打开`CAR_OBD_SHEET`电子表格，并单击**共享**按钮：

![将数据发送到云端](img/image00150.jpeg)

在 Google Docs 中打开电子表格

在弹出窗口中，粘贴 JSON 文件中的`client_email`，然后单击弹出窗口上的**发送**按钮。这将与在上一步创建 OAuth 密钥对时生成的客户端共享电子表格：

![将数据发送到云端](img/image00151.jpeg)

与生成的客户端共享电子表格

现在，我们将测试一下是否一切正常。在 Pi 上创建一个文件，命名为`send_to_sheet.py`，并将以下内容放入其中。不要忘记创建 OAuth JSON 文件，并将我们从 Google 开发者控制台下载的内容放入其中，并将其命名为`piandroidprojects.json`：

```kt
import json
import gspread
from datetime import datetime
from oauth2client.client import SignedJwtAssertionCredentials

json_key = json.load(open('piandroidprojects.json'))
scope = ['https://spreadsheets.google.com/feeds']

credentials = SignedJwtAssertionCredentials(json_key['client_email'], json_key['private_key'], scope)

gc = gspread.authorize(credentials)
t = datetime.now()
sh = gc.open("CAR_OBD_SHEET").add_worksheet(str(t.year) + "_" + str(t.month) + "_" + str(t.day) + "_" + str(t.hour) + "_" + str(t.minute) + "_" + str(t.second), 100, 20)

sh.update_cell(1, 1, 0.23)
```

现在，使用`python send_to_sheet.py`命令运行该文件，我们将在 Google Docs 表格上看到更新。该代码将创建一个名为当前时间戳的新工作表，并在该工作表中保存一个单个值。请注意，Google 允许每个表格 200 个工作表，默认情况下每个工作表 100 行；在我们的代码中，每次运行时都会创建一个新的工作表。我们需要定期清理工作表，以免超出限制。

# 将所有内容放在一起

在接下来的两个部分中，我们将总结到目前为止所做的工作。首先，我们将开始将数据发送到 Google Docs 表格。然后，我们将构建一个 Android 应用程序来在地图上显示数据。

## 发送测量数据

我们将使用一个 Python 脚本来访问 Pi 上的 GPS 数据，我们需要在系统重新启动时运行该脚本。为此，在`/etc/rc.local`文件的末尾添加以下代码：

```kt
sudo killall gpsd
sudo gpsd /dev/ttyUSB0 -F /var/run/gpsd.sock
sudo rfcomm connect hci0 00:1D:A5:15:A0:DC &
sleep 1m
current_time=$(date "+%Y.%m.%d-%H.%M.%S")
file_name=/home/pi/log_sender.txt
new_filename=$file_name.$current_time
sudo /home/pi/pyobd-pi/sender.py > $new_filename 2>&1 &
```

在这里，我们可以重新启动 GPS 服务，连接到 OBD 蓝牙适配器，创建日志文件，并启动我们将在下一步实现的`sender.py`脚本：

```kt
#!/usr/bin/env python

import obd_io
from datetime import datetime
import time
import threading
import commands
import time
from gps import *
import math
import json
import gspread
from oauth2client.client import SignedJwtAssertionCredentials

gpsd = None

class GpsPoller(threading.Thread):
  def __init__(self):
    threading.Thread.__init__(self)
    global gpsd 
    gpsd = gps(mode=WATCH_ENABLE) 

  def run(self):
    global gpsd
    while True:
      gpsd.next()

class OBD_Sender():
    def __init__(self):
        self.port = None
        self.sensorlist = [3,4,5,12,13,31,32]

    def connect(self):
        self.port = obd_io.OBDPort("/dev/rfcomm0", None, 2, 2)
        if(self.port):
            print "Connected to "+str(self.port)

    def is_connected(self):
        return self.port

    def get_data(self):
        if(self.port is None):
            return None
        current = 1
        while 1:
            cell_list = []

            localtime = datetime.now()
            cell = sh.cell(current, 1)
            cell.value = localtime
            cell_list.append(cell)

            try:
                gpsd.next()
            except:
                print "gpsd.next() error"

            cell = sh.cell(current, 2)
            cell.value = gpsd.fix.latitude
            cell_list.append(cell)

            cell = sh.cell(current, 3)
            cell.value = gpsd.fix.longitude
            cell_list.append(cell)

            column = 4
            for index in self.sensorlist:
                (name, value, unit) = self.port.sensor(index)
                cell = sh.cell(current, column)
                cell.value = value
                cell_list.append(cell)
                column = column + 1

            try:
                sh.update_cells(cell_list)
                print "sent data"
            except:
                print "update_cells error"

            current = current + 1
            time.sleep(10)

json_key = json.load(open('/home/pi/pyobd-pi/piandroidprojects.json'))
scope = ['https://spreadsheets.google.com/feeds']

credentials = SignedJwtAssertionCredentials(json_key['client_email'], json_key['private_key'], scope)

while True:
    try:
        gc = gspread.authorize(credentials)
        break
    except:
        print "Error in GoogleDocs authorize"

t = datetime.now()
sh = gc.open("CAR_OBD_SHEET").add_worksheet(str(t.year)+"_"+str(t.month)+"_"+str(t.day)+"_"+str(t.hour)+"_"+str(t.minute)+"_"+str(t.second), 100, 20)

gpsp = GpsPoller()
gpsp.start()

o = OBD_Sender()
o.connect()
time.sleep(5)
o.connect()
time.sleep(5)
o.get_data()
```

代码从定义`json_key`的末尾开始运行，加载 JSON 密钥文件。然后，我们将尝试使用`gspread.authorize(credentials)`方法进行授权。下一步是创建一个以日期时间戳为标题的新工作表，然后在由`GpsPoller`类定义的另一个线程中开始消耗 GPS 数据。接下来，我们将初始化`OBD_Sender`类并两次连接到 OBD 蓝牙设备。当执行连接操作时，第一次可能会失败，但第二次几乎总是成功。然后，我们需要运行`OBD_Sender`类的`get_data`方法来开始循环。

`GpsPoller`类消耗了连接到串行 USB 端口的 GPS 设备的所有值。这是为了在访问`gpsd.fix.latitude`和`gpsd.fix.longitude`变量时获得最新的值。

`OBD_Sender`类的`get_data`方法将本地时间、纬度和经度值发送到电子表格，并且还发送了在`self.sensorlist = [3,4,5,12,13,31,32]`中定义的七个不同的读数。我们可以在`obd_sensors.py`文件的`SENSORS`列表中看到这些值。供您参考，这些是燃油系统状态、计算负荷值、冷却液温度、发动机转速、车速、发动机启动分钟和发动机运行 MIL 值。我们可以更改索引以读取我们想要的值。在[`en.wikipedia.org/wiki/OBD-II_PIDs`](https://en.wikipedia.org/wiki/OBD-II_PIDs)上查看其他值。我们遍历这些代码，读取它们的当前值，并将它们发送到工作表的当前行的不同单元格中。启动并驾驶您的汽车后，您会看到数据上传到电子表格，如下面的屏幕截图所示：

![发送测量值](img/image00152.jpeg)

数据上传到电子表格

## 检索测量值

我们将构建我们自己的应用程序来下载测量值并在地图上显示它们。在 Android Studio 中创建一个新的空白项目，并在创建项目向导的最后一步选择包含 Google 地图活动。我将 Android 4.3 作为此项目的基本 SDK；我将把我的主要活动命名为`MapsActivity`。

为了访问 Google 文档并下载电子表格的内容，我们将使用 Google 提供的一些 Java 库。它们位于不同的位置。从以下位置下载 ZIP 文件：

+   Google 数据服务的通用 Java 客户端位于[`github.com/google/gdata-java-client`](https://github.com/google/gdata-java-client)，文件名为`gdata-src.java-*.zip`，位于**Source**链接下。

+   从[`developers.google.com/api-client-library/java/google-http-java-client/download`](https://developers.google.com/api-client-library/java/google-http-java-client/download)下载 HTTP 客户端，名称为`google-http-java-client-featured.zip`。我们将使用这个来进行授权。

+   从[`developers.google.com/api-client-library/java/google-oauth-java-client/download`](https://developers.google.com/api-client-library/java/google-oauth-java-client/download)下载包含 OAuth 客户端的`google-oauth-java-client-featured.zip`。

现在，打开这些 ZIP 文件，找到以下 JAR 库，并将它们移动到 Android `app`目录下的`libs`文件夹中：

+   `gdata-base-1.0.jar`

+   `gdata-core-1.0.jar`

+   `gdata-spreadsheet-3.0.jar`

+   `google-api-client-1.20.0.jar`

+   `google-http-client-1.20.0.jar`

+   `google-http-client-jackson-1.20.0.jar`

+   `google-oauth-client-1.20.0.jar`

+   `guava-11.0.2.jar`

+   `jackson-core-asl-1.9.11.jar`

要将这些库包含在您的 Android 项目中，您需要将它们添加到`build.gradle`文件的`Module:app`下。为此，请在`dependencies`标签下添加以下代码。

```kt
compile files('libs/gdata-spreadsheet-3.0.jar')
compile files('libs/gdata-core-1.0.jar')
compile files('libs/guava-11.0.2.jar')
compile files('libs/gdata-base-1.0.jar')
compile files('libs/google-http-client-1.20.0.jar')
compile files('libs/google-http-client-jackson-1.20.0.jar')
compile files('libs/google-api-client-1.20.0.jar')
compile files('libs/google-oauth-client-1.20.0.jar')
compile files('libs/jackson-core-asl-1.9.11.jar')
```

当您编辑`build.gradle`文件时，您可能会在 Android 中收到一条消息，指出**Gradle 文件自上次项目同步以来已更改。可能需要进行项目同步以使 IDE 正常工作**。单击附近的**立即同步**链接以更新项目。

下一步是移动我们从 Google 开发者控制台下载的`P12`密钥文件，并将其包含在我们的 Android 项目中。我们需要将此文件复制到位于`PROJECT_HOME\app\src\main\res\raw`的`raw`目录中，并将其重命名为`piandroidprojects.p12`。

由于我们计划在地图上显示内容，因此我们将使用 Google 的地图 API。为了使用它，我们需要一个访问 API 密钥。再次转到开发者控制台 [`console.developers.google.com/project`](https://console.developers.google.com/project)，并选择我们之前创建的项目。在左侧的菜单中，选择 **APIs** 下的 **APIS & auth**，然后选择 **Google Maps Android API**，最后，单击 **启用 API** 按钮。接下来，转到 **凭据**，并在 **公共 API 访问** 部分下单击 **创建新密钥** 按钮。我们需要在弹出的窗口中选择 **Android 密钥**。复制生成的 **API 密钥**，并将其替换为 `google_maps_api.xml` 文件中的 `YOUR_KEY_HERE` 字符串。现在，我们已经准备好了我们的 Android 项目设置，现在是编写代码的时候了。

代码中要做的第一件事是从 Google Docs 下载工作表列表。每次 Pi 重新启动时都会有一个工作表。将以下代码添加到 `MapsActivity.java` 文件的 `onCreate` 方法中：

```kt
new RetrieveSpreadsheets().execute();
```

这段代码将创建一个异步任务，该任务实现为 Android 的 `AsyncTask`，用于下载和显示电子表格。让我们在同一文件中定义任务类：

```kt
class RetrieveSpreadsheets extends AsyncTask<Void, Void, List<WorksheetEntry>> {
   @Override
   protected List<WorksheetEntry> doInBackground(Void params) {
      try {
         service = 
            new SpreadsheetService("MySpreadsheetIntegration-v1");
         HttpTransport httpTransport = new NetHttpTransport();
         JacksonFactory jsonFactory = new JacksonFactory();
         String[] SCOPESArray = 
            {"https://spreadsheets.google.com/feeds", 
             "https://spreadsheets.google.com/feeds/spreadsheets/private/full", 
             "https://docs.google.com/feeds"};
         final List SCOPES = Arrays.asList(SCOPESArray);
         KeyStore keystore = KeyStore.getInstance("PKCS12");
         keystore.load(
            getResources().openRawResource(R.raw.piandroidprojects), "notasecret".toCharArray());
         PrivateKey key = (PrivateKey) keystore.getKey("privatekey", "notasecret".toCharArray());

         GoogleCredential credential = 
            new GoogleCredential.Builder()
                .setTransport(httpTransport)
                .setJsonFactory(jsonFactory)
                .setServiceAccountPrivateKey(key)
                .setServiceAccountId("14902682557-05eecriag0m9jbo50ohnt59sest5694d@developer.gserviceaccount.com")
                .setServiceAccountScopes(SCOPES)
                .build();

         service.setOAuth2Credentials(credential);
         URL SPREADSHEET_FEED_URL = new URL("https://spreadsheets.google.com/feeds/spreadsheets/private/full");
         SpreadsheetFeed feed = 
            service.getFeed(SPREADSHEET_FEED_URL, SpreadsheetFeed.class);
         List<SpreadsheetEntry> spreadsheets = feed.getEntries();

         return spreadsheets.get(0).getWorksheets();

      } catch (MalformedURLException e) {
         e.printStackTrace();
      } catch (ServiceException e) {
         e.printStackTrace();
      } catch (IOException e) {
         e.printStackTrace();
      } catch (GeneralSecurityException e) {
         e.printStackTrace();
      }
      return null;
   }

   protected void onPostExecute(final List<WorksheetEntry> worksheets) {
      if(worksheets == null || worksheets.size() == 0) {
         Toast.makeText(MapsActivity.this, "Nothing saved yet", Toast.LENGTH_LONG).show();
      } else {
         final List<String> worksheetTitles = 
            new ArrayList<String>();
         for(WorksheetEntry worksheet : worksheets) {
             worksheetTitles.add(
                worksheet.getTitle().getPlainText());
         }

         AlertDialog.Builder alertDialogBuilder = 
            new AlertDialog.Builder(MapsActivity.this);
         alertDialogBuilder.setTitle("Select a worksheet");
         alertDialogBuilder.setAdapter(
            new ArrayAdapter<String>(
                MapsActivity.this,
                android.R.layout.simple_list_item_1, worksheetTitles.toArray(new String[0])),
                new DialogInterface.OnClickListener() {
                   @Override
                   public void onClick(DialogInterface dialog, int which) {
                      new RetrieveWorksheetContent()
                         .execute(worksheets.get(which));
                   }
             });
            alertDialogBuilder.create().show();
}
      }
   }
```

在我们描述上述代码之前，为电子表格服务定义一个实例变量，该变量在我们刚刚定义的任务中使用：

```kt
SpreadsheetService service;
```

Android 的 `AsyncTask` 要求我们重写 `doInBackground` 方法，每当我们调用 `onCreate` 中执行的任务的 `execute` 方法时，它就会在新线程中执行。在 `doInBackground` 中，我们将定义 `KeyStore`，并加载我们从 Google Developer Console 下载并复制到 Android 项目的 `raw` 目录中的 `P12` 文件。请注意，`notasecret` 是开发者控制台在我创建和下载 `P12` 文件时通知我的秘密。此外，在 `setServiceAccountId` 方法内，您需要使用自己的帐户名。您可以在开发者控制台的 **服务帐户** 部分的 **电子邮件地址** 字段以及 JSON 密钥文件的 **client_email** 字段中找到它。在加载密钥文件并定义凭据之后，我们将使用 OAuth 授权自己访问 Google 电子表格服务。我们将简单地获取我假设是 `CAR_OBD_SHEET` 的第一个电子表格，并返回其中的工作表。我们也可以遍历所有电子表格并搜索标题，但我将跳过此部分代码，并假设您的帐户中只有一个标题为 `CAR_OBD_SHEET` 的电子表格。

我们将定义的第二个函数是 `onPostExecute`。每当后台处理时，此函数由 Android 系统在 UI 线程中调用。重要的是，这在 UI 线程中运行，因为如果我们在非 UI 线程中运行与 UI 相关的代码，就无法触摸 UI 元素。

请注意，`doInBackground` 方法的返回值作为参数发送到 `onPostExecute` 方法，这是在 Google Docs 服务中找到的工作表的列表。我们将遍历此列表并将标题收集到另一个列表中。然后，我们将在弹出对话框中显示此列表，用户可以单击并选择。每当用户选择工作表之一时，Android 将调用 `DialogInterface.OnClickListener` 的 `onClick` 方法，我们已将其作为参数发送到 `AlertDialog` 的适配器中。此方法调用另一个名为 `RetrieveWorksheetContent` 的 `AsyncTask` 的 `execute` 方法，正如名称所示，它检索所选工作表的内容。以下是此任务的定义：

```kt
class RetrieveWorksheetContent extends AsyncTask<WorksheetEntry, Void, List<List<Object>>> {

   @Override
   protected List<List<Object>> doInBackground(WorksheetEntry params) {
      WorksheetEntry worksheetEntry = params[0];
      URL listFeedUrl= worksheetEntry.getListFeedUrl();
      List<List<Object>> values = new ArrayList<List<Object>>();
      try {
         ListFeed feed = 
            service.getFeed(listFeedUrl, ListFeed.class);
         for(ListEntry entry : feed.getEntries()) {
             List<Object> rowValues = new ArrayList<Object>();
             for (String tag : entry.getCustomElements().getTags()) {
               Object value = 
                  entry.getCustomElements().getValue(tag);
                rowValues.add(value);
            }
            values.add(rowValues);
         }
      } catch (IOException e) {
         e.printStackTrace();
      } catch (ServiceException e) {
         e.printStackTrace();
      }
      return values;
   }

   @Override
   protected void onPostExecute(List<List<Object>> values) {
      setUpMap(values);
      super.onPostExecute(values);
   }
}
```

在这里，最重要的部分是我们遍历`feed.getEntries()`，它指的是电子表格中的所有行，以及我们遍历`entry.getCustomElements().getTags()`的部分，它指的是所有的列。然后，在`onPostExecute`中，我们将使用我们检索到的所有值调用`setUpMap`方法。在这个方法内部，我们将在**MapsActivity**中包含的地图上创建标记。如果您不希望在位置`0`,`0`处有一个标记，可以注释掉 Android Studio 为您自动定义的`setUpMap`方法作为示例：

```kt
private void setUpMap(List<List<Object>> values) {
   for(List<Object> value : values) {
       String title = values.get(0).toString();
       try {
         double latitude = 
            Double.parseDouble(value.get(1).toString());
         double longitude = 
            Double.parseDouble(value.get(2).toString());
         if (latitude != 0 && longitude != 0)
             mMap.addMarker(
               new MarkerOptions().position(
                   new LatLng(latitude, longitude)))
                .setTitle(title);
      } catch(NumberFormatException ex) {
      }
   }
}
```

当您启动应用程序时，您将看到一个可供选择的电子表格列表：

![检索测量数据](img/image00153.jpeg)

电子表格列表

接下来，在选择这些表格中的一个之后，您可以在地图上看到数据：

![检索测量数据](img/image00154.jpeg)

地图上的数据点

# 总结

在本章中，我们涵盖了很多内容，从汽车诊断到 Android 设备的 root 过程。我们甚至涵盖了大量的 Android 代码。

我希望你们所有人都能乐在其中，实现这些令人兴奋的项目，会尝试改进它们，使它们比我做得更好。
