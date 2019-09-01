### Hybrid APP

#### 简述

>  移动端应用开发, 可以查看技术栈里面的相关文档, 这里不对移动端开发做相关描述, 这里只是记录一些移动端里面遇到的一些问题, 以及解决方案

`Hybrid App`（混合模式移动应用）是指介于 ` web-app `, ` native-app ` 这两者之间的 app，兼具 ` Native App ` 良好用户交互体验的优势和 ` Web App ` 跨平台开发的优势,
相对于 ` Native ` 应用(` Android `, ` iOS ` 原生应用), 混合开发的模式, 很大程度上面加快了开发效率, 缩小了开发周期和维护成本, 原本需要多种语言去开发的APP应用,
H5 就能完全胜任; 这也是 H5 目前比较火热的原因;(当然 ` NodeJS ` 和小程序也是一个比较重要的因素);

目前, 主流的混合APP开发平台有 ` react-native ` , ` Cordova` , ` Dcloud `, `ApiCloud`, 包括阿里开源出来的 `Weex`; 自己目前用的比较多的就是 `Dcloud`;
而且, `Dcloud` 还有一款比较便捷的开发工具, 用来做前端开发还是非常好用的; [Dcloud](https://dcloud.io/)

#### 发行Android和iOS原生应用包
环境：
- Java (Java8以上)
- AppUploader
- iPhone 实用配置工具
- OpenSSl 

`Android` 要求所有的 `apk` 包必须先要使用证书进行数字签名, 然后才能安装, `Android`系统在安装`APK`的时候，首先会检验`APK`的签名，如果发现签名文件不存在或者校验签名失败，则会拒绝安装，所以应用程序在发布之前一定要进行签名;

`iOS` 的安装包要求更苛刻, 必须要有开发者证书, 才能把应用打包成 `.ipa` 文件, 并且需要做应用签名; 签名完成之后需要发布上架到 `AppStore` 才能下载安装到手机上使用;

`Android` 的应用签名是很简单的, 而且, 我们开发的应用也可以直接使用 `Dcloud` 官方给我们提供的公有[签名](http://ask.dcloud.net.cn/article/68);
这个文件下载下来之后, 



#### OpenSSL签名

> https 证书文件为 .mobileconfig 文件进行签名验证; 安装描述文件的时候会显示 ` 已验证 `;

首先, 我们要在 `windows` 系统上安装



#### 跨域问题