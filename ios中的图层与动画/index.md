# iOS中的图层与动画


# iOS中的图层与动画

​		前段时间博主开发了一个旦旦翻牌game的小demo（寒假实验室的学习任务，可以在我的github上找到CardGame仓库），自己写了个翻牌动画。开发之路刚刚起步，需要开发知识和实战经验结合，来小总结一下iOS开发中关于图层和动画的一些基本知识~

​		CoreAnimation是iOS和OS X平台上负责图像渲染与动画的基础框架，也是一组非常强大的动画处理API。**使用CoreAnimation开发动画的本质就是将CALayer中的内容转化为位图供硬件操作。**



## 图层

### **UIView和CALayer**

​		UIView类是所有视图的基类，CALayer是图层类，他们处于同层的平行关系，**每一个UIView都有一个CALayer实例的图层属性**。CALayer是对底层图形API层层封装得到的类，用于展示一些可见的图形元素，保留了一些基本的图形化操作。CALayer用于管理图形元素、制作动画，它保留了一些如尺寸、位置的几何属性。UIView简单来说是可以在里面渲染可见内容的矩形框，里面的内容可以与用户交互，对交互事件进行处理。

​		CALayer可以通过UIView很方便地展示操作UI元素，单独使用灵活度更高，自身具有可设置的属性如背景色、边框和阴影等。

​		UIView继承自UIResponder，UIResponder继承自NSObject；而CALayer直接继承自NSObject。因此两者主要区别为：

- CALayer无法像UIView一样响应用户事件
- 在分工上UIView类侧重于对现实内容的管理和整体布局，而CALayer侧重于显示内容的绘制、显示和动画
- UIView属于UIKit.framework框架，CALayer属于QuartzCore.framework框架



​		iOS中提供UIView和CALayer这两个平行层级结构，实现视图的绘制、显示和布局解耦，避免了很多重复的代码。这样做的带来的好处就例如：UIKit框架不跨平台，在iOS和Mac OS上，两者事件和用户交互的实现有显著不同，但是视图绘制的QuartzCore和Core Graphics两个框架是跨平台的，两个平行层级结构方便两个平台的代码可以共享，使开发更便捷。

​		Layer层对象是用来展示可见内容、渲染视图内容的对象，常用的有：CATextLayer（文字布局渲染）、CAShapeLayer（绘制矢量图形）、CAGradientLayer（主要用于平滑渐变）、CATransformLayer（展示3D图形变换）、CAScrollLayer（实现滚动视图）、AVPlayerLayer（播放视频）、CAEmitterLayer（粒子特效）。这些都是继承自CALayer。



### **UIView及其子类的圆角等效果渲染**

1.离屏渲染（直接设置layer圆角属性）：提前另开缓冲区进行的渲染，比较耗费性能；

2.当前屏幕渲染（自定义圆角绘制方法）：GPU直接在当前显示的屏幕缓冲区渲染，不用离开切换缓冲区，性能较高。

tips：为了防止离屏渲染的损耗，可以将处理图片的权利交于CPU，虽然图形处理性能不如GPU，但是圆角处理难度不大，代价远小于上下文切换，常用方法有：使用CALayer的shouldRasterize设置光栅化、通过CoreGraphics绘制圆角图片（用UIBezierPath剪切图片）

​		（小技巧：可以制作中间镂空的图片，颜色同背景的图片遮在图像上实现圆角效果，避免圆角渲染的性能问题）



### **点与像素**

​		点是iOS中标准的坐标体系与虚拟像素（逻辑像素），一个点对应一个像素，但在Retina屏幕上一个点对应2*2个像素，这个坐标测算体系使在Retina设备和普通设备上有一致的视觉效果。

​		像素是图片分辨率的尺寸单位，即物理像素，和图片有相对关系。UIImage用点来度量大小，一些底层的CGImage类型的图片会使用像素。



### **drawRect方法**

​		drawRect:方法被定义在UIView的UIViewRendering类别中，其中的代码利用Core Graphics在指定的rect中绘制图形，然后缓存起来等待被更新。它会在视图出现时被自动调用，具体是loadView和viewDidLoad两个方法之后，因此没有自定义绘图任务时，不要在子类中写空的drawRect:以免造成资源浪费。可以调用setNeedsDisplay将绘制好的图形更新到视图上（更新的原理是在receiver上设置需要重绘标记，在下个绘制周期自动重绘，一般的刷新频率为60Hz）。



### **anchorPoint与position**

​	anchorPoint相对于当前图层。

​	图层的position相当于视图中的center，是相对于父图层的位置。



## 动画

### **隐式动画与显式动画**

​		隐式动画：UIKit动画的基础，是iOS中创建动态UI界面的最直接的一种方式，通过直接设定UI元素的一些可见属性的目标值（如frame、bounds、center、transform、alpha等），生成属性变化的过渡动画，默认从一个初始状态线性变化到目标状态，例如基于block块的UIView的+animateWithDuration:animations:方法。

​		显示动画：需要显式地定义动画的完整流程，略微复杂的同时会更加灵活，可以实现更加复杂的动画效果。



### **隐式动画原理**

​		更改视图的非根图层的可动画属性时，CoreAnimation自动决定如何并且如何去做动画。动画的执行时间取决于当前事务（CATransaction），而动画类型取决于图层行为。CATransaction类是用于包含一系列属性动画的机制，可以用+begin与+commit来入栈和出栈。

​		CALayer的属性被修改时，会调用actionForKey:方法，原理为：

1. 图层检测是否有代理且代理是否实现了CALayerDelegate协议指定的-actionForKey:forKey方法，若有则直接调用并返回结果。
2. 如果无这样的代理，则图层会接着检查包含属性名称对应行为映射的action字典。
3. 如果action字典不包括对应的属性，则图层会在其style字典里搜索属性名。
4. 如果style中无对应的行为，则图层会调用定义每个属性标准行为的-defaultActionForKey:方法。

​		每个UIView是其根图层的代理并且实现了-actionForKey:forKey方法，默认不执行任何动画即返回nil。还可以通过CATransaction的+setDisableActions来开关属性的隐式动画。



### **CGAffineTransform与CATransform3D**

​		CGAffineTransform：定义在CoreGraphics框架中，主要用于在二维平面对视图进行旋转、缩放和平移，事实上是一个可以和空间向量（如CGPoint）做乘法的3 x 2的矩阵。它被称为“仿射变换”——不论使用什么值的变换矩阵，图层中平行的两条线变换后依然平行。UIView中的transform属性和CALayer中的affineTransform属性都是CGAffineTransform类型。

​		CATransform3D是CoreAnimation结构体，也是一个矩阵，主要用来实现更复杂的关于CALayer的3D操作。其相关动画函数多了z轴参数，CATransform3D中的m34元素用来控制3D变换的透视效果。



### **CATransition**

​		CATransition是CoreAnimation框架提供的转场动画类。

​		其type属性指定了转场动画的类型，主要有4个效果可选：fade、moveIn、push、reveal

​		还有iOS的私有API，使用后在审核有被拒绝的风险：cube、oglFlip、suckEffect、rippleEffect、pageCurl、pageUnCurl、cameraIrisHollowOpen、cameraIrisHollowClose



### **UIVIew过渡动画**

1. 使用CATransition
2. 使用UIView的过渡动画API，+transformWithView与+transformFromView:ToView
3. 自定义过渡动画，基础原则为对原始图层外观截图，添加一段动画平滑过渡到图层改变后截图的效果。调用CALayer中的renderInContext:方法可以将图层内容绘制成图片然后在视图在显示出来。



### **CAKeyframeAnimation**

​		CAKeyframeAnimation，即关键帧动画，作用于单一的属性，是CAPropertyAnimation的一个子类，可以根据开发者提供的一连串随意的值来做动画，这些值即关键帧，每帧之间的绘制将由系统自动完成。

​		使用时需要注意，关键帧动画不能将当前值作为第一帧，动画执行后会自动跳转到第一帧的值，动画结束后会突然恢复到最后一帧的值。还可以使用path属性来指定关键帧，且做动画的属性不一定是可动画属性，可以对子属性和虚拟属性做动画，例如指定keyPath为transform.rotation来做旋转动画。



### **UIViewController之间的转场动画**

​		使用常用的跳转方式：push、pop、present与dismiss等，系统会提供默认的动画效果。

​		自定义转场动画时，需要注意的概念：

1. presentingView指源视图控制器，presentedView指跳转目标控制器；
2. UIViewControllerTransitionDelegate协议用于为跳转动画实现了UIViewControllerAnimatedTransitioning协议的对象，而UIViewControllerAnimatedTransitioning协议用于开展动画的展示时间和展示逻辑
3. UIViewControllerInteractiveTransitioning协议即交互式转场动画代理，主要用于交互式动画
4. UIViewControllerContextTransitioning协议即转场动画上下文协议，为动画提供必备的信息



​		实现自定义动画需要代表页面跳转关系的两个控制器对象和动画执行逻辑，实现的步骤为：

1. 为源控制器和目标控制器分别设置一个遵守UIViewControllerAnimatedTransitioning协议的代理对象（可以设置为同一个对象）；
2. 调用对应的跳转方法，此时系统会自动请求动画代理提供动画逻辑对象。



### **保持视图界面为动画结束时的状态**

​	实际开发中有时会发现动画结束时，视图状态会快速返回到初始状态，因为动画过程中所看到的是呈现图层的效果，而图层属性真正的值并非是呈现的效果值，解决方法为：

1. 设置动画对象的removeOnCompletion属性为NO。
2. 设置动画对象的fillMode为kCAFillModeForwards，即填充动画状态。
3. 为动画对象添加非空的键，以便不需要动画的时候将它从图层上移除。
