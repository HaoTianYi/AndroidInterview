# picasso基本使用

[TOC]

## 基本说明

picasso的github地址：https://github.com/square/picasso

picasso是Square公司开源的一个Android图形缓存库，地址[http://square.github.io/picasso/](http://square.github.io/picasso/)，可以实现图片下载和缓存功能

### 基本使用

首先使用jar或者使用gradle：

```
compile 'com.squareup.picasso:picasso:2.5.2'
```

### 加载图片

```
Picasso.with(context).load(path).resize(width, height).centerCrop().into(imageView);
```

仅仅要一行代码

![img](http://oaxelf1sk.bkt.clouddn.com/2E0BD9~1.GIF)

解释一下基本含义：

```
    /**
     * 显示图片
     * @param context 上下文
     * @param path 图片路径
     * @param width 图片的宽
     * @param height 图片的高
     * @param imageView 对应的显示位置
     */
    public static void loadImageWithSize(Context context, String path, int width, int height, ImageView imageView) {
        Picasso.with(context).load(path).resize(width, height).centerCrop().into(imageView);
    }
```

只要完成以上步骤那么就可以实现图片的加载和缓存,注意添加权限。

![sp161001_143955](http://oaxelf1sk.bkt.clouddn.com/sp161001_143955.png)

点击按钮，加载出百度的图标

## 更多使用方法

### 默认和错误的显示图片

当图片不存在或者加载失败以及没有网络的时候都应该有一个图片实现而不是空的imageview

**显示默认的图片**

```
Picasso.with(context).load(path).fit().placeholder(resID).into(imageView);
```

也就是palcehoder这个方法在起作用

**错误图片**

```
Picasso.with(context).load(path).fit().error(resID).into(imageView);
```

就是error这个方法

### 自定义固定的裁剪

比如说只想要图片的左上角二分之一的部分，首先要实现一个接口Transformation，其中一定要实现两个方法，一个是返回的bitmap一个是缓存时对应的唯一key：

```
    /**
     * 实现固定的裁剪方式
     */
    private static class CropSquareTransformation implements Transformation {

        @Override
        public Bitmap transform(Bitmap source) {
            Bitmap result = Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight());
            if (result != null) {
                source.recycle();
            }
            return result;
        }

        @Override
        public String key() {
            return "result";
        }
    }
```

注意一定要清理一下缓存，也就是：

```
            if (result != null) {
                source.recycle();
            }
```

### 完整代码

```
public class PIcassoUtils {
    /**
     * 显示图片
     * @param context 上下文
     * @param path 图片路径
     * @param width 图片的宽
     * @param height 图片的高
     * @param imageView 对应的显示位置
     */
    public static void loadImageWithSize(Context context, String path, int width, int height, ImageView imageView) {
        Picasso.with(context).load(path).resize(width, height).centerCrop().into(imageView);
    }

    public static void loadImageWithHolder(Context context, String path, int resID, ImageView imageView) {
        Picasso.with(context).load(path).fit().placeholder(resID).into(imageView);
    }

    public static void loadImageWithCrop(Context context, String path, ImageView imageView) {
        Picasso.with(context).load(path).transform(new CropSquareTransformation()).into(imageView);
    }

    /**
     * 实现固定的裁剪方式
     */
    private static class CropSquareTransformation implements Transformation {

        @Override
        public Bitmap transform(Bitmap source) {
            Bitmap result = Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight());
            if (result != null) {
                source.recycle();
            }
            return result;
        }

        @Override
        public String key() {
            return "result";
        }
    }
}
```

api文档：http://square.github.io/picasso/2.x/picasso/