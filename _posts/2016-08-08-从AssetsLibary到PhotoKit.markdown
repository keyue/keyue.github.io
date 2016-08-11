---
layout: post
title: 从AssetsLibary到PhotoKit
date: 2016-08-08 20:18:04.000000000 +09:00
---

#### 从AssetsLibary到PhotoKit

> 由于项目需要支持iOS7及以上,所以在照片框架选择的时候,使用了Assets Library Framework,使用ALAsset方式实现照片浏览器。之前在自己6 plus上使用一直正常，直到天用了同事的6s进行测试，发现在6s上相册缩略图都模糊了，经过调试发现，在iOS8下，asset.thumbnail返回照片的size是150\*150，而在iOS9+，返回照片的size变成了75*75，而这个在4s\5\5c\5s\6\6p下都没问题照片还算清晰，但是到了6s下，由于分辨率问题，变得很模糊。测试了一下，目前的主流App还使用Assets Library Framework框架的也都存在这个问题。

> 这里刚好也有博主遇到并附了解决方案 [ios9下照片浏览模糊的解决方法(ALAsset.thumbnail模糊)](http://blog.csdn.net/cuibo1123/article/details/49077923)

> 项目中还有需要保持历史记录图片的功能
首先想到，保存到相册的方法，无法返回图片保存后的文件路径

```
UIImageWriteToSavedPhotosAlbum(UIImage *image, __nullable id completionTarget, __nullable SEL completionSelector, void * __nullable contextInfo) 
```

然后使用下边代码获取图片URL [stack overflow](http://stackoverflow.com/questions/4457904/iphone-how-do-i-get-the-file-path-of-an-image-saved-with-uiimagewritetosavedpho)

```
ALAssetsLibraryWriteImageCompletionBlock completeBlock = ^(NSURL *assetURL, NSError *error){
        if (!error) {  
            #pragma mark get image url from camera capture.
            imageURL = [NSString stringWithFormat:@"%@",assetURL];
        }  
    }; 

// 根据URL获取图片
NSData *data = [NSData dataWithContentsOfURL:url];
imageView.image = [UIImage imageWithData:data];
```
> 这在模拟器上是可以的，但到了真机上却读取不到图片资源，查了一下
![image](http://o9lammkmn.bkt.clouddn.com/IMG_201608080001.png)
	> file:///Users/mingwei/Library/Developer/CoreSimulator/Devices/4B0A6E97-303A-4755-B284-7C308570890D/data/Media/DCIM/100APPLE/IMG_0033.JPG
	> /private/var/mobile/Media/DCIM/100APPLE/IMG_0033.JPG

决定使用PhotoKit。

#### 授权相关

	相册授权

	```
	NSString *tipTextWhenNoPhotosAuthorization;
    
    PHAuthorizationStatus authorizationStatus = [PHPhotoLibrary authorizationStatus];
    
    if (authorizationStatus == PHAuthorizationStatusRestricted || authorizationStatus == PHAuthorizationStatusDenied) {
        NSDictionary *mainInfoDictionary = [[NSBundle mainBundle] infoDictionary];
        NSString *appName = [mainInfoDictionary objectForKey:@"CFBundleDisplayName"];
        tipTextWhenNoPhotosAuthorization = [NSString stringWithFormat:@"请前往\"设置-隐私-照片\"选项中，允许 %@ 照片", appName];
        
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"提示"
                                                                       message:tipTextWhenNoPhotosAuthorization
                                                                preferredStyle:UIAlertControllerStyleAlert];
        
        UIAlertAction *defaultAction = [UIAlertAction actionWithTitle:@"知道了"
                                                                style:UIAlertActionStyleDefault
                                                              handler:^(UIAlertAction * action) {}];
        [alert addAction:defaultAction];
        [self presentViewController:alert animated:YES completion:nil];
    }else if (authorizationStatus == PHAuthorizationStatusNotDetermined) {
        __weak typeof(self) weakSelf = self;
        [PHPhotoLibrary requestAuthorization:^(PHAuthorizationStatus status) {
            if (status == PHAuthorizationStatusAuthorized) {
                dispatch_async(dispatch_get_main_queue(), ^{
                    // TODO:
                });
            }
        }];
    }else if (authorizationStatus == PHAuthorizationStatusAuthorized) {
        // TODO:
    } 
	```
	
	// 相机授权
	```
		NSString *tipTextWhenNoPhotosAuthorization;
        AVAuthorizationStatus cameraAuthStatus = [AVCaptureDevice authorizationStatusForMediaType:AVMediaTypeVideo];
        if (cameraAuthStatus == AVAuthorizationStatusNotDetermined || cameraAuthStatus == AVAuthorizationStatusRestricted || cameraAuthStatus == AVAuthorizationStatusDenied) {
            NSDictionary *mainInfoDictionary = [[NSBundle mainBundle] infoDictionary];
            NSString *appName = [mainInfoDictionary objectForKey:@"CFBundleDisplayName"];
            tipTextWhenNoPhotosAuthorization = [NSString stringWithFormat:@"请前往\"设置-隐私-相机\"选项中，允许 %@ 访问相机", appName];
            
            UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"提示"
                                                                           message:tipTextWhenNoPhotosAuthorization
                                                                    preferredStyle:UIAlertControllerStyleAlert];
            
            UIAlertAction *defaultAction = [UIAlertAction actionWithTitle:@"知道了"
                                                                    style:UIAlertActionStyleDefault
                                                                  handler:^(UIAlertAction * action) {}];
            [alert addAction:defaultAction];
            [self presentViewController:alert animated:YES completion:nil];
        }else {
            [self setupCameraController];
        }
	```
		
#### 保存和读取操作
	// 保存到相册
	```
	[[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{
        assetId = [PHAssetCreationRequest creationRequestForAssetFromImage:_captureImage].placeholderForCreatedAsset.localIdentifier;
    } completionHandler:^(BOOL success, NSError * _Nullable error) {
        if (!error) {
            previewPhoto = assetId;
            // TODO:保存assetId
        }
        
        dispatch_async(dispatch_get_main_queue(), ^{
        // 注意:主线程更新UI部分操作在这里进行
    });
    }];
	```
	// 根据assetId读取
	```
	PHImageRequestOptions *requestOptions = [[PHImageRequestOptions alloc] init];
    requestOptions.resizeMode   = PHImageRequestOptionsResizeModeExact;
    requestOptions.deliveryMode = PHImageRequestOptionsDeliveryModeHighQualityFormat;
    requestOptions.synchronous = YES;
    
    PHImageManager *imageManager = [[PHImageManager alloc] init];
    [imageManager requestImageForAsset:[PHAsset fetchAssetsWithLocalIdentifiers:@[assetId] options:nil].firstObject
                            targetSize:CGSizeMake(800, 1200)
                           contentMode:PHImageContentModeDefault
                               options:requestOptions
                         resultHandler:^(UIImage *result, NSDictionary *info) {
                             if (result) {
                                 
                                 if ([[info valueForKey:@"PHImageResultIsDegradedKey"] integerValue] == 0){
                                     // Do something with the FULL SIZED image
                                     _imageView.image = result;
                                 } else {
                                     // Do something with the regraded image
                                 }
                             }else {
                                 _imageView.image = [UIImage imageNamed:@"default_image"];
                             }
                         }];
	```
#### Tips:

>1.[[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{} completionHandler:^(BOOL success, NSError * _Nullable error) {}];方法是异步操作,在里边更新UI需要dispatch_async(dispatch_get_main_queue(), ^{});
	
>2.使用- (PHImageRequestID)requestImageForAsset:(PHAsset *)asset targetSize:(CGSize)targetSize contentMode:(PHImageContentMode)contentMode options:(nullable PHImageRequestOptions *)options resultHandler:(void (^)(UIImage *__nullable result, NSDictionary *__nullable info))resultHandler方法取照片，会调用2次，有取照片上传服务器的需要注意，不然会遇到上传2次重复图片文件，也有人发现了，[stack overflow](http://stackoverflow.com/questions/26663258/uiimage-size-returned-from-requestimageforasset-is-not-even-close-to-the-targ)




