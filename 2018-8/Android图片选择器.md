#Android图片选择器
>　　GitHub上数千star的图片选择器有不少，但是有很多功能我是不需要的。可是你依赖了别人的库，里面的东西就算没用到也要在那放着（无形中让apk包变大，没用到就相当于过度封装）。
>　　这些库都是支持自定义UI，但是自定义UI很难做出设计师想要的效果。
>　　所有，我决定先实现一个最简单的图片选择器。后期有什么需求往上加就行了。不做过度封装。

##整体逻辑
>　　　1.获取手机所有图片
>　　　2.展示图片
>　　　3.选择图片
>　　　4.返回图片

###1.获取图片
>　　现在的图片选择器，根据文件夹分类是标配。所以，拿到图片后还需要进行处理。

####1.1权限校验
　　现在市场上主流Android系统版本主要是5.X及以上的版本。6.0的动态权限，想必大家都了解。所以这里在进行图片获取之前（从手机sd卡获取），我们要先**校验权限**。不然很容易导致APP因为安全问题而Crash。
　　权限校验我用的是谷歌官方提供的EasyPermissions框架。

```java
　　　　if (EasyPermissions.hasPermissions(this, Manifest.permission.READ_EXTERNAL_STORAGE)) {
            images = scanPic(actions)
            adapter = ImageAdapter(this, R.layout.item_image, images)
            recycler_view?.adapter = adapter
            configDialog()
        } else {
            //没有读取权限
            EasyPermissions.requestPermissions(this, "扫描手机图片", REQUEST_CODE_1, Manifest.permission.READ_EXTERNAL_STORAGE)
        }


```

####1.2获取图片（**核心**）
>　　图片是通过ContentResolver来获取的，api的使用及写法参考官方文档。
>　　在拿到图片后，我对图片做了一些处理。根据文件夹进行了分类。
>　　下面放一条图片路径：
>　　　　/storage/emulated/0/DCIM/Camera/IMG_20180627_143330.jpg


```java
class Image(var name: String, var path: String, var time: String, var folderName: String) {
    override fun toString(): String {
        return "Image(name='$name', path='$path', time='$time', folderName='$folderName')"
    }
}
```



```java

var actions = arrayOf(MediaStore.Images.Media.DATA,
            MediaStore.Images.Media.DISPLAY_NAME,
            MediaStore.Images.Media.DATE_ADDED,
            MediaStore.Images.Media._ID)

private fun scanPic(actions: Array<String>): MutableList<Image> {
        var images = mutableListOf<Image>()
        //扫描图片
        var imageUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
        var result = contentResolver?.query(imageUri, actions, null, null, MediaStore.Images.Media.DATE_ADDED+" DESC")//降序排列，注意空格

        //读取扫描图片
        while (result?.moveToNext()!!) {
            //图片路径
            var path = result?.getString(result.getColumnIndex(MediaStore.Images.Media.DATA))
            //图片名称
            var name = result?.getString(result.getColumnIndex(MediaStore.Images.Media.DISPLAY_NAME))
            //图片创建时间
            var time = result?.getString(result.getColumnIndex(MediaStore.Images.Media.DATE_ADDED))
            //切割路径
            var cutResult = path?.split("/")
            //获取图片所在文件夹名
            var image = Image(name, path, time, cutResult?.get(cutResult?.size - 2)!!)
            //文件夹名添加到集合，用于展示和切换文件夹
            if (!folders?.contains(image?.folderName)) {
                folders?.add(image?.folderName)
            } else {//文件夹名存在，图片放入set集合
                if (folderMap?.containsKey(image?.folderName)) {//文件夹存在,图片路径放入对应集合
                    folderMap?.get(image.folderName)?.add(image)
                } else {
                    var images = mutableListOf<Image>()
                    images?.add(image)
                    folderMap?.put(image?.folderName, images)
                }
            }
            images?.add(image)
        }

        return images
    }

```

####2.展示图片
>　　使用RecyclerView展示图片，布局管理器用的是GridLayoutManager。



```java
        recycler_view?.layoutManager = GridLayoutManager(this, 4)
 　　　　images = scanPic(actions)
        adapter = ImageAdapter(this, R.layout.item_image, images)
        recycler_view?.adapter = adapter
```

####3.图片选择
>　　这里无非就是在adapter的点击事件中进行操作，不做过多描述。


####4.图片返回
>　　图片选择器基本上都是在专门的activity中进行操作，所有选择的结果可以通过intent带回去。
