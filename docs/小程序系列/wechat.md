### 使用 miniprogram-ci 结合云效流水线 进行小程序代码的上传、预览等操作。

- 自动化配置

```js
  const ci = require('miniprogram-ci');
  const path = require('path');
  //解析命令行参数
  function parse(args = []) {
  let news = [];
  args.forEach((item) => {
  let h = item.substring(2).split('=');
  news.push({ name: h[0], value: h[1] });
  });
  const obj = Object.fromEntries(news.map(item => [item.name, item]));
  return obj;
  }
  const arg = parse(process.argv.slice(2));

(async () => {
const project = new ci.Project({
appid: 'wx133c73b7c9deb596',//appid
type: 'miniProgram',
projectPath: path.resolve(**dirname, '../dist/build/mp-weixin'), //项目路径
privateKeyPath: path.resolve(**dirname, './private.wx.key'), //小程序后台的上传密匙
ignores: ['node_modules/**/*'],
})

    const uploadResult = await ci.upload({
        project,
        version: `${arg.version.value}`,
        desc: `${arg.desc.value}`,
        setting: {
            es6: true,
        },
        onProgressUpdate: console.log,
    }).then(res => {
        console.log(arg.version.value)
    }).catch(error => {
        throw error
    })
    console.log(uploadResult);

})()
```

- 在 package.json 文件中配置命令
  "upload": "node ./deploy/upload.js",

### 获取小程序当前所属环境

```js
Object wx.getAccountInfoSync()// 获取当前帐号信息
const accountInfo = uni.getAccountInfoSync();
// env 类型
export const env = accountInfo.miniProgram.envVersion;
console.log(env);
if(!env){
console.error("获取运行环境失败!");
}

const baseApi = {
// 开发版
develop: process.env.VUE_APP_BASEURL_DEV,
// 体验版
trial: process.env.VUE_APP_BASEURL_PREV,
// 正式版
release: process.env.VUE_APP_BASEURL_PROD
};

// request 请求 baseURL
export const baseURL = baseApi[env];s
```

### 生命周期，分别有什么作用

- 应用生命周期

  1. onLanch: 小程序初始化完成时触发，全局只触发一次
  2. onShow: 小程序启动，或从后台进入前台时触发
  3. onHide: 小程序从前台进入后台时触发（如底部 tab 栏切换到其他页面）
  4. onEerror：小程序发生错误或者 API 调用报错时触发
  5. onPageNotFound: 小程序要打开的页面不存在时触发
  6. onUnhandleRejection：小程序有未处理的 Promise 拒绝时触
  7. onThemeChange：系统切换主题时触发

- 页面生命周期

  1. onLoad: 监听页面加载（只会调用一次，可获取当前页面路径的参数，相当于 view 中的 created）
  2. onShow: 监听页面显示（发送请求获取数据）
  3. onReady：监听页面初次渲染完成（获取页面元素，相当于 view 中的 mounted
  4. onHide：监听页面隐藏（如底部 tab 栏切换到其他页面或小程序切入后台等触发）
  5. onUnload：监听页面卸载（如 redirectTo 或 navigateBack 到其他页面时触发）

- 组件生命周期
  1. created：监听页面加载
  2. attached：监听页面显示（这个生命周期用的多，绝大多数初始化工作可以在这里进行）
  3. ready：监听页面初次渲染完成
  4. moved：监听页面隐藏
  5. detached：监听页面卸载
  6. error：每当组件方法抛出错误时间

### 路由跳转以及传递参数

1. wx.navigateTo()
   保留当前页面，跳转到应用内的某个页面。但是不能跳到 tabbar 页面
2. wx.redirectTo()：
   关闭当前页面，跳转到应用内的某个页面。但是不允许跳转到 tabbar 页面
3. wx.switchTab()：
   跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面(不可以携带参数)
4. wx.navigateBack()
   关闭当前页面，返回上一页面或多级页面。可通过 getCurrentPages() 获取当前的页面栈，决定需要返回几层
5. wx.reLaunch()：
   关闭所有页面，打开到应用内的某个页面

**传参** 一种是通过 navigator 组件的 url 完成，另一种是使用 wx.navigateTo()跳转

### 小程序的登陆流程，如何获取手机号

1. wx.login() 获取 code,
2. 步骤 1 拿到的 code 发送给服务端换取唯一用户标识 openid
3. 调 getPhoneNumber() 获取 phoneCode
4. 步骤 3 获取的 phoneCode 和步骤 2 拿到的 openId 一起传给服务端获取手机号
5. 完成登录

### 如果版本更新了这么通知用户

1. 在 APP() 中 监听 检查小程序是否有新版本发布
   updateManager.onCheckForUpdate
2. 有，则使用 Modal 提示用户更新
   updateManager.onUpdateReady
3. 点击更新按钮，下载新版本
   updateManager.onUpdateDownload

```js
App({
  onLaunch: function (options) {
    this.autoUpdate();
  },
  autoUpdate: function () {
    var self = this;
    // 获取小程序更新机制兼容
    if (wx.canIUse('getUpdateManager')) {
      const updateManager = wx.getUpdateManager();
      //1. 检查小程序是否有新版本发布
      updateManager.onCheckForUpdate(function (res) {
        // 请求完新版本信息的回调
        if (res.hasUpdate) {
          //检测到新版本，需要更新，给出提示
          wx.showModal({
            title: '更新提示',
            content: '检测到新版本，是否下载新版本并重启小程序？',
            success: function (res) {
              if (res.confirm) {
                //2. 用户确定下载更新小程序，小程序下载及更新静默进行
                self.downLoadAndUpdate(updateManager);
              } else if (res.cancel) {
                //用户点击取消按钮的处理，如果需要强制更新，则给出二次弹窗，如果不需要，则这里的代码都可以删掉了
                wx.showModal({
                  title: '温馨提示~',
                  content:
                    '本次版本更新涉及到新的功能添加，旧版本无法正常访问的哦~',
                  showCancel: false, //隐藏取消按钮
                  confirmText: '确定更新', //只保留确定更新按钮
                  success: function (res) {
                    if (res.confirm) {
                      //下载新版本，并重新应用
                      self.downLoadAndUpdate(updateManager);
                    }
                  },
                });
              }
            },
          });
        }
      });
    } else {
      // 如果希望用户在最新版本的客户端上体验您的小程序，可以这样子提示
      wx.showModal({
        title: '提示',
        content:
          '当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。',
      });
    }
  },
  /**
   * 下载小程序新版本并重启应用
   */
  downLoadAndUpdate: function (updateManager) {
    var self = this;
    wx.showLoading();
    //静默下载更新小程序新版本
    updateManager.onUpdateReady(function () {
      wx.hideLoading();
      //新的版本已经下载好，调用 applyUpdate 应用新版本并重启
      updateManager.applyUpdate();
    });
    updateManager.onUpdateFailed(function () {
      // 新的版本下载失败
      wx.showModal({
        title: '已经有新版本了哟~',
        content: '新版本已经上线啦~，请您删除当前小程序，重新搜索打开哟~',
      });
    });
  },
});
```

### 小程序嵌入 H5 页面这么做

使用 web-view 组件

### unniapp 开发遇到的难点

1. tabbar 导航栏问题，某些需求需要自定义 tabbar 导航栏
2. 开发小程序和 App 的话是不支持\*选择器，body 改为 page，div，改为 view，span 改为 text 等

### 小程序跳转公众号

方法 1: 直接放个公众号二维码, 在小程序里长按识别
方法 2: 公众号组件<official-account></official-account> 跳转 需前往小程序后台，在“设置”->“关注公众号”中设置要展示的公众号
方法 3: webview 内嵌的公众号链接

### 小程序包大小优化

1. 分包加载
2. 资源 CDN 化
3. JS 和 CSS 压缩
4. 分包异步化
5. 数据外置
