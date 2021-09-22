# iOS中的数据持久化


# iOS中的数据持久化

​		数据持久化是应用开发中的重要部分，博主在编写一个网易彩票app的demo时(项目可以在我的github上找到，WYLottery)使用到了数据持久化来保存用户的设置，设置界面搭建也是借助plist文件来保存层次和属性，今天来整理一下iOS中数据持久化的方案与原理。iOS中的数据持久化的方案主要包括：**NSUserDefault简单数据快速读写、Property list属性列表文件存储、Archive归档、SQLite本地数据库和CoreData。**

## 数据持久化方案

#### **NSUserDefault与Archive归档**

​		NSUserDefault是iOS系统提供的一个单例类，采用简单的键值对存储方式，将用户的偏好设置保存到应用包的plist中，同时也能从中读取。添加新数据时需要自定义key和对应的基本类型的数据，获取时通过自定义key来获取，若不存在对应key则返回默认值（0或-1）。虽然存储的时基本类型数据，但我们也可以将集合数据转化成JSON字符串，以NSString的类型保存，通过这样来存储集合数据，便于加密解密。

​		通过+standardUserDefaults来获取NSUserDefault单例。通过-setObject:ForKey:和-object:ForKey:来存取数据。

​		序列化和归档（Archive），指的是将程序语言的对象转化成二进制流从而存储到文件中，因为对象不可以直接存储，反序列化和反归档（Unarchive）则反之。NSKeyedUnarchiver类继承自NSCoder，可以将满足NSCoding协议的对象进行归档，和NSData类型进行相互转换。若要实现多个对象同时归档和反归档，可以自定义并初始化一个NSKeyedArchiver对象，将数据封装在NSMutableData中进行统一的归档和反归档。



​		如果要想通过NSUserDefault保存自定义对象，需要做以下额外操作：

1. 保证自定义类遵循NSCoding协议并实现协议进行编码/反编码；
2. 使用NSKeyedUnarchiver类将自定义对象转换成NSData对象。

​		这样自定义对象转化成了NSData，就可以用NSUserDefault存储了，只不过取数据的时候需要NSKeyedUnarchiver来解档还原。归档成NSData对象的对应的归档解档方法（也可以调用别的方法归档成.achiver文件）分别为+archivedDataWithRootObject:与+unarchiveObjectWithData:。



#### Property list文件存储

​		Property List文件，扩展名为.plist，所以常被称为plist文件。主要用来存储序列化后的对象，文件是xml格式的，常用于存储用户的设置信息，也可以用于存储捆绑的信息等。

​		在使用plist文件存储前先要明确数据的不同存储位置：

1. 存储到应用沙盒（可写可读，详见后文），沙盒是应用的隔离区域，plist存储在沙盒的Documents文件目录下，写入时要指明plist文件的文件名，如“/demo.plist”

2. 存储到应用工程本身（只可读）。如果是将数据存在工程里，那么plist数据文件是手工创建的，而非代码添加，代码只对手工创建的plist文件进行读取且不可更新，更新只能通过手动修改plist文件更新数据。

   读取工程plist文件并写入沙盒以及读取沙盒plist文件



#### SQLite本地数据库

​		iOS中的SQLite是一个使用ANSI-C开发的自包含的关系型数据库，它可以用于存储大规模的数据。SQLite的操作管理很简单，有小巧、快速、可靠的特点。另外SQLite使用互斥来保证多线程环境下数据操作的安全性。

​	    SQLite源代码是一个叫sqlite3.c的文件，在使用时只要加入这个库文件同时在代码中引入sqlite.h头文件即可



#### CoreData

​		CoreData是苹果公司提供的一种应用数据管理框架，可以用图形界面的方式快速地定义App的数据类型，同时在代码中很容易获取这些数据类型。CoreData提供了基础结构用于处理常用的功能，如保护、恢复、撤销和重做，允许在App中继续创建新的任务。在使用CoreData的时候，不需要安装额外的数据库系统，因为CoreData使用内置的SQLite数据库。

​		CoreData的最本质特点是提供了一种将对象模型和关系数据进行映射的功能，称为对象-关系映射（ORM），可以将模型对象和关系数据相互转换在SQLite数据库中存取。



## 沙盒机制

​		沙盒是应用程序中一块相对封闭的独立空间，需要通过特殊限制通道才能访问沙盒外系统的资源。沙盒是为了系统安全而设置的访问屏障，限制应用程序访问系统文件、系统偏好、网络资源和硬件设备等，沙盒内的应用不会对系统安全有威胁。**应用程序只能访问自己的沙盒，应用程序之间禁止数据的共享和访问。**

#### 沙盒的子目录

​		**Documents目录**

​		主要用于存储非常大的文件或需要非常频繁更新的数据，目录中的文件可以进行iTunes或iCloud的备份。

​		**Library目录**

​		Library目录下后Preferences和Cache目录，如其名，Preferences目录主要用于存储应用程序的偏好设置数据，可以进行iTunes或iCloud的备份。而Caches目录不可以进行iTunes或iCloud的备份，主要用于存放存储体积大、不需要备份的数据缓存文件。

​		**tmp目录**

​		tmp目录是应用程序的临时目录，里面的文件不可以进行iTunes或iCloud的备份，而且这里面的文件可能会随时被系统清除。

​		虽然不能直接访问其他应用程序中的数据，但是可以借助iOS提供的特定的API访问一些特殊应用，如联系人应用等。

​		


