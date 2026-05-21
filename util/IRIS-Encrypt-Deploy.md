
<style>
table td:first-of-type {
    word-break:keep-all;
}

/*超过1600 侧边显示目录*/
@media screen and (min-width: 1600px) {
    .markdown-body>ul:first-of-type{
        position: fixed;
		right: calc(50% - 800px) ; 
		width: 300px;
		height: 90%;
		top: 5%;
        overflow: auto;
        list-style-type:none;
    }
     .markdown-body>ul:first-of-type ul{
        list-style-type:none;
     }

}

</style>

- [加密服务部署说明](#加密服务部署说明)
  - [1. 说明](#1-说明)
  - [2. 部署环境要求](#2-部署环境要求)
    - [2.1 Windows](#21-windows)
    - [2.2 Linux](#22-linux)
  - [3. 部署 JAR 服务](#3-部署-jar-服务)
    - [3.1 Windows 部署](#31-windows-部署)
    - [3.2 Linux 部署](#32-linux-部署)
  - [4. Cache/IRIS 导入与配置](#4-cacheiris-导入与配置)
  - [5. 常见问题](#5-常见问题)

# 加密服务部署说明

## 1. 说明

本文用于指导部署 Java 加密服务（`cryptographic-service-0.0.1-SNAPSHOT.jar`、`service-0.0.1-SNAPSHOT.jar`），并在 Cache/IRIS 中完成调用配置。

## 2. 部署环境要求

- **硬件要求**：无特殊要求，可部署在 ECP 服务器或其他内网可访问服务器。
- **JDK 要求**：JDK 1.8
- **服务端口**：`28086`、`28087`（确保服务器端口已打开）

### 2.1 Windows

- **JDK 安装**：可使用附件中的 `jdk1.8.0_251.zip`。

### 2.2 Linux

- **JDK 安装参考**：
  - [Linux上安装JDK 1.8：详细步骤与环境配置](https://blog.csdn.net/h__913246828/article/details/140322901)
  - [Oracle Java 8 下载](https://www.oracle.com/java/technologies/downloads/?er=221886#java8)
  - CentOS 7 参考：<https://blog.csdn.net/weixin_44571055/article/details/134647535>

## 3. 部署 JAR 服务

- `cryptographic-service-0.0.1-SNAPSHOT.jar` 该jar包为JAVA标准加密 发布的端口为`28087`
- `service-0.0.1-SNAPSHOT.jar` 该jar包为JAVA非标准加密 发布的端口为`28086`

### 3.1 Windows 部署

1. 将 `cryptographic-service-0.0.1-SNAPSHOT.jar`、`service-0.0.1-SNAPSHOT.jar`放到 `D:\` 根目录。  
	![](http://hisui.cn/wp-content/uploads/2026/04/image1.png)

2. 进入 JDK 的 `bin` 目录，打开 `CMD`。  
	![](http://hisui.cn/wp-content/uploads/2026/04/image2.png)
3. 分别执行启动命令：

    ```shell
    java -jar d:\cryptographic-service-0.0.1-SNAPSHOT.jar
    java -jar d:\service-0.0.1-SNAPSHOT.jar
    ```

4. 启动成功后可看到类似如下输出：  
	![](http://hisui.cn/wp-content/uploads/2026/04/image3.png)

> 注意：直接在命令行启动时，关闭窗口后服务会终止。

### 3.2 Linux 部署

1. 上传 `cryptographic-service-0.0.1-SNAPSHOT.jar`、`service-0.0.1-SNAPSHOT.jar`到：

    ```shell
    /dthealth/app/dthis/web/hisbase/bsp
    ```

	若目录不存在请先创建。

2. 确认已安装 JDK 1.8（可使用附件 `jdk-8u271-linux-x64.tar`）。

3. 前台启动（用于首次验证）：

    ```shell
    java -jar /dthealth/app/dthis/web/hisbase/bsp/cryptographic-service-0.0.1-SNAPSHOT.jar
    java -jar /dthealth/app/dthis/web/hisbase/bsp/service-0.0.1-SNAPSHOT.jar
    ```

4. 启动成功后可看到类似如下输出：  

	![image-20260515095351434](http://hisui.cn/wp-content/uploads/2026/05/image-20260515095351434.png)

5. （可选）后台启动（建议接口验证通过后再做）：

    ```bash
    nohup java -jar /dthealth/app/dthis/web/hisbase/bsp/cryptographic-service-0.0.1-SNAPSHOT.jar > /tmp/javalog.txt 2>&1 &
    nohup java -jar /dthealth/app/dthis/web/hisbase/bsp/service-0.0.1-SNAPSHOT.jar > /tmp/javalog.txt 2>&1 &
    ```

    可通过 `/tmp/javalog.txt` 确认服务启动状态。若系统重启，需重新启动服务。

6. （可选）配置开机自启（建议接口验证通过后再做）：

    - 将附件中的 `myjavaapp` 放到 `/etc/init.d/`  
    ![](http://hisui.cn/wp-content/uploads/2026/04/image4.png)
    - 依次执行：

        ```bash
        sudo chmod +x /etc/init.d/myjavaapp
        sudo chkconfig --add myjavaapp
        sudo service myjavaapp start
        sudo chkconfig myjavaapp on
        ```

    - 查看 `/tmp/javalog.txt`，确认启动成功：  
    ![](http://hisui.cn/wp-content/uploads/2026/04/image5.png)

    > 注意：服务发布成功后，请勿停止该进程，否则接口调用会失败。

## 4. Cache/IRIS 导入与配置

1. 将附件中的加密工具类导入目标 Cache/IRIS 数据库。  
![](http://hisui.cn/wp-content/uploads/2026/04/image6.png)

2. 导入时选择：
   - 勾选所有文件
   - 勾选编译导入项目
   - 点击 `OK`  
    ![](http://hisui.cn/wp-content/uploads/2026/04/image7.png)

3. 若系统中已存在同名类，可直接覆盖更新。

4. 导入完成后，打开 `Util.Impl.EncryptionUtils`，修改服务地址参数：

```objectscript
Parameter NonStandardAlgUrl = "http://localhost:28086";
Parameter StandardAlgUrl = "http://localhost:28087";
```

将 `localhost` 改为 JAR 所在服务器 IP，例如：

```objectscript
Parameter NonStandardAlgUrl = "http://192.168.1.1:28086";
Parameter StandardAlgUrl = "http://192.168.1.1:28087";
```

![image-20260515095754680](http://hisui.cn/wp-content/uploads/2026/05/image-20260515095754680.png)

## 5. 验证方法

- 验证JAVA标准加密 选择该链接内任意方法有返回值即可 [JAVA 加密服务 - 标准加密](http://hisui.cn/bsp/util/IRIS-Encrypt#java-加密服务---标准加密) 
- 验证JAVA非标准加密 选择该链接内任意方法有返回值即可[JAVA 加密服务 - 非标准加密](http://hisui.cn/bsp/util/IRIS-Encrypt#java-加密服务---非标准加密)

验证成功后在Terminal中执行以该方法用于部署程序：

```java
w ##class(Util.EncryptionUtils).Deploy()
```

运行后提示该内容即可：

```java
DHC-APP>w ##class(Util.EncryptionUtils).Deploy()

Compilation started on 05/21/2026 11:03:06 with qualifiers 'cuk'
Removed deployed classes from compile list
Class Util.EncodingUtils is up-to-date.
Class Util.Encryption.BigIntBytes is up-to-date.
Class Util.Encryption.DES is up-to-date.
Class Util.Encryption.MD5 is up-to-date.
Class Util.Encryption.Primitives is up-to-date.
Class Util.Encryption.RSA is up-to-date.
Class Util.Encryption.RSAPrimitives is up-to-date.
Class Util.Encryption.SM2 is up-to-date.
Class Util.Encryption.SM2Callout is up-to-date.
Class Util.Encryption.SM2EmbeddedPython is up-to-date.
Class Util.Encryption.SM2Shell is up-to-date.
Class Util.Encryption.SM3 is up-to-date.
Class Util.Encryption.SM4 is up-to-date.
Class Util.Encryption.TripleDES is up-to-date.
Class Util.EncryptionUtils is up-to-date.
Class Util.Impl.EncodingUtils is up-to-date.
Class Util.Impl.EncryptionUtils is up-to-date.
Compilation finished successfully in 0.154s.
1
```

## 6. 常见问题

- **从 Word 复制后出现语法异常**  
  原因通常是复制带入了特殊字符。建议手动输入关键命令或代码。  
  ![](http://hisui.cn/wp-content/uploads/2026/04/image9.png)

- **调用报错，疑似 IP/端口不通**  
  请确认第 4 节中的 URL 配置正确，并在命令行使用 `telnet` 测试目标 IP 与端口连通性。  
  ![](http://hisui.cn/wp-content/uploads/2026/04/image10.png)

- **调用失败，疑似服务未启动**  
  请先确认 Java 服务进程是否正常运行。  
  ![](http://hisui.cn/wp-content/uploads/2026/04/image11.png)

- **提示端口占用**  
  错误示例：`Web server failed to start. Port 8087 was already in use.`  
  处理建议：更换端口、停止占用进程，或重启服务器后重试。

- **Q：有多台 ECP，是否每台都要部署？**  
  A：不需要。部署一台内网可访问、运行稳定的服务器即可。  
  ![](http://hisui.cn/wp-content/uploads/2026/04/image12.png)

- **Q：方法返回与文档示例不一致？**  
  A：请在 `Terminal` 中执行示例命令，再对比结果。  
  ![](http://hisui.cn/wp-content/uploads/2026/04/image13.png)
