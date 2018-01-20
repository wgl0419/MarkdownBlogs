**java.lang.NoClassDefFoundError之TabLayout**
===
>在组合使用ViewPager和TabLayout时，编译器报如下异常  
```java
java.lang.NoClassDefFoundError: Failed resolution of: Landroid/support/v7/internal/widget/TintManager;
	at android.support.design.widget.TabLayout$TabView.<init>(TabLayout.java:1041)
	at android.support.design.widget.TabLayout.createTabView(TabLayout.java:582)
	at android.support.design.widget.TabLayout.addTabView(TabLayout.java:616)
	at android.support.design.widget.TabLayout.addTab(TabLayout.java:334)
	at android.support.design.widget.TabLayout.addTab(TabLayout.java:309)

```
>谷歌了一番，发现是版本版本不一致导致的。至少要保证design包和recyclerview包的大版本一致。  
```android
compile 'com.android.support:design:23.2.0'
compile 'com.android.support:recyclerview-v7:23.1.1'
```