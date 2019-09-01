#### 简述

>  移动端应用开发, 可以查看技术栈里面的相关文档, 这里不对移动端开发做相关描述, 这里只是记录一些移动端里面遇到的一些问题, 以及解决方案

`Hybrid App`（混合模式移动应用）是指介于 ` web-app `, ` native-app ` 这两者之间的 app，兼具 ` Native App ` 良好用户交互体验的优势和 ` Web App ` 跨平台开发的优势,
相对于 ` Native ` 应用(` Android `, ` iOS ` 原生应用), 混合开发的模式, 很大程度上面加快了开发效率, 缩小了开发周期和维护成本, 原本需要多种语言去开发的APP应用,
H5 就能完全胜任; 这也是 H5 目前比较火热的原因;(当然 ` NodeJS ` 和小程序也是一个比较重要的因素);

目前, 主流的混合APP开发平台有 ` react-native ` , ` Dcloud `, `ApiCloud`, 包括阿里开源出来的 `Weex`; 自己目前用的比较多的就是 `Dcloud`;
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

> iphone实用配置工具安装MobileConfig 并且 安装Win64OpenSSL-1_1_1c.exe

步骤:

- 1 用iphone配置工具申请一个 .mobileconfig文件
- 2 http申请出来的两个证书（自己玩就申请，工作就管运维要） mbaike.crt mbaike.key
- 3 .mobileconfig文件改名字为 unsigned.mobileconfig
- 4 运行命令 openssl smime -sign -in unsigned.mobileconfig -out signed.mobileconfig -signer mbaike.crt -inkey mbaikenopass.key -certfile ca-bundle.pem -outform der -nodetach


#### WebView

> 基于 mui和 hbuilder

- 建立 js 文件夹 放入 mui.min.js

- 建立 error.html 错误页面

`````html
<body>
    <img class="error" src="error.jpg" />
    <div class="btn">
        <button class="restart" style="margin-right: 15px;">点我刷新</button>
        <button class="redirect">浏览器打开</button>
    </div>
    <script src="js/mui.min.js" type="text/javascript" charset="utf-8"></script>
    <script type="text/javascript">
        var btn = document.querySelector();
        var btnRedirect = document.querySelector();
        btn.onclick = function() {
            mui.plusReady(function() {
                plus.runtime.restart(); // 重新运行
            })
        }			
        btnRedirect.onclick = function() { // 重定向网址
            mui.plusReady(function() {
                plus.runtime.openURL('网址');
            })
        }
    </script>
</body>
`````

- 建立 index.html

`````html
<!--<style type="text/css">
    #progress{
        position: fixed;
        height: 5px;
        top: 0;
        left: 0;
        width: 0%;
        background: #0eef0e;
    }
</style>-->
<head>
    <script type="text/javascript">
        // 如果有异步请求
    	(function(){
    		try{
    			var xhr = new XMLHttpRequest(); 
    			xhr.timeout = 1000;
	    		xhr.onreadystatechange = function(){
	    			console.log(xhr.readyState);
	    		}
	    		xhr.open('GET',"",true);
	    		xhr.send(null);
    		}catch(e){
    			console.log(e);
    		}
    	})();
    </script>
</head>
`````

`````html
<body>
	<div id="progress"></div>
	
	<script src="js/mui.min.js" type="text/javascript" charset="utf-8"></script>
	<script>		
		
		/*超过三天清除 localstorage*/
		
		const currentTime = localStorage.getItem('FirstTime'),
		nowTime = new Date().getTime();
		
		if(currentTime){
			if(nowTime - currentTime >= 1000 * 60 * 60 * 24 * 3){
				localStorage.clear();
				localStorage.setItem('FirstTime', nowTime);
			}
		}else{
			
			localStorage.setItem('FirstTime', nowTime);
			
		}
		
	</script>
	<script type="text/javascript">
		var Url = 'https://song-d-ace.github.io/#/',
			Index = null,
			loading = null,
			interval = null,
			num = 0,
			progress = document.querySelector('#progress');			
		mui.plusReady(function(){
			//禁用左滑
			plus.webview.currentWebview().setStyle({  
                'popGesture': 'none'  
            }); 			
			//首次进去显示loading框
			loading = plus.nativeUI.showWaiting('正在加载...',{
				width:"150px",
				height:"150px",
				size:"18px",
				padding:"10px",
				loading:{					
				}
			})			
			//创建一个新的webview
			Index = plus.webview.create(Url,'index',{
				top:'0px',
			    bottom:'0px'
			})			
			//设置加载进度条的宽度
			interval = setInterval(function(){
				num += 0.3;
				if(num >= 90){
					num = 90;
					interval = null;
				}
				progress.style.width = num +'%';
			},10)			
			//判断是否有加载完成
			Index.addEventListener('loaded',function(){
				progress.style.width = '100%';
				plus.nativeUI.closeWaiting();
				plus.webview.show(Index);
				
				//创建完成之后禁用侧滑事件
				plus.webview.getWebviewById('index').setStyle({
					'popGesture': 'none' 
				})
				
			})
		})		
	</script>
</body>
`````



- Dcloud 打包 APP 常见问题

1. Android 手机的 back 键直接退出应用的问题

`````javascript
<script>
document.addEventListener('plusready', function() {
	var webview = plus.webview.currentWebview();
	plus.key.addEventListener('backbutton', function() {
		webview.canBack(function(e) {
			if(e.canBack) {
				webview.back();             
			} else {
				//webview.close(); //hide,quit
				//plus.runtime.quit();
				mui.plusReady(function() {
					//首页返回键处理
					//处理逻辑：1秒内，连续两次按返回键，则退出应用；
					var first = null;
					plus.key.addEventListener('backbutton', function() {
						//首次按键，提示‘再按一次退出应用’
						if (!first) {
							first = new Date().getTime();
							mui.toast('再按一次退出应用');
							setTimeout(function() {
								first = null;
							}, 1000);
						} else {
							if (new Date().getTime() - first < 1500) {
								plus.runtime.quit();
							}
						}
					}, false);
				});
			}
		})
	});
});
</script>
`````

2. 个推iOS 和 Android 端的消息处理 （Dcloud 推送开发指南  http://ask.dcloud.net.cn/article/34）

	- 最近做的app中使用到了推送，iOS 和 Android 的处理方式不一样，Android 只需要把平台生成的 appid，appkey，appsecret 添加到 sdk 配置里面就可以直接推送消息， 而 iOS 只支持传透消息，iOS 还区分应用在线和不在线，两个系统的处理方式完全不同；   
		1. Android ：应用在前台会直接受到传透消息，然后自己去处理；
			如果后台不杀死push进程收到透传我去创建本地推送在通知栏显示plus.push.createMessage(str, jsonData, options)；杀死push进程什么也收不到了。当再次打开应用会收到之前的推送。(问了官方说android杀死应用就是收不到郁闷)；
		2. iOS ： 应用在前台会直接收到透传然后自己去处理；
			应用关闭和在后台就需要将消息推送个APNS然后APNS在推送给ios手机。(如果后台和关闭状态收不到推送就有可能是 ios的正式配置有问题 你需要一个ios的哥们帮你了)。
		3. android的push监听可以写很多都没事而ios你多写了它会多次调用push监听方法。

`````javascript
var Push = function() {}
/**
	*
	* 推送的监听
	*/
Push.prototype.pushListener = function () {
	var pointer = this;
	plus.push.addEventListener("click", function (msg) {
		switch (msg.payload) {
			case "LocalMSG":
				ApiConfig.staticToast("点击本地创建消息启动：");
				break;
			default:
				ApiConfig.staticToast("点击离线推送消息启动：");
				break;
		}
		if (msg.payload) {
			pointer.handle(msg);
		}
	}, false);
	plus.push.addEventListener("receive", function (msg) {
		if (msg.aps) { // Apple APNS message
			ApiConfig.staticToast("接收到在线APNS消息：");
		} else {
			ApiConfig.staticToast("接收到在线透传消息：");
		}
		if (plus.os.name == 'iOS') {
			if (msg.payload) {
				pointer.notificationMessage(msg);
			}
		} else {
			pointer.notificationMessage(msg);
		}
	}, false);
}

/**
* 	根据推送消息在通知栏中显现对应的提示
* 	@param {Object} msg
*/
Push.prototype.notificationMessage = function(msg) {
	ApiConfig.staticToast(msg, 1);
	ApiConfig.staticIsDebug("notificationMessage", msg, 1);
	var content = '';//你要展示的提示
	var pointer = this;
	var jsonData = '';
	switch(plus.os.name) {
		case "Android":
			jsonData = eval("(" + msg.payload + ")");
			break;
		case "iOS":
			jsonData = msg.payload;
			break;
	}
	pointer.createLocalPushMsg(msg, content);
}

/**
	*  处理透传消息
* @param {Object} msg 
* @param {Object} content
	*/
Push.prototype.createLocalPushMsg = function(msg, content) {
	//创建一个符合你自己要显示推送通知
		pointer.createMessage(str, jsonData, options);
}

/**
	*  创建本地推送消息
	* @param {Object} str
	* @param {Object} jsonData
	* @param {Object} options
	*/
Push.prototype.createMessage = function(str, jsonData, options) {
	switch(plus.os.name) {
		case "Android":
			jsonData = jsonData;
			break;
		case "iOS":
			jsonData = jsonData.eid;
			break;
	}
	plus.push.createMessage(str, jsonData, options);
}

/**
	* 处理通知方法
	* @param {Object} msg
	*/
Push.prototype.handle = function(msg) {
		//清除ios推送小红点
	this.cancelPushclear();
}
Push.prototype.cancelPushClear = function () {
	plus.push.clear();
}
`````

3. Dcloud 检测服务器版本升级

`````javascript
(function(w) {
	var server = "http://app23900.com/update/update.json", //获取升级描述文件服务器地址
		keyUpdate = "updateCheck", //取消升级键名
		curVersion = null;		   //APP当前的版本号

	/**
	* 检查升级数据
	*/
	function checkUpdateData(j) {
		var inf = j[plus.os.name];
		var srvVer = inf.version;	
		curVersion = plus.runtime.version;
		// 提示用户是否升级
		if(compareVersion(curVersion, srvVer)){
			plus.nativeUI.confirm(inf.note, function(i) {
				if(0 == i.index) {
					plus.runtime.openURL(inf.url);
				} else if(1 == i.index) {
					if(inf.isforce) {
						plus.runtime.quit();
					} else {
						plus.storage.setItem(keyUpdate, (new Date()).getTime().toString());
					}
				}
			}, inf.title, ["立即更新", "跳过此版本"]);
		}
	}

	/**
	* 从服务器获取升级数据
	*/
	function getUpdateData() {
		var xhr = new plus.net.XMLHttpRequest();
		xhr.onreadystatechange = function() {
			switch(xhr.readyState) {
				case 4:
					if(xhr.status == 200) {
						// 读取到文件之后对比APP版本号
						var data = JSON.parse(xhr.responseText);
						checkUpdateData(data);
					} else {
						console.log("获取升级数据，联网请求失败：" + xhr.status);
					}
					break;
				default:
					break;
			}
		}
		xhr.open("GET", server);
		xhr.send();
	}

	/**
		* 比较版本大小，如果新版本nv大于旧版本ov则返回true，否则返回false
		* @param {String} ov
		* @param {String} nv
		* @return {Boolean} 
		*/
	function compareVersion(ov, nv) {
		if(!ov || !nv || ov == "" || nv == "") {
			return false;
		}
		var b = false,
			ova = ov.split(".", 4),
			nva = nv.split(".", 4);
		for(var i = 0; i < ova.length && i < nva.length; i++) {
			var so = ova[i],
				no = parseInt(so),
				sn = nva[i],
				nn = parseInt(sn);
			if(nn > no || sn.length > so.length) {
				return true;
			} else if(nn < no) {
				return false;
			}
		}
		if(nva.length > ova.length && 0 == nv.indexOf(ov)) {
			return true;
		}
	}
	if(w.plus) {
		getUpdateData();
	} else {
		document.addEventListener("plusready", getUpdateData, false);
	}
})(window);
`````


4. 安卓手机的 back 键直接退出应用的问题

`````javascript
document.addEventListener('plusready', function() {
	var webview = plus.webview.currentWebview();
	plus.key.addEventListener('backbutton', function(e){
		webview.canback(function(e){
			if(e.canback) {
				webview.back();
			}else {
				// webview.close(); 
				// plus.runtime.quit();

				mui.plusReady(function(){
					// 首页返回键处理
					// 处理逻辑：1秒内，连续两次按返回键，则退出应用
					var first = null;
					plus.key.addEventListener('backbutton', function(){
						// 首次按键，提示 `再按一次退出应用`
						if(!first){
							first = new Date().getTime();
							mui.toast('再按一次退出应用');
							setTimeout(function(){
								first = null;
							}, 1000)
						}else {
							if(new Date().getTime() - first < 1500){
								plus.runtime.quit()
							}
						}
					}, false)
				})
			}
		})
	})
})
`````

5. 个推ios 和 Android 端的消息处理 （Dcloud 推送开发指南 http://ask.dcloud.net.cn/article/34）

> 最近做的app中使用到了推送，ios和 Android处理方式不一样 ， android 只需要把平台生成的 appid appkey app
