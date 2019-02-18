# 支付宝小程序

!> 这里记录问题，可能随着各个小程序不断迭代升级优化，有些问题可能已被修复。

## 问题锦集

* 目前支付宝开发者工具是默认支持 `ES6` 语法，但部分语法支持有点问题

    模块 a 定义：
    ```javascript
    // ./a.js
    import * as abc from 'xxx';
    export default abc; // 这种写法会导致默认导出没有成功
    // export {abc as default}; // 这样也是没法导出成功
    // export default Object.assign{{}, abc}; // 这样是 ok 的
    // export {abc}; // 不使用默认导出，这样是 ok 的
    ```

    模块 b 导入模块 a 定义：
    ```javascript
    // ./b.js
    import abc from 'a';
    // abc undefined
    ```

* 如果脚本文件内容是空的，会导致开发者工具报错

* 开发者工具有时候点击左上角返回上一级页面会报：`Cannot read property 'NBPageUrl' of undefined` ：具体原因不详，可以把开发者工具生成的 `.tea` 目录（构建出来项目目录下）删掉，退出开发者工具再进入，自己就会好了。

* 构建配置 `localPolyfill` 引入 `promise` 会导致 `IOS` 下预览出错，目前所有小程序都是默认支持 `Promise`，因此不再需要做 `promise` `polyfill`，解决办法删掉该 `promise` 配置项即可修复，具体原因不详。

* 不支持 `icon` 上绑事件
