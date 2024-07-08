---
layout: post
title: flutter 项目打包时 长时间停留在 Running Gradle task 'assembleRelease'... 
categories: Tech
author: 蓝不住
date: 2024-07-09 05:20 +0800 
tags:
 - flutter
---

升级flutter后提示项目需要更高kotlin pulgin 版本，更换版本后尝试build原项目，出现一系列问题，判断时gradle cache 导致，清空缓存后再次尝试build，等待半天后，实在难以忍受20KB左右的下载速度。

原因：从源网站下载速度太慢

解决：修改flutterSDK目录下 packages\flutter_tools\gradle\src\main\groovy\flutter.groovy 中 buildscript -> repositories
    替换

    ```groovy
    repositories {
        google()
        mavenCentral()
    }
    ```

    为
    
    ```groovy
    repositories {
        // google()
        // mavenCentral()
		maven { url 'https://maven.aliyun.com/repository/google' }
		maven { url 'https://maven.aliyun.com/repository/jcenter' }
		maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
    }
    ```
    
    flutter\packages\flutter_tools\gradle\resolve_dependencies.gradle   repositories中如上增加

    ```gradle
    repositories {
        google()
        mavenCentral()
        maven {
            url "$storageUrl/${engineRealm}download.flutter.io"
        }
		maven { url 'https://maven.aliyun.com/repository/google' }
		maven { url 'https://maven.aliyun.com/repository/jcenter' }
		maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
    }
    ```

依稀记得最初使用flutter时，也有这么一步修改SDK下载源的过程，当初似乎修改的是 flutter\packages\flutter_tools\gradle\flutter.gradle 

以上两个文件是否都需要更改，不太确定。。 

反正奏效了。