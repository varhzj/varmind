# android修改hosts方法

``` shell
adb devices -l # make sure your gadget is listed
adb shell # run a shell there
su # become the root (don't miss confirmation request!)
mount -o remount,rw /system # allow to write
vi /system/etc/hosts ## edit the file in place - do what you whant, then <ESC>:wq ##
mount -o remount,ro /system # get things back to normal
exit # unroot
nslookup YourBlockedAdSite.Net # check if it works
exit # good bye
```
