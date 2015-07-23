# PINRemoteImage

## Fast, non-deadlocking parallel image downloader and cache for iOS

[PINRemoteImageManager](Pod/Classes/PINRemoteImageManager.h) is an image downloading, processing and caching manager. It uses the concept of download and processing tasks to ensure that even if multiple calls to download or process an image are made, it only occurs one time (unless an item is no longer in the cache). PINRemoteImageManager is backed by GCD and safe to access from multiple threads simultaneously. It ensures that images are decoded off the main thread so that animation performance isn't affected. None of it's exposed methods allow for synchronous access, however it is optimized to call completions on the calling thread if an item is in its memory cache.

PINRemoteImage supports downloading many types of files. It, of course, supports both PNGs and JPGs. It also supports decoding WebP images if Google's library is available. It even supports returning [FLAnimatedImages](https://github.com/Flipboard/FLAnimatedImage) if it's compiled in (though this can be disabled).

PINRemoteImage also has two methods to improve the experience of downloading images on slow network connections. The first is support for progressive JPGs. This isn't any old support for progressive JPGs though, PINRemoteImage adds an attractive blur to progressive scans before returning them.

![Progressive JPG with Blur](/progressive.gif "Looks better on device.")

[PINRemoteImageCategoryManager](Pod/Classes/PINRemoteImageCategoryManager.h) defines a protocol which UIView subclasses can implement and provide easy access to 
PINRemoteImageManager's methods. Built in, are categories on UIImageView, FLAnimatedImageView and UIButton, however it's very easy to implement a new category. See [UIImageView+PINRemoteImage](/Pod/Classes/Image Categories/UIImageView+PINRemoteImage.h) of the existing categories for reference.


Download an image and set it on an image view:
```objective-c
UIImageView *imageView = [[UIImageView alloc] init];
[imageView setImageFromURL:[NSURL URLWithString:@"http://pinterest.com/kitten.jpg"]];
```
    
Download a progressive jpeg and get attractive blurred updates:
```objective-c
UIImageView *imageView = [[UIImageView alloc] init];
[imageView setUpdateWithProgress:YES];
[imageView setImageFromURL:[NSURL URLWithString:@"http://pinterest.com/progressiveKitten.jpg"]];
```

Download a WebP file
```objective-c
UIImageView *imageView = [[UIImageView alloc] init];
[imageView setImageFromURL:[NSURL URLWithString:@"http://pinterest.com/googleKitten.webp"]];
```

Download a GIF and display with FLAnimatedImageView
```objective-c
FLAnimatedImageView *animatedImageView = [[FLAnimatedImageView alloc] init];
[animatedImageView setImageFromURL:[NSURL URLWithString:@"http://pinterest.com/flyingKitten.gif"]];
```

Download and process an image
```objective-c
UIImageView *imageView = [[UIImageView alloc] init];
[self.imageView setImageFromURL:[NSURL URLWithString:@"https://s-media-cache-ak0.pinimg.com/736x/5b/c6/c5/5bc6c5387ff6f104fd642f2b375efba3.jpg"] processorKey:@"rounded" processor:^UIImage *(PINRemoteImageManagerResult *result, NSUInteger *cost)
 {
     CGSize targetSize = CGSizeMake(200, 300);
     CGRect imageRect = CGRectMake(0, 0, targetSize.width, targetSize.height);
     UIGraphicsBeginImageContext(imageRect.size);
     UIBezierPath *bezierPath = [UIBezierPath bezierPathWithRoundedRect:imageRect cornerRadius:7.0];
     [bezierPath addClip];
     
     CGFloat sizeMultiplier = MAX(targetSize.width / result.image.size.width, targetSize.height / result.image.size.height);
     
     CGRect drawRect = CGRectMake(0, 0, result.image.size.width * sizeMultiplier, result.image.size.height * sizeMultiplier);
     if (CGRectGetMaxX(drawRect) > CGRectGetMaxX(imageRect)) {
         drawRect.origin.x -= (CGRectGetMaxX(drawRect) - CGRectGetMaxX(imageRect)) / 2.0;
     }
     if (CGRectGetMaxY(drawRect) > CGRectGetMaxY(imageRect)) {
         drawRect.origin.y -= (CGRectGetMaxY(drawRect) - CGRectGetMaxY(imageRect)) / 2.0;
     }
     
     [result.image drawInRect:drawRect];
     
     UIImage *processedImage = UIGraphicsGetImageFromCurrentImageContext();
     UIGraphicsEndImageContext();
     return processedImage;
 }];
```

## Installation

### Manually

[Download the latest tag](https://github.com/Pinterest/PINRemoteImage/tags) and drag the `PINRemoteImage` folder into your Xcode project.

Install the docs by double clicking the `.docset` file under `docs/`, or view them online at [cocoadocs.org](http://cocoadocs.org/docsets/PINRemoteImage/)

### Git Submodule

    git submodule add https://github.com/pinterest/PINRemoteImage.git
    git submodule update --init

### CocoaPods

Add [PINRemoteImage](http://cocoapods.org/?q=name%3APINRemoteImage) to your `Podfile` and run `pod install`.

## Requirements

__PINRemoteImage__ requires iOS 7.0 or greater.

## Contact

[Garrett Moon](mailto:garrett@pinterest.com)
[@garrettmoon](https://twitter.com/garrettmoon)
[Pinterest](https://www.pinterest.com/garrettlunar/)

## License

Copyright 2015 Pinterest, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. [See the License](LICENSE.txt) for the specific language governing permissions and limitations under the License.