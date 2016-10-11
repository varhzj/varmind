### Crosswalk cookie管理

``` java

XWalkCookieManager mCookieManager = new XWalkCookieManager();
mCookieManager.setAcceptCookie(true);
mCookieManager.setAcceptFileSchemeCookies(true);
mCookieManager.setCookie("http://xxx.xxx.com/", "cookie_name=" + cookie_value + " ; Domain=.xxx.xxx.com; HttpOnly");

```
