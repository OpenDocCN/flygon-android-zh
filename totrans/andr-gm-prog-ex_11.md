# 第十一章：碰撞事件——第二部分

这款游戏中的碰撞检测比前两款要复杂得多。因此，代码将会有很多注释。有时注释会详细解释一些内容，或者用稍微不同的方式解释。

然而，这并不意味着它需要艰苦的工作。我们需要做的是花点时间考虑一个适合我们的策略。

希望这种方法意味着在本章结束时，我们的碰撞检测解决方案将显得直接明了。

# 碰撞检测的规划

我们试图实现的目标可以分为以下两类：

+   我们希望边界能做到：

    +   小行星、子弹和船只需要在它们与边界碰撞时知道这一点

    +   小行星在接触到边界时应反转并返回游戏区域

    +   子弹在接触到边界时应重置自己

    +   船只需要减去一条生命，然后在中心重新生成

+   我们希望小行星能做到什么。我们需要知道并在以下情况下做出响应：

    +   船只接触到小行星

    +   当一颗子弹接触到小行星时

    +   与原始的《小行星》游戏一样，我们将不对小行星之间的相互碰撞做出响应

尽管我们不会检测小行星之间的碰撞，但当我们的碰撞检测接近完成时，你会发现实现小行星之间的碰撞检测并不会带来太大的额外挑战。然而，这会对设备的 CPU 造成额外的压力。

我们知道我们需要检测的对象有边界碰撞和小行星碰撞。

## 与边界的碰撞

这可能听起来很明显，但边界仅仅是由四条静态直线组成。这使得边界碰撞与小行星碰撞是不同的问题。

我们感兴趣的所有对象都有顶点（子弹的情况就是一个顶点）。这最初可能意味着我们可以简单地从模型空间和存储在`worldLocation`中的对象中心计算每个顶点的世界位置。我们可以这样做，但这忽略了小行星和船只的旋转，这导致所有顶点的实际世界位置不断变化。

我们需要将模型空间的顶点进行平移和旋转，然后测试它们是否触碰到边界。我们可以在每个帧的对象的`update`方法中这样做，但我们只需要在对象非常接近边界时偶尔获取旋转后的坐标。

### 边界碰撞检测的第一阶段

这表明初步检查，即碰撞检测的第一阶段，效率更高。这意味着顶点的平移和旋转需要发生在对象本身之外。

我们将使用一个基于对象中心和宽高的简单矩形相交检查。如果这个低成本的方法返回一个命中，我们然后将每个顶点进行旋转和平移，并单独检查它们的世界坐标是否与边界位置相撞。

计算出顶点的旋转后游戏世界位置后，碰撞检测就变得简单了。

```java
if (any point falls outside the border){collision has occurred}
```

正如我们将看到的，两阶段解决方案也适用于小行星检测。尽管涉及到旋转和平移，但这要次要得多。

## 与小行星碰撞

与小行星的碰撞测试在某些方面是相似的。我们需要找出船或子弹的任何一个顶点是否进入了由小行星顶点所围成的空间。

第一个问题在于小行星不仅是一个移动目标，而且还在旋转。我们不仅要旋转和平移物体的所有顶点，还要对小行星进行同样的操作。

我们还需要计算小行星上每对顶点之间的线段。幸运的是，在这个阶段，我们可以依赖一个比我更伟大的数学家设计并完善的巧妙算法。我们将使用交叉数算法。这是它的工作原理。

### 交叉数

我们计算一对顶点形成的线段，并使用交叉数算法查看被测试物体的某个特定顶点是否穿过了该线段。如果穿过了，我们将一个变量从 0 增加到 1。

我们用交叉数算法测试同一个点与由小行星的每对顶点形成的每一条线，每次它穿过就增加我们的变量。如果在对顶点与每条线进行测试后，我们的变量是奇数，那么就表示有碰撞发生。如果是偶数，则没有发生碰撞。

当然，如果没有发生碰撞，我们必须继续测试被测试物体的每个顶点与由小行星上的顶点对形成的每条线。

这是一张交叉数算法工作过程的视觉表示图。

![交叉数](img/B043422_11_01.jpg)

当然，在进行所有这些复杂的计算时，我们肯定想要先做一个简单的第一阶段测试，以查看是否可能发生了碰撞，然后再进行复杂的测试。

### 小行星碰撞检测的第一阶段和概述

当测试单个顶点，如子弹、像船一样的旋转三角形或旋转小行星时，半径重叠测试非常合适。

这是我们将用于测试与 小行星碰撞的整个过程的概述：

1.  被测试物体的半径是否与小行星的半径重叠？

1.  如果是，物体的第一个顶点是否穿过了小行星的第一条线？

1.  如果是，`crossingNumber ++`。

1.  对每个物体的每行重复步骤 2。

1.  如果`crossingNumber`是奇数，返回给调用代码 true，因为已经发生了碰撞。

1.  如果`crossingNumber`是偶数，则尚未发生碰撞，用被测试物体的下一个顶点重复步骤 2、3 和 4。

1.  如果所有顶点都已测试并且我们到达这里，则没有发生碰撞。

我们将设置一个名为`CD`的碰撞检测类，其中包含两个静态方法。`detect`方法将测试与小行星的碰撞，并且每一帧对每个子弹和飞船调用，针对每一个小行星。

`contain`方法将检查每个小行星、子弹和飞船与边界的碰撞情况。

在对象外部进行计算意味着我们将需要大量我们正在测试的对象的数据，以及那些可供新的`CD`类方法访问的数据。

## `CollisionPackage`类

我们知道，为了正确执行检测，我们需要一组特定的数据。接下来的这个类将保存碰撞检测类方法执行任务所需的所有数据，而每个需要检测碰撞的对象都将拥有这样一个类。

当需要将所有点旋转到它们在现实世界中的位置时，我们的碰撞包需要知道物体面向哪个方向。我们有一个名为`facingAngle`的浮点数。

显然，我们需要模型空间顶点的副本。与旋转位置一样，我们不会在每一帧都麻烦地更新，而是在碰撞检测的第一阶段显示可能发生碰撞后这样做。

我们还将保存一个预计算的值，即保存这些顶点的数组的长度。它可以在碰撞检测过程中潜在地节省时间。

因此，我们还需要物体的世界坐标。这个坐标我们将每一帧更新。

每个对象将有一个预计算的`radius`变量，这是从对象中心到最远顶点的距离，即对象的大小。这将在我们的`detect`方法中用于第一阶段检测的半径重叠。

我们还将有两个`PointF`对象，`currentPoint`和`currentPoint2`，它们只是方便的对象，可以避免在我们两个碰撞检测方法中的密集部分可能调用垃圾收集器。

创建一个名为`CollisionPackage`的新类，并实现我们刚刚讨论过的成员：

```java
// All objects which can collide have a collision package.
// Asteroids, ship, bullets. The structure seems like slight
// overkill for bullets but it keeps the code generic,
// and the use of vertexListLength means there isn't any
// actual speed overhead. Also if we wanted line, triangle or
// even spinning bullets the code wouldn't need to change.

public class CollisionPackage {

    // All the members are public to avoid multiple calls
    // to getters and setters.

    // The facing angle allows us to calculate the
    // current world coordinates of each vertex using
    // the model-space coordinates in vertexList.
    public float facingAngle;

    // The model-space coordinates
    public PointF[] vertexList;

    /* 
    The number of vertices in vertexList
    is kept in this next int because it is pre-calculated
    and we can use it in our loops instead of
    continually calling vertexList.length.
   */
    public int vertexListLength;

    // Where is the centre of the object?
    public PointF worldLocation;

    /* 
    This next float will be used to detect if the circle shaped
    hitboxes collide. It represents the furthest point
    from the centre of any given object.
    Each object will set this slightly differently.
    The ship will use height/2 an asteroid will use 25
    To allow for a max length rotated coordinate.
   */
    public float radius;

    // A couple of points to store results and avoid creating new
    // objects during intensive collision detection
    public PointF currentPoint = new PointF();
    public PointF currentPoint2 = new PointF();
```

接下来，我们有一个简单的构造函数，它将在每个对象的构造函数末尾接收来自每个对象的所有必要数据。按照如下所示实现`CollisionPackage`构造函数：

```java
public CollisionPackage(PointF[] vertexList, PointF worldLocation, 
  float radius, float facingAngle){ 

        vertexListLength = vertexList.length;
        this.vertexList = new PointF[vertexListLength];
        // Make a copy of the array

        for (int i = 0; i < vertexListLength; i++) {
            this.vertexList[i] = new PointF();
            this.vertexList[i].x = vertexList[i].x;
            this.vertexList[i].y = vertexList[i].y;
        }

        this.worldLocation = new PointF();
        this.worldLocation = worldLocation;

        this.radius = radius;

        this.facingAngle = facingAngle;

    }

}
```

这就是我们进行高级碰撞检测所需的所有数据。

### 向对象添加碰撞包并使它们可访问

现在，我们有了`CollisionPackage`类。我们将看到如何向每个需要监控的对象添加一个。

#### 向`Bullet`类添加一个碰撞包

打开`Bullet`类，我们将看到如何在我们最简单的情况（只是一个点）上使用`CollisionPackage`构造函数。为碰撞包添加一个新成员。

在`Bullet`类中添加一个类型为`CollisionPackage`的新成员：

```java
CollisionPackage cp;
```

现在，我们创建一个结构，将其传递给我们的`CollisionPackage`构造函数，并初始化碰撞包。注意，我们传递一个只包含模型空间坐标 0,0,0 的单元素数组。然后，我们传递子弹面向的世界位置、半径 1 和角度。在`Bullet`类的构造函数的最后输入以下代码：

```java
// Initialize the collision package
// (the object space vertex list, x any world location
// the largest possible radius, facingAngle)

// First, build a one element array
PointF point = new PointF(0,0);
PointF[] points = new PointF[1];
points[0] = point;

// 1.0f is an approximate representation 
//of the size of a bullet
cp = new CollisionPackage(points, getWorldLocation(),
1.0f, getFacingAngle());
```

最后，对于`Bullet`类，我们通过在`Bullet`类的`update`方法的最后添加以下代码，在每一帧更新碰撞包：

```java
        move(fps);

 // Update the collision package
 cp.facingAngle = getFacingAngle();
 cp.worldLocation = getWorldLocation();

```

现在，我们的子弹都已准备好进行检测。

#### 向 SpaceShip 类添加碰撞包

打开`SpaceShip`类并添加这些成员。然后我们将在`SpaceShip`构造函数中看到如何使用它们：

```java
CollisionPackage cp;

// Next, a 2d representation using PointF of
// the vertices. Used to build shipVertices
// and to pass to the CollisionPackage constructor
PointF[] points;
```

在这里，与`Bullet`类相比，我们做了一些额外的工作。我们增加了三个额外的模型空间坐标。OpenGL 不需要知道这些，也不需要它们。它们位于构成飞船的每条线的中间。我们这样做是为了使小行星的顶点更难在没有飞船顶点位于小行星内部的情况下漂入飞船内部。这是我们正在解决的问题的视觉表示。飞船的顶点被重点强调，以突出这个问题。参考以下图表：

![向 SpaceShip 类添加碰撞包](img/B043422_11_02.jpg)

我们可以通过测试所有小行星的顶点与所有飞船的线，以及我们计划要做的事情；测试所有飞船的顶点与所有小行星的线，完全解决这个问题。然而，仅向飞船添加几个额外的点确实可以产生近乎完美的检测，如下所示：

![向 SpaceShip 类添加碰撞包](img/B043422_11_03.jpg)

现在，在`SpaceShip`构造函数中`setVertices()`调用之后，立即实现我们刚才讨论的代码：

```java
setVertices(shipVertices);

// Initialize the collision package
// (the object space vertex list, x any world location
// the largest possible radius, facingAngle)

points = new PointF[6];
points[0] = new PointF(- halfW, - halfL);

points[2] = new PointF(halfW, - halfL);
points[4] = new PointF(0, 0 + halfL);

// To make collision detection more accurate we will define some
// more points on the midpoints of all our sides.
// It is possible that the point of an asteroid will pass through
// the side of the ship and we do not test for this!
// We only test for the point of a ship 
// passing through the side of an asteroid!!
// This is computationally cheaper than running both tests.
// Although not as accurate we will see it is very close.
// We can think of this visually as 
// adding extra sensors on the sides of our ship
// Here we use an equation to find the midpoint 
// of a line which you can find an explanation of
// on most good high school math web sites.

points[1] = new PointF(points[0].x + 
 points[2].x/2,(points[0].y + points[2].y)/2);

points[3] = new PointF((points[2].x + points[4].x)/2,
 (points[2].y + points[4].y)/2);

points[5] = new PointF((points[4].x + points[0].x)/2,
 (points[4].y + points[0].y)/2);

cp = new CollisionPackage(points, getWorldLocation(), 
 length/2, getFacingAngle());

}// End SpaceShip constructor
```

接下来，像对`Bullet`类所做的那样，我们在`SpaceShip`类的`update`方法中每帧同步碰撞包。在`move()`调用更新飞船坐标后，我们会在方法的最后这样做。

```java
move(fps);

 // Update the collision package
 cp.facingAngle = getFacingAngle();
 cp.worldLocation = getWorldLocation();

}// End SpaceShip update()
```

最后，我们将在小行星上添加一个碰撞包。

#### 向 Asteroid 类添加碰撞包

打开`Asteroid`类并添加一个`CollisionPackage`成员：

```java
CollisionPackage cp;
```

在`Asteroid`构造函数的最后，紧接在`generatePoints()`调用之后，我们初始化了`CollisionPackage`对象：

```java
// Define a random asteroid shape
// Then call the parent setVertices()
generatePoints();

// Initialize the collision package
// (the object space vertex list, x any world location
// the largest possible radius, facingAngle)
cp = new CollisionPackage
 (points, getWorldLocation(), 25, getFacingAngle());

```

接下来，我们添加一个辅助方法，当检测到碰撞时，这个方法会反转旅行方向，并将小行星通过几个像素*弹回*。当检测到与边界的碰撞时，我们将调用这个方法。将`bounce`方法添加到`Asteroid`类中：

```java
public void bounce(){

  // Reverse the travelling angle
    if(getTravellingAngle() >= 180){
      setTravellingAngle(getTravellingAngle()-180);
     }else{
      setTravellingAngle(getTravellingAngle() + 180);
    }

    // Reverse velocity because occasionally they get stuck
    setWorldLocation((getWorldLocation().x + -getxVelocity()/3), (getWorldLocation().y + -getyVelocity()/3));

    // Speed up by 10%
    setSpeed(getSpeed() * 1.1f);

    // Not too fast though
    if(getSpeed() > getMaxSpeed()){
      setSpeed(getMaxSpeed());

}
```

与`SpaceShip`和`Bullet`类一样，我们将在`update`方法中，紧接在`move`调用之后，在`update`方法的最后更新碰撞包：

```java
move(fps);

// Update the collision package
cp.facingAngle = getFacingAngle();
cp.worldLocation = getWorldLocation();

}
```

现在，我们需要做一件在其他类中不需要做的事情。我们的交叉数算法使用线而不是顶点，所以我们需要通过将最后一个顶点与第一个顶点连接起来来形成一条线。由于我们的碰撞数据代码的工作方式，我们不需要对`SpaceShip`类这样做。碰撞数据代码将测试子弹和飞船的顶点与小行星的线。而不是反过来的方式。

这是需要添加到`generatePoints`方法的第七点处的额外代码。在以下代码中，我在新突出显示的代码两侧包含了现有的代码：

```java
// left below 0
points[5] = new PointF();
i = -(r.nextInt(14)+11);
points[5].x =  i;
i = -(r.nextInt(12)+1);

points[5].y = i;

// We add on an extra point that we won't use in asteroidVertices[].
// The point is the same as the first. 
// This is because the last vertex
// links back to the first to create a line. 
// This line will need to be
// used in calculations when we do our collision detection.

// Here is the extra vertex- same as the first.
points[6] = new PointF();
points[6].x = points[0].x;
points[6].x = points[0].x;

// Now use these points to draw our asteroid
float[] asteroidVertices = new float[]{
// First point to second point
points[0].x, points[0].y, 0,
points[1].x, points[1].y, 0,
```

现在，我们可以谈谈构建碰撞检测类本身。

## CD 类大纲

我们现在将实现碰撞检测的第一阶段。如所讨论的，我们将使用的算法计算成本很高，只有当有实际碰撞的可能性时，我们才希望使用它们。

因此，我们将使用在第三章中讨论的半径重叠方法，检查每个子弹和飞船与每个小行星之间的碰撞。我们将使用简化的矩形相交方法检查小行星、飞船和子弹与边界之间的碰撞。

在接下来的两个部分之后，你实际上可以玩游戏，但你会发现我们到目前为止使用的这种基本碰撞检测对于这类游戏来说还不够令人满意。

这些初步检查将决定我们是否继续进行更准确且计算成本更高的检查。

我们将在*精确边界碰撞检测*和*精确小行星碰撞检测*部分实现这些第二阶段检查，它们将使用更高级的算法，并充分利用我们的碰撞数据包。

首先，创建一个名为`CD`的新类。添加一个`PointF`成员对象并初始化它。我们将在代码的关键部分使用它，以避免创建新对象。

```java
private static PointF rotatedPoint = new PointF();
```

现在，让我们讨论一下这些方法。

### 为小行星和飞船实现半径重叠

让我们在`CD`类中添加我们的第一个方法，用于检测子弹与行星以及飞船与行星之间的碰撞。正如我们讨论的，现在我们只实现这个方法的第一部分。以下是半径重叠代码的实现。

代码通过构建一个缺少一边的假设三角形，然后使用勾股定理计算两个对象中心点之间的缺失边，也就是两个物体之间的距离。如果两个物体的半径之和大于两个物体中心之间的距离，那么就存在重叠。

添加带有半径重叠代码的`detect`方法。注意，如果半径重叠，我们返回`true`。这行代码将在本章后面被更准确的检测所替换。

```java
public static boolean detect(CollisionPackage cp1, 
    CollisionPackage cp2) {

    boolean collided = false;

   // Check circle collision between the two objects

   // Get the distance of the two objects from
   // the centre of the circles on the x axis
   float distanceX = (cp1.worldLocation.x)
        - (cp2.worldLocation.x);

   // Get the distance of the two objects from
   // the centre of the circles on the y axis
   float distanceY = (cp1.worldLocation.y)
        - (cp2.worldLocation.y);

        // Calculate the distance between the center of each circle
        double distance = Math.sqrt
            (distanceX * distanceX + distanceY * distanceY);

        // Finally see if the two circles overlap
        // If they do it is worth doing the more intensive
        // and accurate check.
        if (distance < cp1.radius + cp2.radius) {

         // Log.e("Circle collision:","true");
         // todo  Eventually we will add the 
         // more accurate code here
         // todo and delete the line below.

            collided = true;
        }

        return collided;
    }
```

现在，让我们讨论一下边界。

### 实现边界矩形相交

我们将检查是否有任何小行星、子弹或飞船需要被限制在边界内。如讨论所述，我们将执行一个简单的矩形相交测试，如果检测到则返回`true`。稍后，我们将删除返回`true`并添加更复杂的代码。

按照如下所示实现`contain`方法：

```java
// Check if anything hits the border
public static boolean contain(float mapWidth, float mapHeight,                                              
  CollisionPackage cp) {

   boolean possibleCollision = false;

    // Check if any corner of a virtual rectangle
    // around the centre of the object is out of bounds.
    // Rectangle is best because we are testing 
    // against straight sides (the border)
    // If it is we have a possible collision.

    if (cp.worldLocation.x - cp.radius < 0) {
            possibleCollision = true;
        } else if (cp.worldLocation.x + cp.radius > mapWidth) {
            possibleCollision = true;
        } else if (cp.worldLocation.y - cp.radius < 0) {
            possibleCollision = true;
        } else if (cp.worldLocation.y + cp.radius > mapHeight) {
            possibleCollision = true;
        }

        if (possibleCollision) {
            // todo For now we return true
            return true;
        }

        return false; // No collision
}
```

现在，我们有两个方法，只需对所有合适对象组合调用它们即可。

# 执行检查

我们已经非常接近能够玩我们的游戏了，尽管碰撞检测被简化了。首先添加一些处理特定碰撞被检测到时会发生什么的方法，然后看看我们是如何实际使用我们的`CD`类的。

## 辅助方法

首先，我们需要一些辅助方法，以便在检测到各种类型的碰撞时做出响应。

我们需要一个在飞船被摧毁时调用的方法，以及一个在摧毁小行星时调用的方法。接下来的两个小节将介绍这些内容。

### 摧毁飞船

飞船的“死亡”可以在两个地方检测到，因此添加一个处理随后事件的方法是合理的。在下一个方法中，我们将飞船的位置重置为地图中心，播放声音，并减少`numLives`的值。

如果`numLives`等于零，将`levelNumber`重置为 1，`numLives`重置为 3，调用`createObjects()`重新绘制一个级别，暂停游戏，然后播放一个声音，让玩家知道他要重新开始。

现在，向`AsteroidsRenderer`类中添加`lifeLost`方法：

```java
public void lifeLost(){
        // Reset the ship to the center
        gm.ship.setWorldLocation(gm.mapWidth/2, gm.mapHeight/2);
        // Play a sound
        sm.playSound("shipexplode");

        // Deduct a life
        gm.numLives = gm.numLives -1;

        if(gm.numLives == 0){
            gm.levelNumber = 1;
            gm.numLives = 3;
            createObjects();
            gm.switchPlayingStatus();
            sm.playSound("gameover");
        }
    }
```

我们将处理小行星“死亡”时会发生什么。

### 摧毁小行星

当飞船或子弹击中一个小行星时，将调用此方法。首先，我们将触发碰撞的小行星设置为`setActive(false)`，它将不再被绘制或更新。

接下来，我们播放声音并减少`numAsteroidsRemaining`的值。如果`numAsteroidsRemaining`等于零，意味着玩家已经清除了整个关卡。在这种情况下，我们增加`levelNumber`和`numLives`，播放胜利的声音，并通过调用`createObjects()`开始一个更难的级别。

现在，向`AsteroidsRenderer`类中添加`destroyAsteroid()`方法：

```java
public void destroyAsteroid(int asteroidIndex){

  gm.asteroids[asteroidIndex].setActive(false);
     // Play a sound
     sm.playSound("explode");
     // Reduce the number of active asteroids
     gm.numAsteroidsRemaining --;

     // Has the player cleared them all?
     if(gm.numAsteroidsRemaining == 0){
     // Play a victory sound

     // Increment the level number
     gm.levelNumber ++;

     // Extra life
     gm.numLives ++;

     sm.playSound("nextlevel");
     // Respawn everything
     // With more asteroids
     createObjects();

}
}
}// End class
```

现在，我们可以调用我们新的`CD`类的静态方法，并在检测到碰撞时做出响应。

## 在`update()`中测试碰撞

首先，我们将检查是否需要限制飞船。我们只需使用`mapWidth`、`mapHeight`和飞船的碰撞包调用`CD.contain()`。如果发生碰撞，代码将调用`lifeLost()`。

在`update`方法中更新所有对象后，添加碰撞检测代码： 

```java
// End of all updates!!

// All objects are in their new locations
// Start collision detection

// Check if the ship needs containing
if (CD.contain(gm.mapWidth, gm.mapHeight, gm.ship.cp)) {

  lifeLost();

}
```

这段代码用于检测是否有小行星试图离开小行星模拟器。除了我们遍历每个小行星，检查它是否处于活动状态，并在检测到碰撞时对小行星调用`bounce`方法外，它的工作原理与之前的代码块完全相同。

```java
// Check if an asteroid needs containing
for (int i = 0; i < gm.numAsteroids; i++) {
  if (gm.asteroids[i].isActive()) {
       if (CD.contain(gm.mapWidth, gm.mapHeight, 
       gm.asteroids[i].cp)) {

          // Bounce the asteroid back into the game
          gm.asteroids[i].bounce();

          // Play a sound
          sm.playSound("blip");

       }
    }

}
```

子弹的代码看起来有点复杂，但其实不是。对`CD.contain()`的调用是相同的，我们对每颗子弹都这样做。但是，为了使子弹在离开视口（如果这发生在边界之前）时重置，需要进行一些最后的游戏平衡，否则飞船可以简单地旋转并从很远的距离摧毁小行星。

输入代码以检测子弹与边界和视口边缘的碰撞：

```java
// Check if bullet needs containing
// But first see if the bullet is out of sight
// If it is reset it to make game harder
for (int i = 0; i < gm.numBullets; i++) {

    // Is the bullet in flight?
    if (gm.bullets[i].isInFlight()) {

   // Comment the next block to make the game easier!!!
   // It will allow the bullets to go all the way from
   // ship to border without being reset. 
   // These lines reset the bullet when
   // shortly after they leave the players view.
   // This forces the player to go 'hunting' for the
   // asteroids instead of spinning round spamming the
   // fire button...
   // This code would be better with a viewport.clip() method
   // like in project 2 but seems a bit excessive just for these
   // few 15ish lines of code.

   // Start comment out to make easier
   handyPointF = gm.bullets[i].getWorldLocation();
   handyPointF2 = gm.ship.getWorldLocation();

   if(handyPointF.x > handyPointF2.x + gm.metresToShowX / 2){
        // Reset the bullet
        gm.bullets[i].resetBullet(gm.ship.getWorldLocation());

    }else
        if(handyPointF.x < handyPointF2.x - gm.metresToShowX / 2){
            // Reset the bullet
            gm.bullets[i].resetBullet(gm.ship.getWorldLocation());

        }else
        if(handyPointF.y > handyPointF2.y + gm.metresToShowY/ 2){
            // Reset the bullet
            gm.bullets[i].resetBullet(gm.ship.getWorldLocation());
       }else
        if(handyPointF.y < handyPointF2.y - gm.metresToShowY / 2){
            // Reset the bullet
            gm.bullets[i].resetBullet(gm.ship.getWorldLocation());
                }
            // End comment out to make easier

            // Does bullet need containing?
            if (CD.contain(gm.mapWidth, gm.mapHeight,      
                gm.bullets[i].cp)) {

                 // Reset the bullet
                 gm.bullets[i].resetBullet
                    (gm.ship.getWorldLocation());
                 // Play a sound
                 sm.playSound("ricochet");
          }

     }

}
```

现在你可以运行游戏，看看`CD.contain()`方法是如何很好地保持所有物体在模拟小行星内的。

我们将调用`detect`方法，看看是否有任何东西撞上了小行星。

首先，检查子弹。注意我们进行初步检查，以确保子弹在飞行中，小行星处于活动状态，然后才会麻烦我们的`CD.detect`方法。然后，我们只需传入两个碰撞包，`CD.detect`完成其余工作。如果子弹与边界碰撞，我们会在相应的子弹上调用`resetBullet()`。

```java
// Now we see if anything has hit an asteroid

// Check collisions between asteroids and bullets
// Loop through each bullet and asteroid in turn

for (int bulletNum = 0; bulletNum < gm.numBullets; bulletNum++) {
    for (int asteroidNum = 0; asteroidNum < gm.numAsteroids;                            
        asteroidNum++) {

        // Check that the current bullet is in flight
        // and the current asteroid is 
        // active before proceeding
        if (gm.bullets[bulletNum].isInFlight() &&                                           
            gm.asteroids[asteroidNum].isActive())

            // Perform the collision checks by 
            // passing in the collision packages

            // A Bullet only has one vertex. 
            // Our collision detection works on vertex pairs

          if (CD.detect(gm.bullets[bulletNum].cp,                                           
              gm.asteroids[asteroidNum].cp)) {

                // If we get a hit...
                destroyAsteroid(asteroidNum);

                // Reset the bullet
                gm.bullets[bulletNum].resetBullet
                    (gm.ship.getWorldLocation());
           }

    }
}
```

现在，我们测试飞船。如果检测到碰撞，我们依次调用`destroyAsteroid()`和`lifeLost()`。

```java
// Check collisions between asteroids and ship
// Loop through each asteroid in turn

for (int asteroidNum = 0; asteroidNum < gm.numAsteroids;                            
     asteroidNum++) {

    // Is the current asteroid active before proceeding
    if (gm.asteroids[asteroidNum].isActive()) {

        // Perform the collision checks by
        // passing in the collision packages
        if (CD.detect(gm.ship.cp, gm.asteroids[asteroidNum].cp)) {

        // hit!
        destroyAsteroid(asteroidNum);
        lifeLost();
       }
    }
}
```

在这一点上，你可以玩游戏，我们的基本碰撞检测将会起作用。但是，如果你飞得太接近小行星，你会在没有接触的情况下失去一条生命，或者只是在小行星附近发射一颗子弹，小行星就会消失。我们需要能够掠过边界或小行星的表面，并且只有在当一个点实际进入另一个物体的确切空间时才会发生碰撞。

# 精确的边界碰撞检测

为了升级我们的`detect`方法，我们需要用更精确的检测代码替换`if(possibleCollision)`块中的返回语句。

首先，初始化`radianAngle`为我们的物体面向的任意方向（以度为单位）的弧度等价。`Math`类使用弧度，因为它们在计算中比更容易视觉化的度数更有数学上的用途。

变量`cosAngle`和`sinAngle`正如其名所示，并在接下来的代码块中使用。

### 提示

值得一提的是，`Math.cos()`和`Math.sin()`方法相对耗时。我们可以通过预先计算`sin`和`cos`的 360 个值来加速碰撞检测类，然后使用简单的查找方法代替这个计算。

然而，我们要保持每秒 60 帧以上的目标，所以这里不要这样做。

删除返回语句，并在`if(possibleCollision)`块中添加以下代码：

```java
if (possibleCollision) {

 double radianAngle = ((cp.facingAngle/180)*Math.PI);
 double cosAngle = Math.cos(radianAngle);
 double sinAngle = Math.sin(radianAngle);

```

在下一块代码中，输入一个`for`循环，遍历每个对象的顶点，将它们从模型空间转换到世界空间坐标，然后使用之前计算好的`facingAngle`对象的余弦和正弦值来旋转它们到游戏世界中的精确位置。

```java
    //Rotate each and every vertex then check for a collision
    // If just one is then we have a collision.
    // Once we have a collision no need to check further
    for (int i = 0 ; i < cp.vertexListLength; i++){
        // First update the regular un-rotated model space coordinates
        // relative to the current world location (centre of object)
        float worldUnrotatedX = 
                cp.worldLocation.x + cp.vertexList[i].x;

        float worldUnrotatedY =  
                cp.worldLocation.y + cp.vertexList[i].y;

        // Now rotate the newly updated point, stored in currentPoint
        // around the centre point of the object (worldLocation)
        cp.currentPoint.x = cp.worldLocation.x + (int)                                   
            ((worldUnrotatedX - cp.worldLocation.x)
            * cosAngle - (worldUnrotatedY - cp.worldLocation.y)
            * sinAngle);

        cp.currentPoint.y = cp.worldLocation.y + (int)                                   
            ((worldUnrotatedX - cp.worldLocation.x)
            * sinAngle+(worldUnrotatedY - cp.worldLocation.y)
            * cosAngle);
```

现在我们要做的就是检查旋转和平移后的顶点是否在边界/地图的左侧、右侧、顶部或底部之外。如果是，我们返回`true`；如果不是，循环将继续以相同的方式检查每个顶点（平移、旋转、检查等）。

```java
     // Check the rotated vertex for a collision
     if (cp.currentPoint.x < 0) {

       return true;
     } else if (cp.currentPoint.x > mapWidth) {

       return true;
     } else if (cp.currentPoint.y < 0) {

       return true;
     } else if (cp.currentPoint.y > mapHeight) {

       return true;
   }

}
```

你现在可以运行游戏，观看子弹带着令人满意的撞击声消失在边界内，或者驾驶你的飞船危险地接近边界。

让我们优化小行星碰撞的处理。

# 精确检测小行星的碰撞

我们之所以最后做这个，是因为有一个更复杂的最后步骤。与边界检测类似，我们需要转换和旋转物体的顶点。但这次，我们需要对两个物体都这样做。

此外，一旦我们旋转和平移了小行星的顶点，我们需要成对处理形成线的顶点。这些线是我们将要测试与其他物体每个顶点相交的线。这个测试当然是我们之前讨论过的交叉数方法。

我们需要在`if (distance < cp1.radius + cp2.radius) { ...}`的代码块内完成所有这些操作，之前我们只是将`collided`布尔值设置为`true`。

代码量相当大，因此我们会将其分成几部分，并逐步了解每个阶段发生的情况。此外，为了尽可能使格式易于阅读，代码缩进在各个代码块之间可能不会始终保持一致。

接下来的几段代码就是前述`if`代码块的全部内容，需要替换。

### 提示

如前所述，这里我们也可以使用正弦和余弦的查找表。

我们可以创建一个方法来旋转角度，因为我们经常这样做。但这并不像看起来那么简单。如果我们把旋转代码放在一个方法中，我们要么不得不把下面的正弦和余弦计算也放进去，这将使它变慢；要么在方法调用和`for`循环之前预先计算，这本身也是一种不太整洁的做法。

另外，考虑到我们需要一个角度的正弦和余弦的多个值，该方法需要*知道*使用哪个值，这并不是火箭科学，但它的复杂度可能比我们最初想象的还要高。因此，我选择完全避免方法调用，即使代码看起来有些冗长。实际上，如果你把所有代码放在一个方法调用中，在旧款 Galaxy S2 手机上仍然可以得到接近 60 FPS 的帧率。所以如果你想要整理代码，请随意；我只是认为这种方式值得一谈。

在我们像边界检测一样进入`for`循环之前，我们会计算一些在此方法执行期间不会改变的东西。即两个碰撞包的面向角度的正弦和余弦。

```java
     if (distance < cp1.radius + cp2.radius) {

            double radianAngle1 = ((cp1.facingAngle / 180) * Math.PI);
            double cosAngle1 = Math.cos(radianAngle1);
            double sinAngle1 = Math.sin(radianAngle1);

            double radianAngle2 = ((cp2.facingAngle / 180) * Math.PI);
            double cosAngle2 = Math.cos(radianAngle2);
            double sinAngle2 = Math.sin(radianAngle2);

            int numCrosses = 0;    // The number of times we cross a side

            float worldUnrotatedX;
            float worldUnrotatedY;
```

现在，我们从`cp2`遍历所有顶点，然后依次与`cp1`中的所有边（顶点对）进行测试。记住，小行星有一个额外的顶点填充，与第一个顶点相同。因此，我们可以测试小行星的最后一边。调用`CD.detect()`时，我们一定要将小行星碰撞包作为*第二个*参数传入。

在下一代码块中，将测试对象翻译并相对于小行星进行旋转。

```java
for (int i = 0; i < cp1.vertexListLength; i++) {

    worldUnrotatedX = cp1.worldLocation.x + cp1.vertexList[i].x;
    worldUnrotatedY = cp1.worldLocation.y + cp1.vertexList[i].y;

    // Now rotate the newly updated point, stored in currentPoint
    // around the centre point of the object (worldLocation)
    cp1.currentPoint.x = cp1.worldLocation.x +
        (int) ((worldUnrotatedX - cp1.worldLocation.x)
        * cosAngle1 - (worldUnrotatedY - cp1.worldLocation.y) *
        sinAngle1);

    cp1.currentPoint.y = cp1.worldLocation.y + 
        (int) ((worldUnrotatedX - cp1.worldLocation.x)
        * sinAngle1 + (worldUnrotatedY - cp1.worldLocation.y) *                   
         cosAngle1);

    // cp1.currentPoint now hold the x/y 
    // world coordinates of the first point to test
```

现在，每次使用小行星的一对顶点，将它们翻译并旋转到它们最终的世界坐标空间，为下一代码块做准备，在那里我们将使用上一块和这一块计算出的顶点位置。

```java
// Use two vertices at a time to represent the line we are testing
// We don't test the last vertex because we are testing pairs
// and the last vertex of cp2 is the padded extra vertex.
// It will form part of the last side when we test vertexList[5]

for (int j = 0; j < cp2.vertexListLength - 1; j++) {

    // Now we get the rotated coordinates of 
    // BOTH the current 2 points being
    // used to form a side from cp2 (the asteroid)
    // First we need to rotate the model-space 
    // coordinate we are testing
    // to its current world position
    // First update the regular un-rotated model space coordinates
    // relative to the current world location (centre of object)

    worldUnrotatedX = cp2.worldLocation.x + cp2.vertexList[j].x;
    worldUnrotatedY = cp2.worldLocation.y + cp2.vertexList[j].y;

    // Now rotate the newly updated point, stored in worldUnrotatedX/y
    // around the centre point of the object (worldLocation)

    cp2.currentPoint.x = cp2.worldLocation.x + 
          (int) ((worldUnrotatedX - cp2.worldLocation.x)
          * cosAngle2 - (worldUnrotatedY - cp2.worldLocation.y) *                   
          sinAngle2);

    cp2.currentPoint.y = cp2.worldLocation.y + 
          (int) ((worldUnrotatedX - cp2.worldLocation.x)
          * sinAngle2 + (worldUnrotatedY - cp2.worldLocation.y) *                   
          cosAngle2);

    // cp2.currentPoint now hold the x/y world coordinates
    // of the first point that
    // will represent a line from the asteroid

    // Now we can do exactly the same for the 
    // second vertex and store it in
    // currentPoint2\. We will then have a point and a line (two 
    // vertices)we can use the
    // crossing number algorithm on.

    worldUnrotatedX = cp2.worldLocation.x + cp2.vertexList[i + 1].x;
    worldUnrotatedY = cp2.worldLocation.y + cp2.vertexList[i + 1].y;

    // Now rotate the newly updated point, stored in worldUnrotatedX/Y
    // around the centre point of the object (worldLocation)
    cp2.currentPoint2.x = cp2.worldLocation.x + 
          (int) ((worldUnrotatedX - cp2.worldLocation.x)
          * cosAngle2 - (worldUnrotatedY - cp2.worldLocation.y) *                   
          sinAngle2);

    cp2.currentPoint2.y = cp2.worldLocation.y + 
          (int) ((worldUnrotatedX - cp2.worldLocation.x)
          * sinAngle2 + (worldUnrotatedY - cp2.worldLocation.y) *                   
           cosAngle2);
```

在这里，我们检测当前的顶点（无论是飞船还是子弹）是否穿过由小行星当前顶点对形成的线。如果穿过了，我们就增加`numCrosses`的计数。

```java
// And now we can test the rotated point from cp1 against the
// rotated points which form a side from cp2

if (((cp2.currentPoint.y > cp1.currentPoint.y) !=                               
       (cp2.currentPoint2.y > cp1.currentPoint.y)) &&
       (cp1.currentPoint.x < (cp2.currentPoint2.x -                                
     cp2.currentPoint2.x)    *(cp1.currentPoint.y - 
        cp2.currentPoint.y) / (cp2.currentPoint2.y  -                               
  cp2.currentPoint.y) + cp2.currentPoint.x)){

        numCrosses++;

}
```

最后，我们使用模运算符来确定`numCrosses`是奇数还是偶数。如讨论所述，对于奇数我们返回`true`（碰撞），对于偶数返回`false`（无碰撞）。

```java
            }
            }
            // So do we have a collision?
            if (numCrosses % 2 == 0) {
                // even number of crosses(outside asteroid)
                collided = false;
            } else {
                // odd number of crosses(inside asteroid)
                collided = true;
            }

        }// end if
```

现在你可以驾驶你的飞船直接飞向小行星，只有在真正看起来应该撞上的时候才会被击中。参考以下截图：

![与行星精确碰撞检测](img/B043422_11_04.jpg)

现在，我们的碰撞检测和小行星模拟器游戏都完成了！

# 完成收尾工作

我们可以继续改进我们的游戏。例如，当当前的小行星被摧毁时，生成两个或三个更小的行星并不困难。我们只需要一个数组来保存小行星。当我们停用常规小行星时，该数组会在与常规小行星相同的位置激活一些先前实例化的小型小行星。然后我们可以对计算小行星数量的方式进行一些小修改，这样我们就会有一个整洁的新功能。

街机经典游戏 Asteroids 中，会偶尔出现一个神秘的 UFO。设计一个由线条构成的 UFO 形状很简单，让它随机从左到右或从右到左移动，同时上下也有所移动。

最后，我们可以添加一个超空间按钮。这是玩家在确定即将死亡时的最后手段。轻触超空间按钮，飞船将在随机位置重新生成。我们只需要在`InputController`类中的数组里添加一个按钮，并在`Ship`类中调用一个新的简单方法`randomHyperspaceJump`。

我们还可以添加谷歌游戏成就和排行榜，然后发布游戏。如果你发布一个使用 OpenGL 的游戏，你需要在`AndroidManifest.xml`文件中添加这个声明：

```java
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
```

尝试添加我们讨论过的一些改进，也许还有你自己的改进。无论你是否发布你的游戏，我都想听听你的想法，或者看到你在[gamecodeschool.com](http://gamecodeschool.com)上的项目链接。

我想我们已经完成了！

# 总结

我希望您享受了我们快速浏览的为 Android 制作游戏的旅程，并希望您继续制作更多的新游戏！
