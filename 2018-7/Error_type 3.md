Error type 3 Error: Activity class {} does not exist
============

>　　我在华为8.0的手机上测试demo，本来只是想在应用管理中看一下应用的大小。但是小手一抖，点了卸载。然后就炸了，用AndroidStudio往手机上run程序一直报标题上的错误。
>　　我去百度了一下，真是千奇百怪的原因都有。嗯，然后就是千奇百怪的解决办法。我感觉我自己的问题和他们不太一样，但还是把他们的方法都试了一遍。并没有什么卵用。
>　　根据报错，我大概猜到是因为没有卸载干净的缘故。但是我在华为自带的应用管理中又找不到这个已经被卸载的程序。日了狗。包括用adb命令查包名也是没有。

```java
adb shell pm list packages //
```
>百度是救不了中国人的，我只能谷歌了。然后在stackoverflow里面找，还真让我找到了。
><a href="https://stackoverflow.com/questions/20915266/error-type-3-error-activity-class-does-not-exist">这是链接</a>
>下面是外国朋友的原话：
>
Using Android O developer preview 3, I could not perform the Uninstall for all users action, as it told me that the app did not exist in apps list. Running adb uninstall {packageName} solved the problem. – alexbchr Jun 
>大意就是在Android8.0的系统下，卸载操作出问题了。然后用adb命令卸载掉了。感觉跟我的情况差不多，就试了一下。嗯，问题解决了。

```java
adb uninstall {com.xxx.xxx（包名）}
```
