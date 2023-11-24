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
