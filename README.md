---
html:
    embed_local_images: false
    embed_svg: true
    offline: false
    toc: true
print_background: true
toc:
    depth_from: 1
    depth_to: 6
    ordered: false
---  
  
- [开发前必读](#开发前必读 )
  - [首页](#首页 )
  - [更新日志](#更新日志 )
  - [接口权限说明](#接口权限说明 )
  - [全局返回码说明](#全局返回码说明 )
  - [第三方应用配置信息说明](#第三方应用配置信息说明 )
        - [1. appid](#1-appid )
        - [2. appSecret](#2-appsecret )
        - [3. accessToken](#3-accesstoken )
        - [4. 白名单配置 ips](#4-白名单配置-ips )
        - [5. 回调域名配置：](#5-回调域名配置 )
- [开始开发](#开始开发 )
  - [接入指南](#接入指南 )
      - [接入统一认证平台开发，开发者需要按照如下步骤完成：](#接入统一认证平台开发开发者需要按照如下步骤完成 )
  - [接口域名说明](#接口域名说明 )
  - [获取基础 accessToken](#获取基础-accesstoken )
      - [1. 基础accessToken 说明](#1-基础accesstoken-说明 )
      - [2. 接口调用请求说明](#2-接口调用请求说明 )
- [网页授权开发：](#网页授权开发 )
    - [网页授权说明](#网页授权说明 )
    - [网页授权流程](#网页授权流程 )
      - [第一步：用户同意授权，获取code](#第一步用户同意授权获取code )
      - [第二步：通过code换取网页授权accessToken](#第二步通过code换取网页授权accesstoken )
      - [第三步 ：拉取用户信息](#第三步-拉取用户信息 )
- [接口文档 :](#接口文档 )
    - [接口地址  https://www.xxx.com](#接口地址-httpswwwxxxcom )
    - [用户管理](#用户管理 )
      - [获取用户基础信息](#获取用户基础信息 )
- [demo](#demo )
      - [图片](#图片 )
  
#  开发前必读
  
  
##  首页
  
  
 统一认证中心是第三方网站通过统一认证平台为用户提供身份认证和权限的服务的平台，而统一认证平台开发接口则是提供服务的基础，开发者在统一认证平台网站中创建应用、获取接口权限后，可以通过阅读本接口文档来帮助开发
 为了识别用户，每个用户针对每个应用会产生一个安全的OpenID
  
## 　更新日志
  
  
> 2020-08-12 初版完成，用户网页授权，及获取用户信息接口
  
##  接口权限说明
  
  
> 不同的应用拥有不同的接口权限,具体的权限请登录第三方应用后台查看
  
全局返回参数结构
  
```json
/*** 返回单体对象 ***/
{
	"code": 200,
	"msg": "操作成功",
	"data": {
		...
	},
}
/*** 返回列表对象 ***/
{
	"code": 200,
	"msg": "操作成功",
	"data": [{
		...
	}]
}
```
  
  
  
##  全局返回码说明
  
  
**第三方每次调用接口时，可能获得正确或错误的返回码，开发者可以根据返回码信息调试接口，排查错误。**
  
> **全局返回码说明如下：**
>
> 返回码                  说明
> ------
> 200                             操作成功
>
> 500                        操作失败，具体原因查看msg字段
>
> 99999                      网络异常
>
> 10000                      授权码code不存在
>
> 10001                      授权码code已失效
>
> 10002                      appid或者appSecert错误
>
> 10003                      accessToken生成失败
>
> 10004                      openid获取失败
>
> 10005                      授权码code不存在或已使用
>
> 10006                     token不存在
>
> 10007                      app应用不存在
>
> 10008                      token已失效
>
> 10009                      token非法
>
> 10010                      用户信息获取失败
>
> 10011                      用户已取消授权或授权已过期
>
> 10012                      未配置ip白名单
>
> 10013                      请求来源ip未在ip白名单中
>
> 10014                      授权回调地址与用户授权时不一致
  
  
> ------       
  
##  第三方应用配置信息说明
  
  
  关键字段信息说明
  
#####  1. appid 
  
   > 应用的唯一标识 
   > 申请第三方应用时由统一认证中心自动生成，
   > 长度不超过20个字符
  
#####  2. appSecret 
  
   > 应用密钥（用来获取接口调用的唯一凭证）
   > 1. 申请第三方应用时由统一认证中心自动生成
   > 2. 注意：由于统一认证平台的 **appSecret** 和获取到的 **accessToken** 安全级别都非常高，必须只保存在服务器，不允许传给客户端。后续刷新 -**accessToken** 、通过**accessToken** 获取用户信息等步骤，也必须从服务器发起，该服务器必须在白名单设置中。
   > 3. 长度不超过32个字符
  
#####  3. accessToken
  
  
   > **accessToken** 分为两种，网页授权 token 和 基础接口调用 token ，两者的区别在于：
   > 1. 网页授权接口token 第三方使用授权码调用接口获得（ 授权码是用户通过统一认证中心页授权成功后获得）
   > 2. 基础接口token 是使用 appid + appSecret 调用接口获得
   > 3. 长度不超过512个字符
   > 4. 有效期2小时，第三方应该做好有效期内的缓存，防止频繁调用，接口后面会次数限制，如已过期，第三方应用应该重新获取
  
#####  4. 白名单配置 ips
  
  
   > 1. 第三方调用接口的 ip 必须在白名单中
   >2. 在IP白名单内的IP来源， 调用接口才可调用成功。(例如[获取accessToken])
   > 3. 白名单设置必须为真实的ip，暂不支持通配符
   > 4. 该字段长度不超过500个字符
  
#####  5. 回调域名配置：
  
  
   > 用户在网页授权页同意授权给第三方应用时后，认证中心会将授权数据传给一个回调页面，回调页面需在此域名下，以确保安全可靠。
   >
   > 格式 为 www.baidu.com, 前面需要包含http(s) ，后面也不需要加URI
   >
   > 最多设置两个域名,两个加起来不超过500个字符
  
#  开始开发
  
  
##  接入指南
  
  
####   接入统一认证平台开发，开发者需要按照如下步骤完成：
  
  
> 1. 申请 appid 和 appSecret 
> 2. 设置接口调用IP白名单
> 3. 如果需要网页授权，还需设置网页授权回调域名
  
##  接口域名说明
  
  
> ad.xxx.com
  
##  获取基础 accessToken
  
  
####  1. 基础accessToken 说明
  
  
> accessToken是统一认证平台的全局唯一接口调用凭据，第三方调用各接口时都需使用accessToken。开发者需要进行妥善保存。accessToken的存储至少要保留512个字符空间。accessToken的有效期目前为2个小时，需定时刷新，重复获取将导致上次获取的accessToken失效。
>
> 统一认证平台的API调用所需的accessToken的使用及生成方式说明：
>
> 1、建议第三方开发者使用中控服务器统一获取和刷新accessToken，其他业务逻辑服务器所使用的accessToken均来自于该中控服务器，不应该各自去刷新，否则容易造成冲突，导致accessToken覆盖而影响业务；
>
> 2、目前accessToken的有效期通过返回的expireIn来传达，目前是7200秒之内的值。第三方服务器需要根据这个有效时间提前去刷新新accessToken
>
> 3、accessToken的有效时间可能会在未来有调整，需要提供被动刷新access_token的接口，这样便于业务服务器在API调用获知accessToken已超时的情况下，可以触发accessToken的刷新流程。
>
> **调用接口时，请登录“统一认证平台-第三方应用-基本配置”提前将服务器IP地址添加到IP白名单中，点击查看设置方法，否则将无法调用成功。**
  
####  2. 接口调用请求说明
  
  
> ## 应用通过 appid+appsecret换取网页授权token
>
>
> https请求方式: GET https://www.xxx.com/api/oauth2/token/getAccesToken?appid=APPID&appSecret=APPSECRET
>
> **请求参数**
>
> | 参数名称  | 参数说明     | 是否必须 | 数据类型 | schema |
> | --------- | ------------ | -------- | -------- | ------ |
> | appSecret | 应用密钥     | true     | string   |        |
> | appid     | 应用唯一标识 | true     | string   |        |
>
> **响应参数**
>
> | 参数名称 | 参数说明 | 类型               | schema             |
> | -------- | -------- | ------------------ | ------------------ |
> | code     | 消息码   | integer(int32)     | integer(int32)     |
> | data     | 数据对象 | 用户授权成功返回值 | 用户授权成功返回值 |
> | msg      | 消息内容 | string             |                    |
>
> 
>
> data属性说明**
>
> **用户授权成功返回值**
>
> | 参数名称    | 参数说明                                  | 类型           | schema |
> | ----------- | ----------------------------------------- | -------------- | ------ |
> | accessToken | 基础调用接口调用凭证                      | string         |        |
> | expiresIn   | accessToken接口调用凭证超时时间，单位(秒) | integer(int64) |        |
>
> **响应示例**
>
>
> ```json
> {
> 	"code": 200,
> 	"data": {
> 		"accessToken": "accessToken",
> 		"expiresIn": 7200
> 	},
> 	"msg": "操作成功"
> }
> ```
  
#  网页授权开发：
  
  
###  	网页授权说明
  
  
> 如果用户在浏览器中访问第三方网页，第三方应用可以通过统一认证平台网页授权机制，来获取用户基本信息，进而实现业务逻辑。
>
> 1、在第三方应用请求用户网页授权之前，开发者需要先到统一认证平台官网中的“xxx->xxx->xxx”的配置选项中，修改授权回调域名。请注意，这里填写的是域名（是一个字符串），而不是URL，因此请勿加 http(s):// 等协议头；
>
> 2、授权回调域名配置规范为全域名，比如需要网页授权的域名为：www.xuji.com，配置以后此域名下面的页面http://www.xuji.com/music.html 、 http://www.xuji.com/login.html 都可以进行OAuth2.0鉴权。但http://api.xuji.com 、 http://goods.xuji.com 、 http://xuji.com 无法进行OAuth2.0鉴权
>
> 3、如果公众号登录授权给了第三方开发者来进行管理，则不必做任何设置，由第三方代替公众号实现网页授权即可
>
> **关于网页授权accessToken和普通accessToken的区别**
>
> 1、统一认证平台网页授权是通过OAuth2.0机制实现的，在用户授权给第三方应用后，第三方应用可以获取到一个网页授权特有的接口调用凭证（网页授权accessToken），通过网页授权accessToken可以进行授权后接口调用，如获取用户基本信息；
>
> 2、其他微信接口，需要通过基础支持中的“获取accessToken”接口来获取到的普通accessToken调用。
  
###   网页授权流程
  
  
**具体而言，网页授权流程分为四步：**
  
1. 引导用户进入授权页面同意授权，获取code
2. 通过code换取网页授权accessToken（与基础支持中的accessToken不同）
3. 通过网页授权accessToken和openid获取用户基本信息
  
####  第一步：用户同意授权，获取code
  
  
> 授权页地址：
> 手机端 ： https://www.xxx.com/oauth/auth?appid=AAPPID&redirectUri=REDIRECT_URI&state=STATE
> PC端 ： https://www.xxx.com/pauth/auth?appid=AAPPID&redirectUri=REDIRECT_URI&state=STATE
  
   ​		参数说明
  
|    参数     | 是否必须 |                             说明                             |
| :---------: | :------: | :----------------------------------------------------------: |
|    appid    |    是    |                         应用唯一标识                         |
| redirectUri |    是    | 授权后重定向的回调链接地址， 请使用 urlEncode 对链接进行处理 |
|    state    |    否    | 重定向后会带上state参数，开发者可以填写a-zA-Z0-9的参数值，最多128字符 |
  
点击上面的连接会跳转到统一认证中心页
  
![统一认证页](images/oauth.png )
  
输入账号密码后如果有账号绑定了多用用户会出现如下页面
  
![选择用户页面](images/chooseUser.png )
  
选择用户后会跳回到用户填写的 redirect_uri 参数对应的地址上，并且带上code 和 STATE ，
  
`redirect_uri?code=CODE&state=STATE。`
  
> code说明 ： code作为换取access_token的票据，每次用户授权带上的code将不一样，code只能使用一次，10分钟未被使用自动过期
  
  
  
####  第二步：通过code换取网页授权accessToken
  
  
首先请注意，这里通过code换取的是一个特殊的网页授权accessToken,与基础支持中的accessToken（该accessToken用于调用其他接口）不同第三方应用可通过下述接口来获取网页授权accessToken。
  
尤其注意：由于第三方应用的appSecret和获取到的accessToken安全级别都非常高，必须只保存在服务器，不允许传给客户端。后续刷新access_token、通过accessToken获取用户信息等步骤，也必须从服务器发起。
  
请求方法
  
> 获取code后，请求以下链接获取accessToken： https://www.xxx.com/api/oauth2/token/getToken?appid=APPID&appSecret=SECRET&code=CODE
  
参数说明：
  
| 参数      | 是否必须 | 说明                     |
| --------- | -------- | ------------------------ |
| appid     | 是       | 应用唯一标识             |
| appSecret | 是       | 应用appSecert            |
| code      | 是       | 填写第一步获取的code参数 |
  
  返回说明
  
正常情况下返回json数据包如下 :
  
```json
/*****返回正确示例***/
{
	"code": 200,
	"data": {
		"accessToken": "ACCESS_TOKEN",
		"expiresIn": 7200,
		"openid": "OPENID"
	},
	"msg": "操作成功"
}
/******返回错误示例***/
{
  "code": 10001,
  "data": null,
  "msg": "授权码code已失效"
}
```
  
返回参数data字段说明：
  
| 参数        | 描述                                                         | 示例 |
| ----------- | ------------------------------------------------------------ | ---- |
| accessToken | 网页授权接口调用凭证,注意：此access_token与基础支持的access_token不同 | xxx  |
| expiresIn   | access_token接口调用凭证超时时间，单位（秒）                 | 7200 |
| openid      | 应用唯一标识，用户授权应用后产生的唯一用户标识               | xxx  |
  
####  第三步 ：拉取用户信息
  
  
则此时开发者可以通过accessToken和openid拉取用户信息了
  
请求方法：
  
> GET（请使用https协议） https://www.xxx.com/api/oauth2/user/userinfo?accessToken=ACCESS_TOKEN&openid=OPENID
  
**请求参数说明 ：**
  
| 参数        | 是否必须 | 描述                                                         |
| ----------- | -------- | ------------------------------------------------------------ |
| accessToken | 是       | 网页授权接口调用凭证,注意：此access_token与基础支持的access_token不同 |
| openid      | 是       | 用户的唯一标识                                               |
  
**响应参数说明**
  
| 参数名称 | 参数说明 | 类型           | schema         |
| -------- | -------- | -------------- | -------------- |
| code     | 消息码   | integer(int32) | integer(int32) |
| data     | 数据对象 | UserInfoByApp  | UserInfoByApp  |
| msg      | 消息内容 | string         |                |
  
  
  
**data 属性说明**
  
| 参数名称         | 参数说明     | 类型             | schema           |
| ---------------- | ------------ | ---------------- | ---------------- |
| identity         | 身份         | string           |                  |
| realName         | 姓名         | string           |                  |
| userExtendedInfo | 用户扩展信息 | UserExtendedInfo | UserExtendedInfo |
  
**UserExtendedInfo 属性说明**
  
| 参数名称      | 参数说明 | 类型   | schema |
| ------------- | -------- | ------ | ------ |
| blocDeptName  | 中心校   | string |        |
| classDeptName | 班级     | string |        |
| gradeDeptName | 年级     | string |        |
| scDeptName    | 区县     | string |        |
| snDeptName    | 学校名   | string |        |
| ssDeptName    | 学段     | string |        |
  
**响应示例**
  
  
```json
/*********返回正常结构*********/
{
	"code": 200,
	"data": {
		"identity": "老师",
		"realName": "张三",
		"userExtendedInfo": {
			"blocDeptName": "白云小学中心校",
			"classDeptName": "一班",
			"gradeDeptName": "一年级",
			"scDeptName": "椒江区",
			"snDeptName": "白云小学",
			"ssDeptName": "小学"
		}
	},
	"msg": "操作成功"
}
/*****返回错误结构**********/
{
  "code": 10006,
  "data": null,
  "msg": "token不存在"
}
  
```
  
  
  
  
  
------
  
  
  
#  接口文档 :
  
  
###  接口地址  https://www.xxx.com
  
  
> 该模块下的接口中的请求参数中的accessToken 指的是基础接口accessToken（通过appid+appSecret 获得）
  
###  用户管理
  
  
####  获取用户基础信息
  
  
>**接口地址** `/api/oauth2/user/info`
>
>**请求方式** `GET`
>
>**请求参数**
>
>| 参数名称    | 参数说明               | 请求类型 | 是否必须 | 数据类型 | schema |
>| ----------- | ---------------------- | -------- | -------- | -------- | ------ |
>| accessToken | 基础接口调用唯一凭证   | query    | true     | string   |        |
>| openid      | 用户在该应用的唯一标识 | query    | true     | string   |        |
>
>
>
>
>**响应参数**
>
>| 参数名称 | 参数说明 | 类型           | schema         |
>| -------- | -------- | -------------- | -------------- |
>| code     | 消息码   | integer(int32) | integer(int32) |
>| data     | 数据对象 | UserInfoByApp  | UserInfoByApp  |
>| msg      | 消息内容 | string         |                |
>
>**schema属性说明**
>
>**UserInfoByApp**
>
>| 参数名称         | 参数说明     | 类型             | schema           |
>| ---------------- | ------------ | ---------------- | ---------------- |
>| identity         | 身份         | string           |                  |
>| realName         | 姓名         | string           |                  |
>| userExtendedInfo | 用户扩展信息 | UserExtendedInfo | UserExtendedInfo |
>
>**UserExtendedInfo**
>
>| 参数名称      | 参数说明 | 类型   | schema |
>| ------------- | -------- | ------ | ------ |
>| blocDeptName  | 中心校   | string |        |
>| classDeptName | 班级     | string |        |
>| gradeDeptName | 年级     | string |        |
>| scDeptName    | 区县     | string |        |
>| snDeptName    | 学校名   | string |        |
>| ssDeptName    | 学段     | string |        |
>
>
>
>**响应示例**
>
>
>```json
>/****success example***/
>{
>	"code": 200,
>	"data": {
>		"identity": "",
>		"realName": "",
>		"userExtendedInfo": {
>			"blocDeptName": "",
>			"classDeptName": "",
>			"gradeDeptName": "",
>			"scDeptName": "",
>			"snDeptName": "",
>			"ssDeptName": ""
>		}
>	},
>	"msg": ""
>}
>/****error example***/
>{
>  "code": 10006,
>  "data": null,
>  "msg": "token不存在"
>}
>```
  
  
  
  
  
  
  
#  demo 
  
  
  
  
  
  
####  图片
  
  
  
  
