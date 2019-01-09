# OAuth2.0授权（siwang.hu&nbsp;&nbsp;V1.0)  
> OAuth2.0中定义的授权方式  
> + 密码模式  
>  
> + 授权码模式  
>  
> + 简化模式  
>  
> + 客户端模式  
>  
> [参考资料](https://www.kancloud.cn/kancloud/oauth_2_0/63331)
## 协议参与者名称  
> + Resource Owner：资源所有者，实际用户(user)  
>  
> + client：客户端，用户使用的自家端软件  
>  
> + Authorization server：认证服务器，即服务提供商专门用来处理认证的服务器  
>  
> + User Agent：用户代理，第三方端软件  
>  
> + access_token：用户访问api权限token，拥有指定有效时间  
## 密码模式  
> + client如果是自家的应用，可以使用密码模式完成对client的授权  
>  
> + 如果不是自家的应用，这种模式不应该使用，因为第三方client可能会保存用户的账号与密码信息  
>  
> + 密码模式支持refresh token（在token快过期是刷新获取新的token）  
>  
> ![图片](./data/opwd.PNG)  
>  
> + 用户将自身的账号和密码交由client，client将使用它们来申请access_token，整个过程会将用户信息暴露,用户保证对客户端高度信任,客户端也不得储存密码  
>  
> **postman发送post请求**   
> ![图片](./data/ppwd.PNG)  
>  
> + 在postman中选择请求格式为post  
>  
> + 填写请求的服务器地址127.0.0.1:8088/uaa/oauth/token  
>  
> + 在Headers中填写Authorization=Basic Y2xpZW50OmNsaWVudA==字段（Basic后面有一个空格，然后在加上加密串）  
>  
> + Y2xpZW50OmNsaWVudA==值是**client_id:client_secret**的base64编码值  
>  
> + client_id与client_secret是auth服务器分配给客户端的client账号与secret密码  
>  
> ![图片](./data/ppwd1.PNG)  
>  
> + 在Body中填写grant_type为password，username为用户实际用户名，password为用户实际密码  
>  
> **实际http请求格式**  
```
POST /token HTTP/1.1
Host: 127.0.0.1:8088
Authorization: Basic Y2xpZW50OmNsaWVudA==
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=ws&password=ws
```
> **auth服务器返回access_token**  
```
{
  //授权的access_token
  "access_token": "5abcf659-6ec5-4177-bc23-96c42e28cb11", 
  "token_type": "bearer",
  "refresh_token": "ae0a997c-7422-4ab7-983d-69709b807775",
  "expires_in": 38952,
  "scope": "xx"
}
```
> **用户利用access_token访问服务器资源**  
> ![图片](./data/ppwd2.PNG)  
>  
## 授权码模式  
> + 授权码模式（authorization code）是功能最完整、流程最严密的授权模式  
>  
> + 授权码模式（authorization code）保证第三方应用无法获取到用户的账号与密码信息  
>  
> + 支持refresh_token  
> ![图片](./data/osp.PNG)  
### 步骤  
> + 1.用户访问第三方客户端，第三方客户端将用户导向认证服务器  
>  
> + 2.认证服务器要求用户选择是否给予这个客户端授权（通常要求输入账号与密码登录，不过这个登录的过程不是在第三方客户端实现的，而是在自己的认证服务器上实现的）  
>  
> + 3.用户给予授权，认证服务器将用户导向第三方客户端事先指定的重定向URI同时附上一个授权码  
>  
> + 4.第三方客户端收到授权码，附上早先的重定向URI，向认证服务器申请令牌  
>  
> + 5.证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌access_token和更新令牌refresh_token  
## 简化模式  
> + 这种模式比授权码模式少了授权码环节，回调url直接携带access_token  
>  
> + 不支持refresh_token  
>  
## 客户端模式  
> + 这种模式直接根据client的id和密钥即可获取token，无需用户参与  
>  
> + 这种模式比较合适消费api的后端服务  
>  
> + 不支持refresh token  
>  
> **postman发送post请求**  
> ![图片](./data/cpp.PNG)  
>  
> + 在postman中选择请求格式为post  
>  
> + 填写请求的服务器地址127.0.0.1:8088/uaa/oauth/token  
>  
> + 在Headers中填写Authorization=Basic cmVnaXN0ZXI6cmVnaXN0ZXI=字段（Basic后面有一个空格，然后在加上加密串）  
>  
> + cmVnaXN0ZXI6cmVnaXN0ZXI=值是**client_id:client_secret**的base64编码值  
>  
> + client_id与client_secret是auth服务器分配给客户端的client账号与secret密码  
>  
> ![图片](./data/cpp1.PNG)  
>  
> + 在Body中填写grant_type为client_credentials，无需用户填写账号和密码
>  
> **auth服务器返回access_token** 
```
{
  "access_token": "fae28c9a-3e5f-4904-b534-89100152a288",
  "token_type": "bearer",
  "expires_in": 43094,
  "scope": "xx"
}
```