# 自动化打包加固上传脚本

------
RreinforcePlugin支持加固和多渠道流程自动化，
## 目前加固主要包括：360加固和腾讯LeGu加固
### 360加固
> 其中360加固工具包含加固和重签名，无需额外配置

### 腾讯LeGu加固
> Legu加固支持加固，重签名需要调用android_sdk中的对齐和重签名进制文件，
所以需要配置环境ANDROID_HOME或者local.properties中配置sdk.dir

## 目前多渠道主要包括：美团walle多渠道和腾讯Vasdolly多渠道
相关多渠道工具需要配合自己的多渠道依赖才能在代码中取到渠道信息
### walle
```java
implementation 'com.meituan.android.walle:library:1.1.7'
WalleChannelReader.getChannel(getApplicationContext())
```
### vasdolly
```java
implementation 'com.leon.channel:helper:2.0.3'
ChannelReaderUtil.getChannel(this)
```


------
使用流程
### 0.预置环境
在根目录gradle.properties配置签名信息
```
keyStorePath="zkyml.jks"
keyStorePass="keyStorePass"
keyStoreAlias="zkymlkey"
keyStoreAliasPass="keyStoreAliasPass"
```
如果使用腾讯legu加固(360加固则不需要，自带重签名功能)
则需要预置环境ANDROID_HOME或者local.properties中配置sdk.dir=F\:\\sdk
该环境一般默认都有
### 1.在项目的根目录的build.gradle配置maven仓库和依赖的classpath
```
buildscript {
 repositories {
      maven {
                 url 'https://repo.rdc.aliyun.com/repository/41055-release-SKNpal'
                 credentials {
                     username 'C7zBkz'
                     password 'KV5vz3yTCo'
                 }
             }
 }

  dependencies {
        classpath "com.zkyml.plugin:reinforce:1.0.4"
  }
}
```
### 2.在app这个Module下的的build.gradle使用该插件
```
apply plugin: 'reinforceplugin'
ReinForceChannels {
    //加固类型 四种方式 360 le all query 为空则不启用加固
    reinforceType = "360"
    //多渠道类型 2种方式 walle vasdolly
    channelType = "walle"

    //蒲公英上传key以及渠道，key为空或者渠道不存在则不上传蒲公英
    pgyKey = "key"
    pgyChannel = "pgy"

    //多渠道配置,二选一,优先channelstr,没有才channelFile,默认channelstr=default
    channelStr = "yyb,pgy"
    //channelFile = file("channel.txt").absolutePath
    reinforce360 {
    autoSign = true
        // 支持该加固
        support = true
        // 配置自己的360账号
        user = "360加固必填"
        // 配置自己的360账号密码
        pass = "360加固必填"
        // 配置自己的360加固文件jiagu.jar和lib的文件夹位置
        path = file("build/jiagu/jiagu.jar").absolutePath
        /** 增值配置 默认配置空. 其它 -x86 -data -crashlog -vmp -nocert*/
        addConfig = ""
    }
    reinforceLe {
    autoSign = true
        // 支持该加固
        support = true
        // 配置自己的乐固加固文件路径 可以选择不配置使用默认内置的
        path = file("build/jiagu/ms-shield.jar").absolutePath
        // 配置自己的乐固secretId
        secretId = "乐固加固必填"
        // 配置自己的乐固secretKey
        secretKey = "乐固加固必填"
    }
}
```
------
