<img src="http://i.imgur.com/0ksj7Gh.png" alt="SwiftShield logo" height="140" >

# Swift/OBJ-C 代码混淆

[![GitHub release](https://img.shields.io/github/tag/rockbruno/swiftshield.svg)](https://github.com/rockbruno/swiftshield/releases)

SwiftShield是一个为你的iOS项目对象(包括Pods和Storyboards)生成不可逆的加密名称的工具，以保护你的App避免逆向设计iOS程序相关工具的干扰，比如class-dump和Cycript.

```swift
class EbnaVwSSHIWHeIRrFLtFSYnTgvRgTTpo: jXDJKvNJeYqaQvdEOlpEpUJrdBmNMFaH {
  func SbIhBmtNGVVkdqRUwxotofZfQxbNYwXY() -> GWKvEvnEwaGTliRSseMposPTETmhkhCZ {
    return aQdxnPDJQNKFtQPIYRxsoePvVfEdkwua()
  }
}
```


## 🤖 自动模式 (仅限Swift)

使用 `-automatic` 标记, SwiftShield将使用 SourceKit 自动混淆整个工程(包括依赖项).特别的, SwiftShield自动模式[并不会重构所有内容](SOURCEKITISSUES.md), 它的范围与Xcode的自带重构工具的范围直接相关. 虽然文档中的特定案例不会被混淆，但SwiftShield将对所有可以进行逆向工程的Swift类和方法进行混淆. 看一下示例项目，看看SwiftShield是如何工作的!


## 🛡 手动模式 (Swift/OBJ-C)

如果您想完全混淆所有内容(包括typealiases和property), 也可以使用手动模式. 这是运行Swiftshield最简单的方法, 也是最耗时的方法. 使用时, Swiftshield将基于您在属性名和类名结尾添加的标记来混淆它们. 例如， 在手动模式下运行Swiftshield和标记 `__s` 后，以下代码:

```swift
class EncryptedVideoPlayer__s: DecryptionProtocol__s {
  func start__s() {
    let vc__s = ImportantDecryptingController__s(secureMode__s: true)
    vc__s.start__s(playAutomatically__s: true)
  }
}
```
becomes:
```swift
class fjiovh4894bvic: XbuinvcxoDHFh3fjid {
  func cxncjnx8fh83FDJSDd() {
    let DjivneVjxrbv42jsr = vPAOSNdcbif372hFKF(vnjdDNsbufhdks3hdDs: true)
    DjivneVjxrbv42jsr.cxncjnx8fh83FDJSDd(dncjCNCNCKSDhssuhw21w: true)
  }
}
```


## 💥 还原被混淆的内容

在成功混淆项目后, Swiftshield将生成一个包含 `conversionMap.txt` 文件的输出文件夹, 其中包含对项目所做的所有更改, 方便你定位一个加密对象原来是什么.

````
//
// SwiftShield Conversion Map
// Automatic mode for SYK, 2019-06-30 17.03.25
// Deobfuscate crash logs (or any text file) by running:
// swiftshield -deobfuscate CRASH_FILE -deobfuscate_map THIS_FILE
//

minAction ===> RPrwgsxJxrhBcYbIjTnhyoBXTIkagWui
member ===> JZjtOeNrUOUOXcGJLLhautLrQjJnUaUq
SYXErrorResponseModel ===> ySoqAgZKJISbwiktTnGvvrgVwgbdyETc
````

通过运行以下命令，可以使用此文件自动还原任何类型的被混淆内容基于文本内的日志:

```
swiftshield -deobfuscate CRASH_FILE -deobfuscate-map PATH_TO_CONVERSION_MAP
```

<img src="https://i.imgur.com/qMKy84P.png" alt="SwiftShield logo" height="172">

## 🚨 要求

### 自动模式:

如果你的app的一个或多个模块/扩展满足这些条件而无法正确混淆, 你可以使用  `-ignore-modules` 参数避免混淆它们

1. 基于类名/属性名的字符串参数, 比如加载 `MyClass.xib` , 其中 `String(describing: type(of:self))`  是  `'MyClass'`   
2. 在OC类调用Swift方法 (Swift 类调用OC方法能正常工作, 除非涉及到接口)
4. 最新的Swift版本和Xcode命令行工具 (适用于所有版本, 但可能由于不同的sourcekit版本而产生不同的结果)
5. 确保您的项目不包含 [SourceKit 的bug](SOURCEKITISSUES.md)之一. 虽然这些bug不会阻止项目被混淆的进程，但其中一些bug可能需要在之后进行一些手动修复.

(使用 `NSExtensionPrincipalClass` 或者变量的App扩展部分在 `Info.plist` (像 Rich Notifications/Watch apps) 也将会被正确的混淆并被索引, 前提是你没有改变他们在 `$(PRODUCT_MODULE_NAME).ClassName` 中的默认值. 如果修改这些plist以指向不同模块中的类, 则在运行此工具后必须手动更改它们.)

### 手动模式:

1. 确保您的标签没有使用无法被混淆的内容，如硬编码字符串.

## ⚙️ 安装

**⚠️:** SwiftShield **不可撤销的覆盖** 你的所有源文件. 理想情况下, 应该只在CI服务器和发布版本上运行它.

下载 [最新版本](https://github.com/rockbruno/swiftshield/releases) 从这个仓库中 [单击此处查看如何配置Swiftshield.](USAGE.md)


## 🛫 运行 SwiftShield

### 自动模式

```
swiftshield -automatic -project-root /app/MyApp -automatic-project-file /app/MyApp/MyApp.xcworkspace -automatic-project-scheme MyApp-AppStore
```
**所需参数:**

- `-automatic`: 启用自动模式.

- `-project-root`: 你的项目的根目录.Swiftshield将使用此目录搜索项目文件.

- `-automatic-project-file`: 你的应用程序的主.xcodeproj/.xcworkspace文件.

- `-automatic-project-scheme myScheme`: 从你的 `-automatic-project-file`中编译的主要方案.

**可选参数:**

- `-ignore-modules`: 防止某些模块被混淆, 用逗号分隔. 如果某个模块不能正确混淆, 请使用此选项. 请注意, 这应该是导入模块的确切名称(而不是目标名称!).示例: `MyLib,MyAppRichNotifications,MyAppWatch_Extension`

- `-show-sourcekit-queries`: 打印发送到SourceKit的查询.请注意,它们是巨大的,并且绝对会使您的终端杂乱无章,所以只在bug报告和特性开发中使用它! 

### 手动模式

```
swiftshield -project-root /app/MyApp
```
**所需参数:**

- `-project-root`: 你的项目的根目录.Swiftshield将使用此目录搜索项目文件、storyboards和资源文件.

**可选参数:**

- `-tag`: 使用一个自定义的标签. 默认是 `__s`.

### 两种模式的附加参数

- `-verbose`: 打印附加信息.

- `-obfuscation-character-count`: 设置混淆名称的字符数.默认情况是 `32` .请注意,由于名称冲突的可能性较大,使用较小的数字将导致运行较慢.

- `-dry-run`: 不会实际覆盖文件.调试的时候很有用!


## 🤝 贡献

对Swiftshield的贡献非常受欢迎! 目前还没有贡献指南, 所以您可以自由的使用问题选项卡来讨论未来的特性和改进.

## ✅ 自动模式下一步

- [X] 方法名称
- [X] 更新扩展plist (Rich Notifications / Watch main classes)
- [ ] "SDK Mode" (ignore `public`)
- [ ] 属性
- [ ] 模块名称


## 许可证

Swiftshield根据GNU GPL v3.0许可证发布.有关详细信息, 请参阅许可证.

## 鸣谢

感谢来自 [Refactorator](https://github.com/johnno1962/Refactorator) 的john holdsworth对`SourceKit.swift`的贡献，感谢Apple对其开源SourceKit包装器的支持，感谢SourceKitten帮助我找出SourceKit的哪些编译参数可以忽略.
