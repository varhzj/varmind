### ios 生成设备唯一编号

官方比较推荐使用广告ID作为设备的唯一ID，但是存在系统更新或用户操作后ID变更信息，所以使用UUID＋keychain进行设备唯一ID生成和保存，尽可能保持一致性，惯例，直接上代码。


``` objective-c

#import "KeychainItemWrapper.h"

- (NSString*)getDeviceID
{
    KeychainItemWrapper *keychain = [[KeychainItemWrapper alloc] initWithIdentifier:@"what_ever_unique" accessGroup:nil];
    NSString *currentDeviceID = [keychain objectForKey:(__bridge id)(kSecValueData)];
    if ([currentDeviceID isEqualToString:@""]) {
        // 这里选择现将生成的ID加入到keychain然后再取出
        [keychain setObject:[self generateUUID] forKey:(__bridge id)(kSecValueData)];
        currentDeviceID = [keychain objectForKey:(__bridge id)(kSecValueData)];
    }
    // NSLog(@"currentDeviceID%@", currentDeviceID);
    return currentDeviceID;
}

- (NSString*) generateUUID
{
    if(NSClassFromString(@"NSUUID")) {
        // only available in iOS >= 6.0
        return [[NSUUID UUID] UUIDString];
    }
    // 实际上目前只需要使用上面的NSUUID，保险起见，添加cfuuid生成
    CFUUIDRef uuidRef = CFUUIDCreate(kCFAllocatorDefault);
    CFStringRef cfuuid = CFUUIDCreateString(kCFAllocatorDefault, uuidRef);
    CFRelease(uuidRef);
    NSString *uuid = [((__bridge NSString *) cfuuid) copy];
    CFRelease(cfuuid);
    return uuid;
}

```


注：代码中使用到了KeychainItemWrapper.h和KeychainItemWrapper.m文件，可自行下载导入工程，可能需要修改KeychainItemWrapper.m文件的Compiler Flags值为 -fno-objc-arc
