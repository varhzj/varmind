### android 生成设备唯一ID

说明：生成方法主要依靠本机imei、imsi、mac在本地生成，由于存在获取三者信息都失败的情况，这时将采用生成的UUID取MD5作为设备ID，可能会产生两次生成不一致情况，目前较为靠谱的应该是收集本机信息如imei、imsi、mac包括设备分辨率等信息，交由服务端生成并返回到本机，当设备卸载app重新安装时依然能通过这些信息从服务端获取到相同ID（同样可能存在前后不一致情况）。条件限制，没有服务端支持，目前就直接从本地生成。具体代码如下，仅供参考。

``` java

private String getDeviceId() {
    String gid = "";
    String infos = "";
    TelephonyManager telephonyManager = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
    String imei = telephonyManager.getDeviceId();
    String imsi = telephonyManager.getSubscriberId();
    String mac = getMacAddr();
    // android M后以下获取mac地址方法失效
    // WifiManager wifiManager = (WifiManager) cordova.getActivity().getSystemService(Context.WIFI_SERVICE);
    // String mac = wifiManager.getConnectionInfo().getMacAddress();
    UUID uuid = null;
    if (isEmpty(imei)) {
        imei = "";
    }
    if (isEmpty(imsi)) {
        imsi = "";
    }
    if (isEmpty(mac)) {
        mac = "";
    }
    infos = imei + imsi + mac;
    if (isEmpty(infos)) {
        return getMD5(UUID.randomUUID().toString());
    }

    return getMD5(infos);
}

private String getMacAddr() {
    try {
        Enumeration<NetworkInterface> interfaces = NetworkInterface.getNetworkInterfaces();
        while (interfaces.hasMoreElements()) {
            NetworkInterface intf = interfaces.nextElement();
            if (intf.getName().equalsIgnoreCase("wlan0")) {
                byte[] macBytes = intf.getHardwareAddress();
                if (macBytes == null) {
                    return null;
                }

                StringBuilder res = new StringBuilder();
                for (byte b : macBytes) {
                    res.append(String.format("%02X:", b));
                }

                if (res.length() > 0) {
                    res = res.deleteCharAt(res.length() - 1);
                }
                return res.toString();
            }
        }
    } catch (SocketException e) {
        Log.e(TAG, "getMacAddr error", e);
    }

    return null;
}

private boolean isEmpty(String s) {
    return s == null || "".equals(s.trim())
            || s.trim().length() == 0 || "null".equals(s.trim());
}

private String getMD5(String s) {
    try {
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] bytes = md.digest(s.getBytes("GBK"));
        return bytesToHex(bytes);
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    return null;
}

private String bytesToHex(byte[] in) {
    StringBuilder res = new StringBuilder();
    for (byte b : in) {
        res.append(String.format("%02X", b));
    }
    return res.toString();
}


```
