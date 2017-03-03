## 配置
参考官网[使用 Java 8 语言功能](https://developer.android.google.cn/guide/platform/j8-jack.html?hl=zh-cn)

支持 Java 8 语言功能需要一个名为 Jack 的新编译。

- Android Studio 2.1 和更高版本上才受支持
- 旧版 javac 工具链：
```
javac (.java → .class) → dx (.class → .dex)
```
- 新版 Jack 工具链：
```
Jack (.java → .jack → .dex)
```

### gradle
```
android {
  ...
  defaultConfig {
    ...
    jackOptions {
      enabled true
    }
  }
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

## 已知问题
### ButterKnife需要升级到8.3.0
```
compile 'com.jakewharton:butterknife:8.3.0'
annotationProcessor 'com.jakewharton:butterknife-compiler:8.3.0'
```

### 不需要再使用android-apt插件
删除
```
apply plugin: 'android-apt'
```
```
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```
使用**annotationProcessor**替代**apt**(gradle v2.2.0以上版本已内置支持)
