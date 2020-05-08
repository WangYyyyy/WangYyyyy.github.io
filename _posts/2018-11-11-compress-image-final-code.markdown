---
layout: post
title:  "iOS图片压缩终极方案"
date:   2018-11-11 16:46:34 +0800
categories: iOS
tags:
  - iOS
  - OC
---
压缩图片一般就是两个思路 `压缩质量` 和 `压缩尺寸` 

压缩质量的核心代码
```
NSData *data = UIImageJPEGRepresentation(image, compression);
UIImage *resultImage = [UIImage imageWithData:data];
```
压缩尺寸的核心代码
```
UIGraphicsBeginImageContext(size);
[image drawInRect:CGRectMake(0, 0, size.width, size.height)];
resultImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
```

我们在实际业务场景中首先需要指定图片最终大小上限, 然后基于这两种方式对图片进行一个最理想化的压缩.
**核心代码**

```
- (NSData *)compressWithMaxLength:(NSUInteger)maxLength {
    // Compress by quality
    CGFloat compression = 1;
    NSData *data = UIImageJPEGRepresentation(self, compression);
    //NSLog(@"Before compressing quality, image size = %ld KB",data.length/1024);
    if (data.length < maxLength) return data;
    
    CGFloat max = 1;
    CGFloat min = 0;
    for (int i = 0; i < 6; ++i) {
        compression = (max + min) / 2;
        data = UIImageJPEGRepresentation(self, compression);
        //NSLog(@"Compression = %.1f", compression);
        //NSLog(@"In compressing quality loop, image size = %ld KB", data.length / 1024);
        if (data.length < maxLength * 0.9) {
            min = compression;
        } else if (data.length > maxLength) {
            max = compression;
        } else {
            break;
        }
    }
    //NSLog(@"After compressing quality, image size = %ld KB", data.length / 1024);
    if (data.length < maxLength) return data;
    UIImage *resultImage = [UIImage imageWithData:data];
    // Compress by size
    NSUInteger lastDataLength = 0;
    while (data.length > maxLength && data.length != lastDataLength) {
        lastDataLength = data.length;
        CGFloat ratio = (CGFloat)maxLength / data.length;
        //NSLog(@"Ratio = %.1f", ratio);
        CGSize size = CGSizeMake((NSUInteger)(resultImage.size.width * sqrtf(ratio)),
                                 (NSUInteger)(resultImage.size.height * sqrtf(ratio))); // Use NSUInteger to prevent white blank
        UIGraphicsBeginImageContext(size);
        [resultImage drawInRect:CGRectMake(0, 0, size.width, size.height)];
        resultImage = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();
        data = UIImageJPEGRepresentation(resultImage, compression);
        //NSLog(@"In compressing size loop, image size = %ld KB", data.length / 1024);
    }
    //NSLog(@"After compressing size loop, image size = %ld KB", data.length / 1024);
    return data;
}
```