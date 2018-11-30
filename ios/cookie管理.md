# iOS cookie管理

## 添加cookie

简单起见，直接上代码：

``` objective-c
NSMutableDictionary *mailCookieProperties = [NSMutableDictionary dictionary];
// 设置cookie名称
[mailCookieProperties setValue:@"cookie_name" forKey:NSHTTPCookieName];
// 设置cookie值
[mailCookieProperties setValue:@"cookie_value" forKey:NSHTTPCookieValue];
// 设置domain
[mailCookieProperties setValue:@".xxx.xxx.com" forKey:NSHTTPCookieDomain];
// 设置cookiepath
[mailCookieProperties setValue:@"/" forKey:NSHTTPCookiePath];
// 设置过期时间为一天
[mailCookieProperties setObject:[[NSDate date] dateByAddingTimeInterval:24*60*60] forKey:NSHTTPCookieExpires];
[[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:[NSHTTPCookie cookieWithProperties:mailCookieProperties]];
```

## 删除cookie

``` objective-c
NSHTTPCookie *cookie;
NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
for (cookie in [storage cookies])
{
    [storage deleteCookie:cookie];
}

```
