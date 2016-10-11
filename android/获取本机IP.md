### android 获取本机IP

感觉用途不大，产品需要，写出来玩玩

``` java

private String getLocalIP() {
    try {
        Enumeration<NetworkInterface> interfaces = NetworkInterface.getNetworkInterfaces();
        while (interfaces.hasMoreElements()) {
            NetworkInterface intf = interfaces.nextElement();
            Enumeration<InetAddress> inaddrs = intf.getInetAddresses();
            while (inaddrs.hasMoreElements()) {
                InetAddress inaddr = inaddrs.nextElement();
                // 目前只获取ipv4地址
                if (!inaddr.isLoopbackAddress() && inaddr instanceof Inet4Address) {
                    return inaddr.getHostAddress();
                }
            }
        }
    } catch (SocketException e) {
        Log.e(TAG, "getLocalIP error", e);
    }

    return null;
}

```
