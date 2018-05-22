Android数据库Realm
====================
前言
-----------
>　　Android原生的Sqlite数据库用起来是比较麻烦的。当然，如果你封装的足够好也是蛮好用的。博主的封装功底只能说非常的一般，所以会觉得用起来比较麻烦。而且写sql语句的时候，如果小手一抖多了一个空格什么的。你懂的。



><a href="https://realm.io/docs/java/4.0.0/#migrations">官方文档点这里（Realm 4.0.0）</a>

　　集成步骤这里就不讲了，官方文档里面讲的很清楚了（图文并茂）。


1.初始化并配置数据库
```kotlin
		Realm.init(this)//
        var realmConfig = RealmConfiguration.Builder()//创建自定义配置
                ?.name("shtoone.realm")//数据库文件名
                ?.schemaVersion(0)//数据库版本号，表结构更改后要更新版本号
                ?.migration(MyMigration())
//                ?.deleteRealmIfMigrationNeeded()//开发时使用，上线时注释
                ?.build()
        Realm.setDefaultConfiguration(realmConfig)//使用自定义配置
        Realm.getDefaultInstance()//获取数据库实例
```
2.创建表
>　　在realm中一个Java实体类只要继承了RealmObject就是一张表。实体类的成员变量就是表中的字段。可以通过注解@PrimaryKey来设置表中的主键。

```kotlin
open class Cat : RealmObject() {
	@PrimaryKey
    var name: String? = null
    var age: Int? = null
}
```
3.插入数据
>　　在realm中，想数据库插入数据有多种写法。不变的是事务机制。realm实例不要写成全局变量，否则会出现线程问题。在哪里使用，就在哪里创建实例。及时回收即可。

```kotlin
	//insert写法
	var realm = Realm.getDefaultInstance()
	realm?.beginTransaction()
	var cat = Cat()
	cat?.name = "Tom"
	cat?.age = 9
	realm?.insert(cat)
	realm?.commitTransaction()
	realm.close()

	//createObject写法
	var realm = Realm.getDefaultInstance()
	realm?.beginTransaction()
	var cat = realm?.createObject(Cat::class.java)
	cat?.name = "Tom"
	cat?.age = 9
	realm?.commitTransaction()
	realm.close()

	//copyToRealm写法
	var realm = Realm.getDefaultInstance()
	realm?.beginTransaction()
	var cat = Cat()
	cat?.name = "Tom"
	cat?.age = 9
	realm?.copyToRealm(cat)
	realm?.commitTransaction()
	realm.close()	
```
4.查询数据
>　　realm为查询操作提供了很多的API方便开发者使用，可以根据需求自由组合。另外查询操作是不需要开启事务的，注意线程问题即可。

```kotlin
var realm = Realm.getDefaultInstance()
var result = realm?.where(Cat::java.class)
				  ?.equalTo("age",9)
				  ?.or()//或者
				  ?.equalTo("name","Tom")
				  ?.findAll()
//				  ?.findAllSorted("age",Sort.DESCENDING)//对结果进行进行排序（降序）
//可跟查询条件  
//.or()                      或者  
//.beginsWith()              以xxx开头  
//.endsWith()                以xxx结尾  
//.greaterThan()             大于  
//.greaterThanOrEqualTo()    大于或等于  
//.lessThan()                小于  
//.lessThanOrEqualTo()       小于或等于  
//.equalTo()                 等于  
//.notEqualTo()              不等于  
//.findAll()                 查询所有  
//.average()                 平均值  
//.beginGroup()              开始分组  
//.endGroup()                结束分组  
//.between()                 在a和b之间  
//.contains()                包含xxx  
//.count()                   统计数量  
//.distinct()                去除重复  
//.findFirst()               返回结果集的第一行记录  
//.isNotEmpty()              非空串  
//.isEmpty()                 为空串  
//.isNotNull()               非空对象  
//.isNull()                  为空对象  
//.max()                     最大值  
//.maximumDate()             最大日期  
//.min()                     最小值  
//.minimumDate()             最小日期  
//.sum()                     求和  
```
5.删除
