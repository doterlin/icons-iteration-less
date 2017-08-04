# icons-iteration-less
源码地址：[https://github.com/doterlin/icons-iteration-less](https://github.com/doterlin/icons-iteration-less)
##为什么需要字体图标
当设计师给到我们的图标是一些图片，如果交互上要修改图标的经过效果、颜色、大小等，我们需要去修改图片或者去修改`css`的`width`和`height`等；这种情况在对图标的数量和修改度很小的情况下是可取的。
但不是最好的。如果我们使用的图标是字体，而不是一张张图片，修改大小和颜色就轻而易举。

## 生成字体图标
有些线上的网站可以把`svg`转换成字体文件，比如阿里的[iconfont](http://iconfont.cn/)：
![iconfont.cn](http://upload-images.jianshu.io/upload_images/3169607-8e99a153e413c7bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上面上传你的svg(icon一般被设计成矢量的，设计软件有导出`svg`的功能)，加入购物车后点击下载代码：
![选择下载代码](http://upload-images.jianshu.io/upload_images/3169607-f7a314b78fbc7275.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载后解压可以拿到我们后面需要的四个文件：

![四个文件](http://upload-images.jianshu.io/upload_images/3169607-eb708e52b99762df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当然 你需要知道每个字体对应的编码和使用方法，可以在`demo_unicode.html`中查看：

![demo_unicode.html](http://upload-images.jianshu.io/upload_images/3169607-9f7e0bdfed5b6f79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

拿到这些字体文件后，我们说一下使用步骤。

##使用字体图标
将所需文件放到你觉得合理的目录，然后分两步走：
**1.定义字体及字体的基本样式**：
```
/*css*/
@font-face {
        font-family: "iconfont";
        src: url('/font/iconfont.eot');
        src: url('/font/iconfont.eot?#iefix') format('embedded-opentype'),
        url('/ifont/iconfont.woff') format('woff'),
        url('/font/iconfont.ttf') format('truetype'),
        url('/font/iconfont.svg#iconfont') format('svg');
    }

.icon {
    display: inline-block;
    speak: none;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    text-rendering: auto;
    vertical-align: middle;
    line-height: 1;
    -webkit-font-smoothing: antialiased;
    -webkit-text-stroke-width: 0.2px;
    -moz-osx-font-smoothing: grayscale;
}
```
**2.调用.icon类**：
```
<!--html-->
<i class="'icon'" style="font-size: 18px; color:#333">&#xe602</i>
```
在元素内容填写要显示的图标对应的编码（类似`&#xe602`，可在`demo_unicode.html`中查找）就ok了。

## 版本迭代
当图标不能满足当前的ui需求，直接扩充数量后导出字体文件会有与原图标字体文件时生成`unicode`不一致问题，之前代码中使用的编码就要重写。这个我们可以用css预编译处理。
**1.使用less分管icon版本**：
```
//icon.less
// 根据版本号定义字体
.setFontFace(@verison) {
    @font-face {
        font-family: "iconfont@{verison}";
        src: url('/img/icon/@{verison}/iconfont.eot');
        src: url('/img/icon/@{verison}/iconfont.eot?#iefix') format('embedded-opentype'),
        url('/img/icon/@{verison}/iconfont.woff') format('woff'),
        url('/img/icon/@{verison}/iconfont.ttf') format('truetype'),
        url('/img/icon/@{verison}/iconfont.svg#iconfont') format('svg');
    }
}

.setFontStyle(@verison) {
    .icon-@{verison} {
        font-family: "iconfont@{verison}";
    }
}

.setFont(@verison) {
    .setFontFace(@verison);
    .setFontStyle(@verison);
}
// end 根据版本号定义字体

//公共样式
.icon {
    display: inline-block;
    speak: none;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    text-rendering: auto;
    vertical-align: middle;
    line-height: 1;
    -webkit-font-smoothing: antialiased;
    -webkit-text-stroke-width: 0.2px;
    -moz-osx-font-smoothing: grayscale;
}
// end 公共样式

// 定义字体, 即ver="xxx"；如定义字体ver="2"就添加一行.setFont(2);
.setFont(1);
.setFont(2);
```
这样我们在定义一批新图标时只需调用一下`mixins`，比如我要定义一个`3`版本的icon：
```
.setFont(3);
```

**2.调用**
引入编译后的css后如下调用：
```
<!--html-->
<!-- icon-*  其中*号就是你定义的版本号-->
<i class="'icon icon-1'" style="font-size: 18px; color:#333">&#xe602</i>
<i class="'icon icon-1'" style="font-size: 18px; color:#333">&#xe604</i>
<i class="'icon icon-3'" style="font-size: 18px; color:#333">&#xe605</i>
...
```
**写成组件（可选）**
如果使用的MVVM框架，可以考虑把它写一个组件更方便：
```
//vue.js demo
//icon.vue
<template>
    <i :class="'icon icon-' + ver" :style="styles" v-html="type"></i>
</template>
<script>
    export default {
        name: 'Icon',
        props: {
            type: String,
            size: [Number, String],
            color: String,
            ver: {
                type: String,
                default: '1'
            }
        },
        computed: {
            styles () {
                let style = {};
                if (this.size) {
                    style['font-size'] = `${this.size}px`;
                }
                if (this.color) {
                    style.color = this.color;
                }
                return style;
            }
        }
    };
</script>
```
调用方式如下：
```
//在父组件引入icon.vue并写入模板
<Icon type="&#xe602" size="18" ver="1" color="#333"></Icon>
<Icon type="&#xe604" size="18" ver="2" color="#333"></Icon>
<Icon type="&#xe605" size="18" ver="3" color="#333"></Icon>
...
```
个人经验总结。就介绍到这里，需要查看代码和demo的请点此github：
[https://github.com/doterlin/icons-iteration-less](https://github.com/doterlin/icons-iteration-less)

