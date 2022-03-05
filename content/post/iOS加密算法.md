---
title:       "iOS加密算法(DES、AES、MD5、SHA512、Base64)"
subtitle:    ""
description: ""
date:        2014-10-28
author:      "xtcel"
image:       ""
tags:        ["iOS", "加密"]
categories:  ["iOS" ]
---

在iOS的项目中，我们经常会用到加密技术，比如说在登录的时候，我们会先把密码用MD5加密再传输给服务器或者直接对所有的参数进行加密再Post到服务器。

### 常用加密算法：

1. DES:Data Encryption Standard，即数据加密算法，它是IBM公司于1975年研究成功并公开发表的。
2. AES:高级加密标准，这个标准用来替代原先的DES，已经被多方分析且广为全世界所使用。高级加密标准已然成为对称密钥加密中最流行的算法之一。
3. MD5:Message-Digest Algorithm 5（信息-摘要算法5），用于确保信息传输完整一致。是计算机广泛使用的杂凑算法之一（又译摘要算法、哈希算法），主流编程语言普遍已有MD5实现。这个应该是听到最多的算法，据说是已经被破解了。但是我觉得破解这个应该也要很久吧！
4. Base64:Base64是网络上最常见的用于传输8Bit字节代码的编码方式之一。Base64编码可用于在HTTP环境下传递较长的标识信息。例如，在Java Persistence系统Hibernate中，就采用了Base64来将一个较长的唯一 标识符（一般为128-bit的UUID）编码为一个字符串，用作HTTP表单和HTTP GET URL中的参数。在其应用程序中，也常常需要把二进制数据编码为适合放在URL（包括隐藏表单域）中的形式。此时，采用Base64编码具有不可读性，即所编码的数据不会被人用肉眼所直接看到。

### 算法实现

介绍完这些算法，接下来我们就来看看它们再iOS下面的具体实现吧！  
我再GitHub上做了一个小Demo，可以直接下载，放到项目中直接使用  
链接地址:[GitHub - yongca887/XTSecurity](https://github.com/yongca887/XTSecurity/)

### 一、DES加密算法

废话不多说了，直接上代码：

```object-c
+ (NSString *)encrypt:(NSString *)sText encryptOrDecrypt:(CCOperation)encryptOperation key:(NSString *)key
{
const void *dataIn;
size_t dataInLength;
if (encryptOperation == kCCDecrypt)//传递过来的是decrypt 解码
{
//解码 base64
NSData *decryptData = [GTMBase64 decodeData:[sText dataUsingEncoding:NSUTF8StringEncoding]];//转成utf-8并decode
dataInLength = [decryptData length];
dataIn = [decryptData bytes];
}
else //encrypt
{
NSData* encryptData = [sText dataUsingEncoding:NSUTF8StringEncoding];
dataInLength = [encryptData length];
dataIn = (const void *)[encryptData bytes];
}
/*
DES加密 ：用CCCrypt函数加密一下，然后用base64编码下，传过去
DES解密 ：把收到的数据根据base64，decode一下，然后再用CCCrypt函数解密，得到原本的数据
*/
CCCryptorStatus ccStatus;
uint8_t *dataOut = NULL; //可以理解位type/typedef 的缩写（有效的维护了代码，比如：一个人用int，一个人用long。最好用typedef来定义）
size_t dataOutAvailable = 0; //size_t 是操作符sizeof返回的结果类型
size_t dataOutMoved = 0;
dataOutAvailable = (dataInLength + kCCBlockSizeDES) & ~(kCCBlockSizeDES – 1);
dataOut = malloc( dataOutAvailable * sizeof(uint8_t));
memset((void *)dataOut, 0x0, dataOutAvailable);//将已开辟内存空间buffer的首 1 个字节的值设为值 0
NSString *initIv = @”12345678″;
const void *vkey = (const void *) [key UTF8String];
const void *iv = (const void *) [initIv UTF8String];
//CCCrypt函数 加密/解密
ccStatus = CCCrypt(encryptOperation,// 加密/解密
kCCAlgorithmDES,// 加密根据哪个标准（des，3des，aes。。。。）
kCCOptionPKCS7Padding,// 选项分组密码算法(des:对每块分组加一次密 3DES：对每块分组加三个不同的密)
vkey, //密钥 加密和解密的密钥必须一致
kCCKeySizeDES,// DES 密钥的大小（kCCKeySizeDES=8）
iv, // 可选的初始矢量
dataIn, // 数据的存储单元
dataInLength,// 数据的大小
(void *)dataOut,// 用于返回数据
dataOutAvailable,
&dataOutMoved);
NSString *result = nil;
if (encryptOperation == kCCDecrypt)//encryptOperation==1 解码
{
//得到解密出来的data数据，改变为utf-8的字符串
result = [[NSString alloc] initWithData:[NSData dataWithBytes:(const void *)dataOut length:(NSUInteger)dataOutMoved] encoding:NSUTF8StringEncoding];
}
else //encryptOperation==0 （加密过程中，把加好密的数据转成base64的）
{
//编码 base64
NSData *data = [NSData dataWithBytes:(const void *)dataOut length:(NSUInteger)dataOutMoved];
result = [GTMBase64 stringByEncodingData:data];
}
return result;
}
```

二、AES加密算法

```object-c
+(NSData *)AES256EncryptWithKey:(NSString *)key {//加密
char keyPtr[kCCKeySizeAES256+1];
bzero(keyPtr, sizeof(keyPtr));
[key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
NSUInteger dataLength = [self length];
size_t bufferSize = dataLength + kCCBlockSizeAES128;
void *buffer = malloc(bufferSize);
size_t numBytesEncrypted = 0;
CCCryptorStatus cryptStatus = CCCrypt(kCCEncrypt, kCCAlgorithmAES128,
kCCOptionPKCS7Padding | kCCOptionECBMode,
keyPtr, kCCBlockSizeAES128,
NULL,
[self bytes], dataLength,
buffer, bufferSize,
&numBytesEncrypted);
if (cryptStatus == kCCSuccess) {
return [NSData dataWithBytesNoCopy:buffer length:numBytesEncrypted];
}
free(buffer);
return nil;
}
– (NSData *)AES256DecryptWithKey:(NSString *)key {//解密
char keyPtr[kCCKeySizeAES256+1];
bzero(keyPtr, sizeof(keyPtr));
[key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
NSUInteger dataLength = [self length];
size_t bufferSize = dataLength + kCCBlockSizeAES128;
void *buffer = malloc(bufferSize);
size_t numBytesDecrypted = 0;
CCCryptorStatus cryptStatus = CCCrypt(kCCDecrypt, kCCAlgorithmAES128,
kCCOptionPKCS7Padding | kCCOptionECBMode,
keyPtr, kCCBlockSizeAES128,
NULL,
[self bytes], dataLength,
buffer, bufferSize,
&numBytesDecrypted);
if (cryptStatus == kCCSuccess) {
return [NSData dataWithBytesNoCopy:buffer length:numBytesDecrypted];
}
free(buffer);
return nil;
}
```

三、MD5数字摘要。

```object-c
#pragma mark – MD5 加密
//String转化为md5加密String
+ (NSString *)md5:(NSString *)str
{
const char *cStr = [str UTF8String];
unsigned char result[16];
CC_MD5(cStr, strlen(cStr), result); // This is the md5 call
return [NSString stringWithFormat:
@”%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x”,
result[0], result[1], result[2], result[3],
result[4], result[5], result[6], result[7],
result[8], result[9], result[10], result[11],
result[12], result[13], result[14], result[15]
];
}
```

四、SHA512算法

```object-c
//SHA512加密
+ (NSString) getSHA512String:(NSString)s
{
const char cstr = [s cStringUsingEncoding:NSUTF8StringEncoding];
NSData *data = [NSData dataWithBytes:cstr length:s.length];
uint8_t digest[CC_SHA512_DIGEST_LENGTH];
CC_SHA512(data.bytes, data.length, digest);
NSMutableString output = [NSMutableString stringWithCapacity:CC_SHA512_DIGEST_LENGTH * 2];
for(int i = 0; i < CC_SHA512_DIGEST_LENGTH; i++)
[output appendFormat:@”%02x”, digest[i]];
return output;
}
```

五、Base64编码
首先下载GTMBase64文件，在工程中加入三个文件
GTMDefines.h
GTMBase64.h
GTMBase64.m
你可以在这里找到这三个文件
[GTMBase64](http://code.google.com/p/google-toolbox-for-mac/source/browse/trunk/Foundation/?r=87),
你也可以在下面的demo里面找到这3个文件，demo会完整实现文章里面常用的3种编码方法。
我在此稍微封装一下：
.h文件

```object-c
#pragma mark – base64
+ (NSString)encodeBase64String:(NSString *)input;
+ (NSString)decodeBase64String:(NSString )input;
+ (NSString)encodeBase64Data:(NSData )data;
+ (NSString)decodeBase64Data:(NSData )data;
.m文件
#pragma mark – base64
+ (NSString)encodeBase64String:(NSString * )input {
NSData data = [input dataUsingEncoding:NSUTF8StringEncoding allowLossyConversion:YES];
data = [GTMBase64 encodeData:data];
NSString *base64String = [[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease];
return base64String;
}
+ (NSString)decodeBase64String:(NSString * )input {
NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding allowLossyConversion:YES];
data = [GTMBase64 decodeData:data];
NSString *base64String = [[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease];
return base64String;
}
```
