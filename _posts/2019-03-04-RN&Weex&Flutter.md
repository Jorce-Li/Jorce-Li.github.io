---
layout: post
title: "RN、Weex、Flutter项目踩坑"
subtitle: '踩坑记录'
author: "Jorce"
header-style: text
tags:
  - 踩坑
---

Update:项目启动

---

最近在捣鼓Hybird开发APP就想对比一下几个框架的优劣,所以有了本篇文章,windows系统

# Weex&RN

### 两者需要开发androidApp的的环境相同,注意点是目前版本需要jdk为1.8

### gradle版本

这是个大坑,目前android studio已经更新到3.x版本了

RN最新的脚手架下载的版本是3.2也就是比较新的版本配套的设置基本正确

Weex最新的脚手架的版本是2.14.1,所以全部需要手动配置

1. Gradle版本不匹配
修改项目下 gradle/wrapper/gradle-wrapper.propertie 文件中的distributionUrl

AS 3.0.0 ~ 3.0.1改为：
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
AS 3.1.1 ~ 3.1.4 改为：
distributionUrl=https\://services.gradle.org/distributions/gradle-4.4-all.zip
AS 3.2.0 ~ 3.2.1 改为：
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip
AS 3.3.0 ~ 3.3.x 改为：
distributionUrl=https\://services.gradle.org/distributions/gradle-4.10.1-all.zip

2. Gradle插件不匹配
1) 项目根目录下的 build.gradle文件中两个repositories节点都添加google()
2) build.gradle支持库版本
AS 3.0 ~ 3.0.1：
android {
  compileSdkVersion 26
  buildToolsVersion "26.0.2"
  ...
}
AS 3.1.1 ~ 3.1.4：
android {
  compileSdkVersion 27
  buildToolsVersion "27.0.3"
  ...
}
AS 3.2 ~ 3.3.x：
android {
  compileSdkVersion 28
  buildToolsVersion "28.0.3"
  ...
}
3. Gradle自定义apk名称报错（Cannot set the value of read-only property 'outputFile' ）

applicationVariants.all { variant ->
    variant.outputs.all { output ->  // each 改为 all
    def fileName = "${variant.versionName}_release.apk"
    def outFile = output.outputFile
    if (outFile != null && outFile.name.endsWith('.apk')) {
        outputFileName = fileName  //  output.outputFile 改为 outputFileName 
    }    
}

这里又有个大坑,打包出来之后应该是“x.x.x_release.apk”,但是默认stat的名称是weex-app.apk,就安装不上,目前我还没有发现怎么解决,只能手动adb install一下

4. 关键字变化compile => implementation
5. includeCompileClasspath 设置

Error:Execution failed for task ':app:javaPreCompileDebug'.Annotation processors
如果出现上述报错,
在android{
  defaultConfig{
    添加这句话
    javaCompileOptions { annotationProcessorOptions { includeCompileClasspath = true } }
  }
}

至此基本大功告成。

# Flutter 

这里先记录一点就是需要把虚拟机hype-v关一下就可以了
