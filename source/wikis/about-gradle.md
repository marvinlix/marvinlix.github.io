title: Android开发中常用gradle配置
date: 2016-06-30 11:53:47
---

## Android Gradle常用配置

### 编译加速

在``gradle.properties``中配置

```
org.gradle.configureondemand=true				#demand
org.gradle.daemon=true 						#单独的守护进程
org.gradle.parallel=true 					#串行编译
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8						#内存设置
```

``build.gradle``中添加

```
lintOptions {
    disable 'InvalidPackage', 'ContentDescription'
    abortOnError false
}

dexOptions {
    jumboMode = true
    preDexLibraries = false
    javaMaxHeapSize "4g"
}
```

### 只支持特定abi

``abiFilters``配置，可配合``Product flavors``使用。


```
ndk {
	abiFilters "armeabi", "x86"
}
```

### 依赖库版本管理

在项目工程``build.gradle``中统一定义

```
ext {
    // Sdk and tools
    minSdkVersion = 10
    targetSdkVersion = 22
    compileSdkVersion = 23
    buildToolsVersion = '23.0.2'

    // App dependencies
    supportVersion = '23.4.0'
}
```

在模块工程``build.gradle``使用

```
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion
}

dependencies {
    // App's dependencies, including test
    compile "com.android.support:appcompat-v7:$rootProject.supportVersion"
    compile "com.android.support:cardview-v7:$rootProject.supportVersion"
    compile "com.android.support:design:$rootProject.supportVersion"
    compile "com.android.support:recyclerview-v7:$rootProject.supportVersion"
    compile "com.android.support:support-v4:$rootProject.supportVersion"
}
```

### 依赖库多版本解决

```
configurations.all {
    resolutionStrategy.force "com.android.support:support-v4:$rootProject.supportVersion"
}
```

### 签名信息

项目中添加``sign.properties``

```
keystore=[path to]\release.keystore
keystore.password=*********
keyAlias=***********
keyPassword=********
```

在``build.gradle``读取文件并设置签名信息

```
if(new File(project.property("sign.properties")).exists()) {

    Properties props = new Properties()
    props.load(new FileInputStream(file(project.property("sign.properties"))))

    signingConfigs {
        release {
            storeFile file(props['keystore'])
            storePassword props['keystore.password']
            keyAlias props['keyAlias']
            keyPassword props['keyPassword']
        }
    }
}
```