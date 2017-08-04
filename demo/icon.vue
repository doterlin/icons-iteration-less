<!--
    使用方法如下(type直接传图标的unicode编码，各编码请查看对应版本下截图，如font/1/unicode.png)：
        <Icon type="&#xe602" size="18" ver="1" color="#333"></Icon>
    关于参数`ver`: ver是为了图标可拓展，防止直接覆盖原图标字体文件时出现unicode不一致问题。
    新增一批图标时，需要：
      1.使用工具（如http://iconfont.cn/）将svg图标生成图标字体文件(详情参考public/img/zyIcon/1)
      2.放入/font下新增的一个文件夹，保存字体文件和一张截图示意这批图标字体的unicode码。
      3.less/icons.less定义字体：.setFont('1');
-->
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