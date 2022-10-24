## 创建 xcframework

[苹果官方文档](https://developer.apple.com/documentation/xcode/creating-a-multi-platform-binary-framework-bundle)

### 为 frameworks | libraries 创建 archives

```
xcodebuild archive \
-project ABCFramework.xcodeproj \
-scheme ABCFramework  \
-destination "generic/platform=iOS" \
-archivePath "archives/ABCFramework"
```
>-destination 这个参数决定了架构和SDK
generic/platform=iOS的意思是为 simulator
generic/platform=macOS,variant=Mac Catalyst, 为mac 产生一个 archive

### 创建一个 XCFramework bundle

```
xcodebuild -create-xcframework
    -archive archives/MyFramework-iOS.xcarchive -framework MyFramework.framework
    -archive archives/MyFramework-iOS_Simulator.xcarchive -framework MyFramework.framework
    -archive archives/MyFramework-macOS.xcarchive -framework MyFramework.framework
    -archive archives/MyFramework-Mac_Catalyst.xcarchive -framework MyFramework.framework
    -output xcframeworks/MyFramework.xcframework
```
>如果要包含一个.a 结尾的静态库文件,则需要将-framework替换为-library
如果你想包含一些 archive 之外的文件, 像如下一样指定路径
```
xcodebuild -create-xcframework
    -library products/iOS/usr/local/lib/libMyLibrary.a -headers products/iOS/usr/local/include
    -library products/iOS_Simulator/usr/local/lib/libMyLibrary.a -headers products/iOS/usr/local/include
    -library products/macOS/usr/local/lib/libMyLibrary.a -headers products/macOS/usr/local/include
    -library products/Mac\ Catalyst/usr/local/lib/libMyLibrary.a -headers products/Mac\ Catalyst/usr/local/include
    -output xcframeworks/MyLibrary.xcframework
```
获取帮助```xcodebuild -create-xcframework -help```


### 注意点:
要单独保留 iOS 和 iOS Simulator的单一性, 不要使用 lipo 命令将两者合并成一个二进制文件.
iOS 设备的二进制文件是为 ARM64准备的,普通的二进制Simulator 是为 x86_64和 ARM64 苹果芯片准备的.
避免使用动态库二进制文件(.dylib)动态链接, xcframwork 文件虽然能包含动态库文件, 但是仅仅是 macOS 支持这些动态库的动态链接, 动态链接在这些平台(iOS, watchOS, tvOS)需要 XCFramework 包含.framework bundles

确定架构二进制文件的支持类型

```file <PathToFramework>/<FrameworkName>.framework/<FrameworkName>```
```file <PathToLibrary>/libMyLibrary.a```