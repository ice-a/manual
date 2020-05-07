# Gradle配置全局国内镜像仓库
## 进入GRADLE_USER_HOME
Windows环境下默认路径为: `C:\Users\[你的用户名]\.gradle\`
如果手动修改过环境变量, 则为环境变量指定的路径.

## 新建一个名为init.gradle的文件
以下是文件内容
```groovy
allprojects {
    // 配置jar包仓库
    repositories {
        mavenLocal()
        maven { name "aliyun" ; url "https://maven.aliyun.com/repository/public"   }
        maven { name "bsdn"   ; url "http://nexus.bsdn.org/content/groups/public/" }
    }

    // 配置插件仓库
    buildscript {
        repositories {
            maven { name "aliyun" ; url 'https://maven.aliyun.com/repository/public'   }
            maven { name "bsdn"   ; url 'http://nexus.bsdn.org/content/groups/public/' }
            maven { name "m2"     ; url 'https://plugins.gradle.org/m2/'               }
        }
    }
}
```

