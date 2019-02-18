# 百度小程序

!> 这里记录问题，可能随着各个小程序不断迭代升级优化，有些问题可能已被修复。

## 问题集锦

* 自定义组件中的图片路径是相对于引用页面的，这是实现问题，最新版本会在 `2019.1.3` [修复上线](https://smartprogram.baidu.com/forum/topic/show/64967)。因此对于此情况，想多平台生效，可以将路径设置为 相对于 小程序项目目录的 绝对路径，如: `'/common/img/x.png'`, 具体路径，视自己项目情况而定。

* 百度小程序非兼容性升级关注
    * [2018.11.23](https://smartprogram.baidu.com/forum/topic/show/64420)
    * [2018.12.14](https://smartprogram.baidu.com/forum/topic/show/64967)

* 字体图标 真机ios正常，安卓不正常
