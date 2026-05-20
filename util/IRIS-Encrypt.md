

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


- [IRIS加密接口说明](#iris加密接口说明)
  - [文档简介](#文档简介)
  - [文档说明](#文档说明)
  - [参数约定与填写说明](#参数约定与填写说明)
    - [1. `outputType` / `outputFormat`（摘要、HMAC、对称加密的输出或 Java 接口的格式）](#1-outputtype--outputformat摘要hmac对称加密的输出或-java-接口的格式)
    - [2. `keyType` / `ivType`（密钥、IV 如何解码）](#2-keytype--ivtype密钥iv-如何解码)
    - [3. `cipherType` 与 AES 解密里的 `outputType`](#3-ciphertype-与-aes-解密里的-outputtype)
    - [4. AES（IRIS 原生）](#4-aesiris-原生)
    - [5. SM4（`SM4Encrypt` / `SM4Decrypt`）](#5-sm4sm4encrypt--sm4decrypt)
    - [6. 原生 RSA（`RSAEncrypt` / `RSADecrypt`）](#6-原生-rsarsaencrypt--rsadecrypt)
    - [7. Java 网关加密（方法名以 `Java` 开头）](#7-java-网关加密方法名以-java-开头)
    - [8. 编码类 `Util.EncodingUtils`](#8-编码类-utilencodingutils)
- [一、加密方法](#一加密方法)
- [IRIS 原生加密](#iris-原生加密)
  - [1. MD5](#1-md5)
    - [MD5Encrypt](#md5encrypt)
    - [HMACMD5Encrypt](#hmacmd5encrypt)
  - [2. SHA](#2-sha)
    - [SHA1Encrypt](#sha1encrypt)
    - [SHA224Encrypt](#sha224encrypt)
    - [SHA256Encrypt](#sha256encrypt)
    - [SHA384Encrypt](#sha384encrypt)
    - [SHA512Encrypt](#sha512encrypt)
    - [HMACSHA1Encrypt](#hmacsha1encrypt)
    - [HMACSHA224Encrypt](#hmacsha224encrypt)
    - [HMACSHA256Encrypt](#hmacsha256encrypt)
    - [HMACSHA384Encrypt](#hmacsha384encrypt)
    - [HMACSHA512Encrypt](#hmacsha512encrypt)
  - [3. AES](#3-aes)
    - [AESECBZeroPaddingEncrypt](#aesecbzeropaddingencrypt)
    - [AESECBZeroPaddingDecrypt](#aesecbzeropaddingdecrypt)
    - [AESCBCPKCS7PaddingEncrypt](#aescbcpkcs7paddingencrypt)
    - [AESCBCPKCS7PaddingDecrypt](#aescbcpkcs7paddingdecrypt)
    - [AESCBCPKCS5PaddingEncrypt](#aescbcpkcs5paddingencrypt)
    - [AESCBCPKCS5PaddingDecrypt](#aescbcpkcs5paddingdecrypt)
    - [AESECBPKCS5PaddingEncrypt](#aesecbpkcs5paddingencrypt)
    - [AESECBPKCS5PaddingDecrypt](#aesecbpkcs5paddingdecrypt)
    - [AESECBPKCS7PaddingEncrypt](#aesecbpkcs7paddingencrypt)
    - [AESECBPKCS7PaddingDecrypt](#aesecbpkcs7paddingdecrypt)
    - [AESECBPKCS5PaddingEncryptStream](#aesecbpkcs5paddingencryptstream)
    - [AESECBPKCS5PaddingDecryptStream](#aesecbpkcs5paddingdecryptstream)
  - [4. SM3](#4-sm3)
    - [SM3Encrypt](#sm3encrypt)
    - [HMACSM3Encrypt](#hmacsm3encrypt)
  - [5. SM4](#5-sm4)
    - [SM4Encrypt](#sm4encrypt)
    - [SM4Decrypt](#sm4decrypt)
  - [6. RSA](#6-rsa)
    - [RSAEncrypt](#rsaencrypt)
    - [RSADecrypt](#rsadecrypt)
  - [7. DES](#7-des)
    - [DESEncrypt](#desencrypt)
    - [DESDecrypt](#desdecrypt)
  - [8. 3DES](#8-3des)
    - [3DESEncrypt](#3desencrypt)
    - [3DESDecrypt](#3desdecrypt)
  - [9. 奇偶加密](#9-奇偶加密)
    - [OddEvenEncrypt](#oddevenencrypt)
    - [OddEvenDecrypt](#oddevendecrypt)
- [JAVA 加密服务 - 标准加密](#java-加密服务---标准加密)
  - [1. SM2](#1-sm2)
    - [JavaSm2Sign](#javasm2sign)
    - [JavaSm2Verify](#javasm2verify)
    - [JavaSm2Encrypt](#javasm2encrypt)
    - [JavaSm2Encrypt4PKCS8](#javasm2encrypt4pkcs8)
    - [JavaSm2Decrypt](#javasm2decrypt)
  - [2. SM3](#2-sm3)
    - [JavaSm3Encrypt](#javasm3encrypt)
    - [JavaSm3Hmac](#javasm3hmac)
    - [JavaSm3VerifyHmac](#javasm3verifyhmac)
  - [3. SM4](#3-sm4)
    - [JavaSm4CbcZeroEncrypt](#javasm4cbczeroencrypt)
    - [JavaSm4CbcZeroDecrypt](#javasm4cbczerodecrypt)
    - [JavaSm4EcbPkcs5Encrypt](#javasm4ecbpkcs5encrypt)
    - [JavaSm4EcbPkcs5Decrypt](#javasm4ecbpkcs5decrypt)
    - [JavaSm4EcbPkcs7Encrypt](#javasm4ecbpkcs7encrypt)
    - [JavaSm4EcbPkcs7Decrypt](#javasm4ecbpkcs7decrypt)
  - [4. RSA](#4-rsa)
    - [JavaRsaEncryptByPublicKey](#javarsaencryptbypublickey)
    - [JavaRsaDecryptByPrivateKey](#javarsadecryptbyprivatekey)
    - [JavaSignByha256WithRsa](#javasignbyha256withrsa)
    - [JavaSignBySha1WithRsa](#javasignbysha1withrsa)
    - [JavaVerifyBySha1WithRsa](#javaverifybysha1withrsa)
  - [5. DES](#5-des)
    - [JavaDesEcbPkcs5Encrypt](#javadesecbpkcs5encrypt)
    - [JavaDesEcbPkcs5Decrypt](#javadesecbpkcs5decrypt)
  - [6. 3DES](#6-3des)
    - [JavaDesedeEcbPkcs5Encrypt](#javadesedeecbpkcs5encrypt)
    - [JavaDesedeEcbPkcs5Decrypt](#javadesedeecbpkcs5decrypt)
    - [JavaDesedeCbcPkcs7Encrypt](#javadesedecbcpkcs7encrypt)
    - [JavaDesedeCbcPkcs7Decrypt](#javadesedecbcpkcs7decrypt)
- [JAVA 加密服务 - 非标准加密](#java-加密服务---非标准加密)
    - [SignSm3WithSm2](#signsm3withsm2)
    - [SignSm3WithSm2Stream](#signsm3withsm2stream)
    - [SignSm3WithSm2StreamV2](#signsm3withsm2streamv2)
    - [JavaNonstandardYlswgdEncrypt](#javanonstandardylswgdencrypt)
    - [JavaNonstandardYlswgdDecrypt](#javanonstandardylswgddecrypt)
- [二、编码方法](#二编码方法)
  - [1. Base64](#1-base64)
    - [Base64Encode](#base64encode)
    - [Base64URLEncode](#base64urlencode)
    - [Base64Decode](#base64decode)
    - [Base32Encode](#base32encode)
    - [Base32Decode](#base32decode)
  - [2. HEX](#2-hex)
    - [Hex2ByteEncode](#hex2byteencode)
    - [Hex2ByteDecode](#hex2bytedecode)
    - [HexEncode](#hexencode)
    - [HexDecode](#hexdecode)
  - [3. UTF8](#3-utf8)
    - [UTF8Encode](#utf8encode)
    - [UTF8Decode](#utf8decode)
  - [4. Unicode](#4-unicode)
    - [UnicodeEncode](#unicodeencode)
    - [UnicodeDecode](#unicodedecode)
  - [5. URL](#5-url)
    - [URLEncode](#urlencode)
    - [URLDecode](#urldecode)

# IRIS加密接口说明

## 文档简介

本说明面向在 **IRIS ** 等环境中调用项目加密与编码能力的开发人员。文档汇总 **`Util.EncryptionUtils`**（摘要、对称加密、国密、RSA、Java 网关等）与 **`Util.EncodingUtils`**（Base64、Hex、URL、Unicode 等）的类方法：给出用途、参数、返回值、ObjectScript 签名及可调式示例，便于与第三方系统对账或联调。

阅读建议：先浏览 **「文档说明」** 了解结构；调用前务必阅读 **「参数约定与填写说明」**，其中说明了 `outputType`、`keyType`、`cipherType`、SM4、RSA 路径、Java 网关等常见填法，可减少因编码或密钥表示方式不一致导致的错误。正文按「IRIS 原生加密 → Java 加密服务 → 编码方法」分块，算法顺序与门面类中的方法分组大体一致。

文档内容力求与仓库内 **`Util.EncryptionUtils`**、**`Util.EncodingUtils`** 及 **`Util.Encryption.*`** 的当前实现一致；若代码变更后示例或参数说明未同步，以源码与实际运行结果为准。

## 文档说明

本文档按两类组织：

- 加密方法
- 编码方法

每个方法均采用统一模板：

- 描述
- 参数（名称与类型；**具体取值与互斥关系见下节**）
- 返回值
- 方法签名
- 调试示例

实现与门面类对应关系：**`Util.EncryptionUtils`** 为对外门面，**`Util.EncodingUtils`** 为编码门面；逻辑在 **`Util.Impl.EncryptionUtils`**、**`Util.Impl.EncodingUtils`**（及 `Util.Encryption.*` 等）。下文示例中的 `##class(Util.EncryptionUtils)` 与 `##class(Util.EncodingUtils)` 均指上述门面。

---

## 参数约定与填写说明

### 1. `outputType` / `outputFormat`（摘要、HMAC、对称加密的输出或 Java 接口的格式）

| 取值 | 含义 |
|------|------|
| `hex` | 小写十六进制字符串 |
| `base64` | 标准 Base64 |
| `raw` | 原始二进制串（终端里可能显示为乱码，适合程序间传递） |

- 空字符串或非法值会回退为**各方法自己的默认值**（常见为 `hex` 或 `base64`，以方法签名为准）。
- **别名**（仅走 `NormalizeOutputType` 的路径）：`base` 视为 `base64`；`text` 视为 `raw`。
- **MD5** 的 `outputType` 另支持把字面量 `text` 转成 `raw`（与其它方法的 `NormalizeOutputType` 行为对齐）。

### 2. `keyType` / `ivType`（密钥、IV 如何解码）

| 取值 | 含义 |
|------|------|
| `text` | 把传入的 `%String` 按 **UTF-8** 编成字节，再作为密钥或 IV |
| `hex` | 把内容当作十六进制串解码为字节 |
| `base64` | Base64 解码后的二进制作为密钥或 IV |

- 若传入非法类型，实现会按默认（多为 `text`）处理。
- **HMAC-SM3**：第三个参数是 **`keyType`**，不是输出格式；密钥为普通字符串时应填 `text`。

### 3. `cipherType` 与 AES 解密里的 `outputType`

对称解密（如 AES ECB/CBC 各 `*Decrypt`）需要知道**密文**是 Base64、Hex 还是原始字节：

- 若 **`cipherType` 非空**：以 `cipherType` 为准（`hex` / `base64` / `raw`）。
- 若 **`cipherType` 为空**：使用 **`outputType`** 表示密文编码（历史兼容参数名）。

加密侧 `outputType` 表示**输出**密文的编码；解密侧请勿混淆「密文长什么样」与「密钥类型」。

### 4. AES（IRIS 原生）

- `str`：明文；内部按 UTF-8 参与运算（与 Impl 一致）。
- `key`、`iv`：经 `keyType` / `ivType` 解码后的字节长度须符合 **AES-128 / 192 / 256**（**16 / 24 / 32 字节**），需与对接系统一致。
- **PKCS5** 与 **PKCS7** 在本库 AES 实现中等价（块长 16）。
- **ZeroPadding**：明文末尾补 `0x00` 至块对齐；解密会去掉尾部连续 `0x00`（若明文本身以二进制 0 结尾，可能与填充混淆，需谨慎）。

### 5. SM4（`SM4Encrypt` / `SM4Decrypt`）

| 参数 | 填写说明 |
|------|----------|
| `str` | 明文（加密）或密文（解密） |
| `key` | 密钥材料；与 `keyType` 一起决定 16 字节密钥：不足末尾补 `0x00`，超过取前 16 字节 |
| `retType`（加密） | 密文输出形式：`BASE64` 或 `HEX`（大小写不敏感） |
| `textType`（解密） | 密文输入形式：`BASE64` 或 `HEX` |
| `keyType` | `TEXT`：UTF-8 字节；`HEX`：十六进制；`BASE64`：标准 Base64；`BASE64URLSAFE`：URL 安全 Base64 先转标准再解码 |
| `padding` | `PKCS7`（与常见 PKCS5 称呼互通）或 `ZERO` |
| `mode` | `ECB` 或 `CBC`；拼写 `EBC` 也会被当作 ECB |
| `iv` | 仅 **CBC** 需要；IV 与密钥使用**同一套** `keyType` 规则解析为 16 字节 |

### 6. 原生 RSA（`RSAEncrypt` / `RSADecrypt`）

- `plainText` / `cipherText`：明文或 Base64 密文（见实现）。
- `certPath` / `privatePath`：服务器上 **IRIS 进程可读** 的 **PEM 公钥/私钥文件绝对路径**。

### 7. Java 网关加密（方法名以 `Java` 开头）

- **`keyType`**：依方法而定。RSA 类可为 **`base64`**、**`hex`** 或 **`pemFile`**（公钥/私钥为磁盘上 PEM 文件路径）；对称类多为 `text` / `hex` / `base64` 等，**以网关接口约定为准**，会原样传入 HTTP 请求体。
- **`outputFormat`**：密文或签名输出格式，一般为 `hex` 或 `base64`（非法值会回退到实现中的默认）。
- **`encoding`**（RSA 加解密）：**`1`** = OAEP，**`2`** = PKCS1Padding（默认 **2**）。

### 8. 编码类 `Util.EncodingUtils`

| 参数 | 说明 |
|------|------|
| `str` / `content` | 待编码或解码的字符串 |
| `hex`（`HexDecode`） | 仅含十六进制字符的串，解码为字节后按 UTF-8 还原 |
| `ConvertUrl(str, type)` | `type` 默认 **`equal`**：把 Base64 的 `+/=` 换成 URL 安全形式；其它取值见 `Util.Impl.EncodingUtils.ConvertUrl` |

Base64 / Base32 等：编码前明文会按 **UTF-8** 转字节；`UnicodeEncode` / `UnicodeDecode` 为类 URL/JS 风格的 Unicode 转义。

---

# 一、加密方法

# IRIS 原生加密

## 1. MD5

校验网站：https://www.jyshare.com/crypto/md5/

### MD5Encrypt

- 描述：MD5 摘要统一输出。
- 参数：`str` 明文，`outputType(hex/base64/raw)`
- 返回值：按 `outputType` 输出的 MD5 摘要
```objectscript
ClassMethod MD5Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).MD5Encrypt("1a姚鑫！.","hex")
a1f4cc2b0615babeba2a72f5cc4819c1
```
```
DHC-APP>w ##class(Util.EncryptionUtils).MD5Encrypt("1a姚鑫！.","base64")
ofTMKwYVur66KnL1zEgZwQ==
```
```
DHC-APP>w ##class(Util.EncryptionUtils).MD5Encrypt("1a姚鑫！.","raw")   
¡ôÌ+º¾º*rõÌHÁ
```

### HMACMD5Encrypt
- 描述：HMAC-MD5。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：HMAC 结果
```objectscript
ClassMethod HMACMD5Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACMD5Encrypt("1ab姚鑫@#！", "1234567890", "text", "hex")
9b03630a896fda4b376391d7572f2709
```

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACMD5Encrypt("1ab姚鑫@#！", "1234567890", "text", "base64")
mwNjColv2ks3Y5HXVy8nCQ==
```

## 2. SHA

校验网站：https://www.jyshare.com/crypto/sha1/

### SHA1Encrypt

- 描述：SHA1 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SHA1Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SHA1Encrypt("1ab姚鑫@#！")
33c10475eb88ea8edb81d55cc683243caec06132
```

### SHA224Encrypt
- 描述：SHA224 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SHA224Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SHA224Encrypt("1ab姚鑫@#！")
024fc87a6a6b5e10d5dd556da6996bd8b9c7e881d9c02ef6b3bd6a84
```

### SHA256Encrypt
- 描述：SHA256 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SHA256Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SHA256Encrypt("1ab姚鑫@#！")
9e93e1e74f3c296f8c8a7911827611d67fa81125451b75a52393dd5b60af9a96
```

### SHA384Encrypt
- 描述：SHA384 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SHA384Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SHA384Encrypt("1ab姚鑫@#！")
3b1e2e0e7bf956a303f43668b536bbc4bb15872228a58b06ce74b54c74cb6bf7ea6e75ce01f67b22d37e0f24e5600f6e
```

### SHA512Encrypt
- 描述：SHA512 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SHA512Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SHA512Encrypt("1ab姚鑫@#！")
bf718aaf7018a5e2f914825ae805cd37775e8d524407369bd28b034c61e3d33fc8e1dc7784bb73eaf2db2e1042bccb340c63415d52b2df80f3986904b37b6d2a
```

### HMACSHA1Encrypt
- 描述：HMAC-SHA1。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod HMACSHA1Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSHA1Encrypt("1ab姚鑫@#！",1234567890, "text", "hex")
ac34ef896f5987c91b35ff6b047d65af04defc02
```

### HMACSHA224Encrypt
- 描述：HMAC-SHA224。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod HMACSHA224Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSHA224Encrypt("1ab姚鑫@#！",1234567890, "text", "hex")
2b22b6528c540792db696b8c4a3d30fc35b83e23b832be8527cb5128
```

### HMACSHA256Encrypt
- 描述：HMAC-SHA256。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod HMACSHA256Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSHA256Encrypt("1ab姚鑫@#！",1234567890, "text", "hex")
67ec05376fc6cc719216ebe6a3ec6b7f73001b3b8a6f451f454066e58b9a04e7
```

### HMACSHA384Encrypt
- 描述：HMAC-SHA384。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod HMACSHA384Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSHA384Encrypt("1ab姚鑫@#！",1234567890, "text", "hex")
96ff10ed4b67429320acfb9fcee811d4754055c2dfe82edddab5da99f9cc7843c46ad2a83e90a80957ad2c465addf283
```

### HMACSHA512Encrypt
- 描述：HMAC-SHA512。
- 参数：`str`，`key`，`keyType(text/base64/hex)`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod HMACSHA512Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
调试示例：
```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSHA512Encrypt("1ab姚鑫@#！",1234567890, "text", "hex")
34c28fc5d47df816c546494dee1b4b4ae4a1cd815805bd7f7938a473c75ee8891bb847111afc1fa65a3c8e0c726dd1e676d16be1c849065f767ae780a4d00fba
```

## 3. AES

校验网站：http://tool.chacuo.net/cryptaes

### AESECBZeroPaddingEncrypt
- 描述：AES ECB ZeroPadding 加密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(base64/raw/hex)`
- 返回值：密文
```objectscript
ClassMethod AESECBZeroPaddingEncrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "base64") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBZeroPaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "base64")
e7mwJvE9+OgKf/IHdTthsA==
```

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBZeroPaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "hex")   
7bb9b026f13df8e80a7ff207753b61b0
```

### AESECBZeroPaddingDecrypt
- 描述：AES ECB ZeroPadding 解密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(兼容参数)`,`cipherType(base64/raw/hex, 优先)`
- 返回值：明文
```objectscript
ClassMethod AESECBZeroPaddingDecrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "base64", cipherType As %String = "") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBZeroPaddingDecrypt("e7mwJvE9+OgKf/IHdTthsA==","J#y9sJesv*512345","text", "base64", "base64")
1ab姚鑫@#！
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBZeroPaddingDecrypt("7bb9b026f13df8e80a7ff207753b61b0","J#y9sJesv*512345","text","hex", "hex")
1ab姚鑫@#！
```

### AESCBCPKCS7PaddingEncrypt
- 描述：AES CBC PKCS7 加密。
- 参数：`str`,`key`,`iv`,`keyType(text/base64/hex)`,`ivType(text/base64/hex)`,`outputType(base64/raw/hex)`
- 返回值：密文
```objectscript
ClassMethod AESCBCPKCS7PaddingEncrypt(str As %String, key As %String, iv As %String, keyType As %String = "text", ivType As %String = "text", outputType As %String = "base64") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS7PaddingEncrypt("1ab姚鑫@#！", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "base64")
6Y8NmJMsagLOsgErqcYmkw==
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS7PaddingEncrypt("1ab姚鑫@#！", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "hex")   
e98f0d98932c6a02ceb2012ba9c62693
```

### AESCBCPKCS7PaddingDecrypt
- 描述：AES CBC PKCS7 解密。
- 参数：`str`,`key`,`iv`,`keyType(text/base64/hex)`,`ivType(text/base64/hex)`,`outputType(兼容参数)`,`cipherType(base64/raw/hex, 优先)`
- 返回值：明文
```objectscript
ClassMethod AESCBCPKCS7PaddingDecrypt(str As %String, key As %String, iv As %String, keyType As %String = "text", ivType As %String = "text", outputType As %String = "base64", cipherType As %String = "") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS7PaddingDecrypt("e98f0d98932c6a02ceb2012ba9c62693", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "hex", "hex")
1ab姚鑫@#！
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS7PaddingDecrypt("6Y8NmJMsagLOsgErqcYmkw==", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "base64", "base64")
1ab姚鑫@#！
```

### AESCBCPKCS5PaddingEncrypt
- 描述：AES CBC PKCS5 加密。
- 参数：`str`,`key`,`iv`,`keyType(text/base64/hex)`,`ivType(text/base64/hex)`,`outputType(base64/raw/hex)`
- 返回值：密文
```objectscript
ClassMethod AESCBCPKCS5PaddingEncrypt(str As %String, key As %String, iv As %String, keyType As %String = "text", ivType As %String = "text", outputType As %String = "base64") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS5PaddingEncrypt("1ab姚鑫@#！", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "base64")
6Y8NmJMsagLOsgErqcYmkw==
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS5PaddingEncrypt("1ab姚鑫@#！", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "hex")
e98f0d98932c6a02ceb2012ba9c62693
```

### AESCBCPKCS5PaddingDecrypt
- 描述：AES CBC PKCS5 解密。
- 参数：`str`,`key`,`iv`,`keyType(text/base64/hex)`,`ivType(text/base64/hex)`,`outputType(兼容参数)`,`cipherType(base64/raw/hex, 优先)`
- 返回值：明文
```objectscript
ClassMethod AESCBCPKCS5PaddingDecrypt(str As %String, key As %String, iv As %String, keyType As %String = "text", ivType As %String = "text", outputType As %String = "base64", cipherType As %String = "") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS5PaddingDecrypt("6Y8NmJMsagLOsgErqcYmkw==", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "base64", "base64")
1ab姚鑫@#！
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESCBCPKCS5PaddingDecrypt("e98f0d98932c6a02ceb2012ba9c62693", "J#y9sJesv*512345", "J#y9sJesv*512345","text", "text", "hex", "hex")
1ab姚鑫@#！
```

### AESECBPKCS5PaddingEncrypt
- 描述：AES ECB PKCS5 加密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(base64/raw/hex)`
- 返回值：密文
```objectscript
ClassMethod AESECBPKCS5PaddingEncrypt(str As %String, key As %String = "", keyType As %String = "text", outputType As %String = "base64") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "base64")
ROvjltaCFBS5thrSSqkd+g==
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "hex")
44ebe396d6821414b9b61ad24aa91dfa
```

### AESECBPKCS5PaddingDecrypt

- 描述：AES ECB PKCS5 解密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(兼容参数)`,`cipherType(base64/raw/hex, 优先)`
- 返回值：明文

```objectscript
ClassMethod AESECBPKCS5PaddingDecrypt(str As %String, key As %String = "", keyType As %String = "text", outputType As %String = "base64", cipherType As %String = "") As %String
```

- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingDecrypt("ROvjltaCFBS5thrSSqkd+g==","J#y9sJesv*512345","text", "base64", "base64")
1ab姚鑫@#！
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingDecrypt("44ebe396d6821414b9b61ad24aa91dfa","J#y9sJesv*512345","text", "hex", "hex")
1ab姚鑫@#！
```

### AESECBPKCS7PaddingEncrypt

- 描述：AES ECB PKCS7 加密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(base64/raw/hex)`
- 返回值：密文

```objectscript
ClassMethod AESECBPKCS7PaddingEncrypt(str As %String, key As %String = "", keyType As %String = "text", outputType As %String = "base64") As %String
```

- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS7PaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "base64")
ROvjltaCFBS5thrSSqkd+g==
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS7PaddingEncrypt("1ab姚鑫@#！","J#y9sJesv*512345","text", "hex")
44ebe396d6821414b9b61ad24aa91dfa
```

### AESECBPKCS7PaddingDecrypt

- 描述：AES ECB PKCS7 解密。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(兼容参数)`,`cipherType(base64/raw/hex, 优先)`
- 返回值：明文

```objectscript
ClassMethod AESECBPKCS7PaddingDecrypt(str As %String, key As %String = "", keyType As %String = "text", outputType As %String = "base64", cipherType As %String = "") As %String
```

- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS7PaddingDecrypt("ROvjltaCFBS5thrSSqkd+g==","J#y9sJesv*512345","text", "base64", "base64")
1ab姚鑫@#！
```
```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS7PaddingDecrypt("44ebe396d6821414b9b61ad24aa91dfa","J#y9sJesv*512345","text", "hex", "hex")
1ab姚鑫@#！

```

### AESECBPKCS5PaddingEncryptStream

- 描述：AES ECB PKCS5 加密（流输出）。
- 参数：`str`,`key`,`keyType(text/base64/hex)`
- 返回值：`%Stream.GlobalBinary`
```objectscript
ClassMethod AESECBPKCS5PaddingEncryptStream(str As %String, key As %String = "", keyType As %String = "text") As %Stream.GlobalBinary
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingEncryptStream("1ab姚鑫@#！","J#y9sJesv*512345").Read()
ROvjltaCFBS5thrSSqkd+g==
```

### AESECBPKCS5PaddingDecryptStream
- 描述：AES ECB PKCS5 解密（流输入）。
- 参数：`stream`,`key`,`keyType(text/base64/hex)`
- 返回值：明文
```objectscript
ClassMethod AESECBPKCS5PaddingDecryptStream(stream As %Stream.GlobalBinary, key As %String = "", keyType As %String = "text") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).AESECBPKCS5PaddingDecryptStream(##class(Util.EncryptionUtils).AESECBPKCS5PaddingEncryptStream("1ab姚鑫@#！","J#y9sJesv*512345"),"J#y9sJesv*512345")
1ab姚鑫@#！
```

## 4. SM3

校验网站：https://tool.hiofd.com/sm3-online/

### SM3Encrypt
- 描述：SM3 摘要。
- 参数：`str`，`outputType(hex/base64/raw)`
- 返回值：摘要
```objectscript
ClassMethod SM3Encrypt(str As %String, outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).SM3Encrypt("1ab姚鑫@#！","hex")
a13f3300653d28a97d41b66540a4546ffe0e708fcc9b38de3be1d938b9fa85ff
```
```
DHC-APP>w ##class(Util.EncryptionUtils).SM3Encrypt("1ab姚鑫@#！","base64")
oT8zAGU9KKl9QbZlQKRUb/4OcI/MmzjeO+HZOLn6hf8=
```

### HMACSM3Encrypt

校验网站：https://www.easydebug.net/encryption-sm

- 描述：HMAC-SM3。
- 参数：`str`,`key`,`keyType(text/base64/hex)`,`outputType(hex/base64/raw)`
- 返回值：摘要
- 说明：第三个参数是 **`keyType`**（密钥如何解码），不是输出格式。下例第三参数为 **`hex`**，表示把密钥串 `"1234567890"` 按十六进制解码为字节再 HMAC；若密钥是日常字符串，应使用 **`text`**，摘要会与下例不同。
```objectscript
ClassMethod HMACSM3Encrypt(str As %String, key As %String, keyType As %String = "text", outputType As %String = "hex") As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSM3Encrypt("1ab姚鑫@#！","1234567890","hex", "hex")
8f7440535c3c8954221f4bed3d5fbfb8cfed04be223d1b414a9eaaa0ec03587e
```
```
DHC-APP>w ##class(Util.EncryptionUtils).HMACSM3Encrypt("1ab姚鑫@#！",1234567890,"hex", "base64")
j3RAU1w8iVQiH0vtPV+/uM/tBL4iPRtBSp6qoOwDWH4=
```

## 5. SM4

校验网站：https://tool.hiofd.com/sm4-encrypt-online/

### SM4Encrypt

- 描述：SM4 加密。
- 参数：`str`,`key`,`retType`,`keyType`,`padding`,`mode`,`iv`,`ivType`（各参数取值见 **「参数约定与填写说明」→ 5. SM4**）
- 返回值：密文
```objectscript
ClassMethod SM4Encrypt(str As %String, key As %String, retType As %String = "BASE64", keyType As %String = "TEXT", padding = "PKCS7", mode As %String = "ECB", iv As %String = "") As %String
```
- 调试示例：

SM4 - ECB - PKCS7 密钥类型为HEX输出结果为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","975C7238C3824FB0975C7238C3824FB0","Hex","HEX","PKCS7","ECB")
2D123303571BA4C5A15A1692EDB13783
```

SM4 - ECB - PKCS7 密钥类型为HEX输出结果为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","975C7238C3824FB0975C7238C3824FB0","BASE64","HEX","PKCS7","ECB")
LRIzA1cbpMWhWhaS7bE3gw==
```

SM4 - ECB - PKCS7 密钥类型为TEXT输出结果为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","975C7238C3824FB0","HEX","TEXT","PKCS7","ECB")
7AD4159325EB143C2E7338CD42514EA3
```

SM4 - ECB - PKCS7 密钥类型为TEXT输出结果为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","975C7238C3824FB0","base64","TEXT","PKCS7","ECB")
etQVkyXrFDwuczjNQlFOow==
```

SM4 - ECB - PKCS7 密钥类型为BASE64输出结果为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","TvaBgrhE46sft3nZlfe7xw==","base64","base64","PKCS7","ECB")
d9cw5sCuUtZLZSD60y9bjw==
```

SM4 - ECB - ZERO 密钥类型为BASE64输出结果为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","Rl+LAwAmOfgJjzcfo8hXfw==","BASE64","BASE64","ZERO","ECB")
zu12rx3u213lw3GNaGvJBw==
```

SM4 - CBC - PKCS7 密钥类型为TEXT IV 类型为 TEXT 输出结果为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Encrypt("1ab姚鑫@#！","975C7238C3824FB0","HEX","TEXT","PKCS7","CBC","975C7238C3824FB0")
ACBC8301E0964705A55EA3E077B8EC8A
```

### SM4Decrypt
- 描述：SM4 解密。
- 参数：`str`（密文）,`key`,`textType`（密文编码）,`keyType`,`padding`,`mode`,`iv`,`ivType`（同上节；`textType` 对应加密侧 `retType`）
- 返回值：明文
```objectscript
ClassMethod SM4Decrypt(str As %String, key As %String, textType As %String = "BASE64", keyType As %String = "TEXT", padding = "PKCS7", mode As %String = "ECB", iv As %String = "") As %String

```
- 调试示例：

SM4 - ECB - PKCS7 密钥类型为HEX密文为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Decrypt("2D123303571BA4C5A15A1692EDB13783", "975C7238C3824FB0975C7238C3824FB0","HEX","HEX","PKCS7","ECB")
1ab姚鑫@#！
```

SM4 - ECB - PKCS7 密钥类型为TEXT密文为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Decrypt("7AD4159325EB143C2E7338CD42514EA3","975C7238C3824FB0","HEX","TEXT","PKCS7","ECB")
1ab姚鑫@#！
```

SM4 - ECB - PKCS7 密钥类型为BASE64密文为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Decrypt("d9cw5sCuUtZLZSD60y9bjw==","TvaBgrhE46sft3nZlfe7xw==","base64","base64","PKCS7","ECB")
1ab姚鑫@#！
```

SM4 - ECB - ZERO 密钥类型为BASE64密文为BASE64

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Decrypt("zu12rx3u213lw3GNaGvJBw==","Rl+LAwAmOfgJjzcfo8hXfw==","BASE64","BASE64","ZERO","ECB")
1ab姚鑫@#！
```

SM4 - CBC - PKCS7 密钥类型为TEXT IV 类型为 TEXT 密文为HEX

```
DHC-APP>w ##class(Util.EncryptionUtils).SM4Decrypt("ACBC8301E0964705A55EA3E077B8EC8A","975C7238C3824FB0","HEX","TEXT","PKCS7","CBC","975C7238C3824FB0")
1ab姚鑫@#！
```

## 6. RSA

### RSAEncrypt
- 描述：RSA 公钥证书加密。
- 参数：`plainText`,`certPath`
- 返回值：密文
```objectscript
ClassMethod RSAEncrypt(plainText As %String, certPath As %String) As %String
```
- 调试示例：`w ##class(Util.EncryptionUtils).RSAEncrypt("姚鑫", "E:\m\key\public-key.pem")`

### RSADecrypt
- 描述：RSA 私钥解密。
- 参数：`cipherText`,`privatePath`
- 返回值：明文
```objectscript
ClassMethod RSADecrypt(cipherText As %String, privatePath As %String) As %String
```
- 调试示例：`w ##class(Util.EncryptionUtils).RSADecrypt("skpyEtXjiLyY8noaW22Kh5udr/7GvnMTUj/4U3mLo9CK4OP8wrU86jfKSVPeJIH3rGm6keyaHGwE8S4GFbXzbFK0E4RlAm/l0smsEyBwiPTWfsKNYVIfGb+ptFMYGkjuTmaNbt7Mvq14B7HEjcYvrNbw/5LfeKdXrqnc/TRWNws=", "E:\m\key\private-key.pem")`

## 7. DES

### DESEncrypt

- 描述：DES加密
- 参数：`plain - 明文,key - 密钥,keyType - 密钥类型(hex,base64,text) - Base64串,mode - (ECB,CBC),padding - 填充类型(PKCS7,PKCS5,ZERO) outputFormat - 结果(hex,base64) , iv - CBC使用，ivType - (hex,base64,text)`
- 返回值：密文

```objectscript
ClassMethod DESEncrypt(plain As %String, key As %String, keyType As %String = "text", mode As %String = "ECB", padding As %String = "PKCS7", outputFormat As %String = "base64", iv As %String = "", ivType As %String = "text") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).DESEncrypt("1ab姚鑫@#！","1234567890","text   ","ECB","PKCS7","hex")
2afa3ebe6f6949c351965ac257c2cc90
```

### DESDecrypt

- 描述：DES解密
- 参数：`cipher - 密文,key - 密钥,keyType - 密钥类型(hex,base64,text) - Base64串,mode - (ECB,CBC),padding - 填充类型(PKCS7,PKCS5,ZERO) outputFormat - 结果(hex,base64) , iv - CBC使用，ivType - (hex,base64,text`)
- 返回值：明文

```objectscript
ClassMethod DESDecrypt(cipher As %String, key As %String, keyType As %String = "text", mode As %String = "ECB", padding As %String = "PKCS7", cipherType As %String = "base64", iv As %String = "", ivType As %String = "text") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).DESDecrypt("2afa3ebe6f6949c351965ac257c2cc90","1234567890","text","ECB","PKCS7","hex")
1ab姚鑫@#！
```

## 8. 3DES

### 3DESEncrypt

- 描述：3DES加密
- 参数：`plain - 明文,key - 密钥,keyType - 密钥类型(hex,base64,text) - Base64串,mode - (ECB,CBC),padding - 填充类型(PKCS7,PKCS5,ZERO) outputFormat - 结果(hex,base64) , iv - CBC使用，ivType - (hex,base64,text`)
- 返回值：密文

```objectscript
ClassMethod DesedeEncrypt(plain As %String, key As %String, keyType As %String = "text", mode As %String = "ECB", padding As %String = "PKCS7", outputFormat As %String = "base64", iv As %String = "", ivType As %String = "text", keyMode As %String = "strict") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).DesedeEncrypt("1ab姚鑫@#！","12345678123456   78","text","ECB","PKCS7","hex")
2afa3ebe6f6949c351965ac257c2cc90
```

### 3DESDecrypt

- 描述：3DES解密
- 参数：`cipher - 密文,key - 密钥,keyType - 密钥类型(hex,base64,text) - Base64串,mode - (ECB,CBC),padding - 填充类型(PKCS7,PKCS5,ZERO) outputFormat - 结果(hex,base64) , iv - CBC使用，ivType - (hex,base64,text`)
- 返回值：明文

```objectscript
ClassMethod DesedeDecrypt(cipher As %String, key As %String, keyType As %String = "text", mode As %String = "ECB", padding As %String = "PKCS7", cipherType As %String = "base64", iv As %String = "", ivType As %String = "text", keyMode As %String = "strict") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).DesedeDecrypt("2afa3ebe6f6949c351965ac257c2cc90","1234567812345678","text","ECB","PKCS7","hex")
1ab姚鑫@#！
```

## 9. 奇偶加密

### OddEvenEncrypt
- 描述：奇偶位交换加密。
- 参数：`plainText`
- 返回值：密文
```objectscript
ClassMethod OddEvenEncrypt(plainText As %String) As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).OddEvenEncrypt("ab2")
900789500
```

### OddEvenDecrypt
- 描述：奇偶位交换解密。
- 参数：`cipherText`
- 返回值：明文
```objectscript
ClassMethod OddEvenDecrypt(cipherText As %String) As %String
```
- 调试示例：

```
DHC-APP>w ##class(Util.EncryptionUtils).OddEvenDecrypt("900789501091")
ab2w
```

# JAVA 加密服务 - 标准加密

## 1. SM2

### JavaSm2Sign

校验网站：https://tool.hiofd.com/sm2-sign-verify/

- 描述：Java SM2 签名。
- 参数：`content`,`privateKey`,`isResultHex`,`isKeyHex`
- 返回值：签名
```objectscript
ClassMethod JavaSm2Sign(content As %String, privateKey As %String, isResultHex As %String = 1, isKeyHex As %String = 1) As %String
```
- 调试示例：

```
w ##class(Util.EncryptionUtils).JavaSm2Sign("1ab姚鑫@#！","a99d6978656   355b570a19cc707b2ea68ed033c3fa436df9c0a15e84a06069c7b")
304402207627bfb7e7bdef7de087c0a64fd8e179c10df18548294d72fdf089c8e85e42740220724ea448f5242a79f4c127fc6a153372f824f275822f0d62564e3cf8c0006775
```

### JavaSm2Verify
- 描述：Java SM2 验签。
- 参数：`content`,`publicKey`,`sign`,`isResultHex`,`isKeyHex`
- 返回值：布尔值
```objectscript
ClassMethod JavaSm2Verify(content As %String, publicKey As %String, sign As %String, isResultHex As %String = 1, isKeyHex As %String = 1) As %Boolean
```
- 调试示例：

```
w ##class(Util.EncryptionUtils).JavaSm2Verify("1ab姚鑫@#！","04b674b6e   dfd08f754410b21cc3c8b522f318a1f1b27403bc63a290b7e1790d03d967d06c46e69357793967ff42a34d77ed5d7bb40d31b7f5ab0b0840017cff114","3044022016fb0f645bbc0ba9fbeacb3dfb7ba20a7c629e64183149d8a43b6ece471faba802202dcfd47e72fc45011eb280f328312f78aaad48d499e648fc8ba7b48844f0a236")
1
```

### JavaSm2Encrypt

校验网站：https://tool.hiofd.com/sm2-encrypt-online/

- 描述：Java SM2 公钥加密。
- 参数：`content`,`publicKey`,`mode`,`isResultHex`,`isKeyHex`
- 返回值：密文
```objectscript
ClassMethod JavaSm2Encrypt(content As %String, publicKey As %String, mode As %String = "C1C3C2", isResultHex As %String = 1, isKeyHex As %String = 1) As %String
```
- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm2Encrypt("1ab姚鑫@#！","04b674b6   edfd08f754410b21cc3c8b522f318a1f1b27403bc63a290b7e1790d03d967d06c46e69357793967ff42a34d77ed5d7bb40d31b7f5ab0b0840017cff114","C1C3C2",1,1)
04f988b4bd8bbfc9691b175a246fbbb87d36ff5d23519a9944da822e508d5891f38d0529686e664fe263d46ff28ce7fcf771f3b5432d4ca50315a428c48bc37cf37bcbc8bf4c6a6e83911c8b9f09d8b7a48e2540004df102cfb7b9ddfea788f96c0cd5902e3c3761a8429426c87e0e
```

### JavaSm2Encrypt4PKCS8
- 描述：Java SM2 PKCS8 公钥加密。
- 参数：`content`,`publicKey`,`mode`,`isResultHex`
- 返回值：密文
```objectscript
ClassMethod JavaSm2Encrypt4PKCS8(content As %String, publicKey As %String, mode As %String = "C1C3C2", isResultHex As %String = 0) As %String
```
- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm2Encrypt4PKCS8("1ab姚鑫@#！","LS   0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQk16Q0I3QVlIS29aSXpqMENBVENCNEFJQkFUQXNCZ2NxaGtqT1BRRUJBaUVBLy8vLy92Ly8vLy8vLy8vLwovLy8vLy8vLy8vOEFBQUFBLy8vLy8vLy8vLzh3UkFRZy8vLy8vdi8vLy8vLy8vLy8vLy8vLy8vLy8vOEFBQUFBCi8vLy8vLy8vLy93RUlDanArcDZkbjE0MFRWcWVTODlsQ2Fmemw0bjFGYXVQa3QyOHZVRk5sQTZUQkVFRU1zU3UKTEI4WmdSbGZtUVJHYWpuSmxJL2pDNy95Wmd2aGNWcEZpVE5NZE1lOE56YWk5UFozbkZtOXp1TnJhU0ZUMEttSApmTVlxUjBBQzN6TGxJVG53b0FJaEFQLy8vLzcvLy8vLy8vLy8vLy8vLy85eUE5OXJJY1lGSzFPNzlBazUxVUVqCkFnRUJBMElBQkt2NnhzTkN2QUNteGd1SGREOWx1WVc1Q25RbFVmazcyM1JxYjRMNUpua21rTzJNQTNTSmNwNm8KMS9FRzM4ZWk5S2duRXFiV2lTcktHODJYbExTTi9KVT0KLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg","C1C2C3",0)
BPXhrYOz0DjkEc1T9eikPiWT1hHSjYZl16kuQtKddGFOVNicDNVbvdzqa9sYePXg6UKHV9Y8e220oz/aK0indAggQYZ0QiyovLdQIUN4SfxjUIgc78zTfIG76jL2bCO6J3FdXsOhS/lL7SG1BuaG
```

### JavaSm2Decrypt
- 描述：Java SM2 私钥解密。
- 参数：`cipherText`,`privateKey`,`mode`,`isResultHex`,`isKeyHex`
- 返回值：明文
```objectscript
ClassMethod JavaSm2Decrypt(cipherText As %String, privateKey As %String, mode As %String = "C1C3C2", isResultHex As %String = 1, isKeyHex As %String = 1) As %String
```
- 调试示例：

```
w ##class(Util.Impl.EncryptionUtils).JavaSm2Decrypt("042d2e5e82795458f4599a377bd08b758de09431d1352c2ff461d5c6cac037c75b4afa2b875ed028ff762a2b901065d617eef788149cd6324cf5089c674d7c4595dededad2603cdfac80bf17ff4d03e8ce50f0ee5334282976e8481c2476a6369252657f3cfde02b3b43b34e8ccff9","a99d6978656355b570a19cc707b2ea68ed033c3fa436df9c0a15e84a06069c7b")
1ab姚鑫@#！
```

## 2. SM3

### JavaSm3Encrypt

校验网站：https://tool.hiofd.com/sm3-online/

- 描述：Java SM3 摘要。
- 参数：`plainText`,`isHexOutput`
- 返回值：摘要
```objectscript
ClassMethod JavaSm3Encrypt(plainText As %String, isHexOutput As %Boolean = 1) As %String
```
- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm3Encrypt("Hello World")
77015816143ee627f4fa410b6dad2bdb9fcbdf1e061a452a686b8711a484c5d7
```

### JavaSm3Hmac

校验网站：https://www.easydebug.net/encryption-sm

- 描述：Java SM3-HMAC。
- 参数：`data`,`key`,`isHexOutput`
- 返回值：摘要
```objectscript
ClassMethod JavaSm3Hmac(data As %String, key As %String, isHexOutput As %Boolean = 1) As %String
```
- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm3Hmac("Hello World", "mySecretKey123456")
MI/1ELLsN8gJbGFnZLjIGM6ergxswdKCDyhT7ZWyY/g=
```

### JavaSm3VerifyHmac
- 描述：Java SM3-HMAC 验证。
- 参数：`data`,`key`,`expectedHmac`,`isHexOutput`
- 返回值：验证结果
```objectscript
ClassMethod JavaSm3VerifyHmac(data As %String, key As %String, expectedHmac As %String, isHexOutput As %Boolean = 1) As %String
```
- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm3VerifyHmac("Hello World", "mySecretKey123456","MI/1ELLsN8gJbGFnZLjIGM6ergxswdKCDyhT7ZWyY/g=")
true
```

## 3. SM4

校验网站：https://tool.hiofd.com/sm4-encrypt-online/

### JavaSm4CbcZeroEncrypt

- 描述：Java SM4 CBC Zero 加密。
- 参数：`plainText`,`keyStr`,`ivStr`,`keyType`,`outputFormat`
- 返回值：密文

```objectscript
ClassMethod JavaSm4CbcZeroEncrypt(plainText As %String, keyStr As %String, ivStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm4CbcZeroEncrypt("1ab姚鑫@#！","b   e82c53a6c452f2e","gatherdataeworld")
32e4c73ebdfed40a6ffd6f8fc11d86d7
```

### JavaSm4CbcZeroDecrypt

- 描述：Java SM4 CBC Zero 解密。
- 参数：`cipherText`,`keyStr`,`ivStr`,`keyType`,`outputFormat`
- 返回值：明文

```objectscript
ClassMethod JavaSm4CbcZeroDecrypt(cipherText As %String, keyStr As %String, ivStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.Impl.EncryptionUtils).JavaSm4CbcZeroDecrypt("32e4c73ebdfed40a6ffd6f8fc11d86d7","be82c53a6c452f2e","gatherdataeworld")
1ab姚鑫@#！
```

### JavaSm4EcbPkcs5Encrypt

- 描述：Java SM4 ECB PKCS5 加密。
- 参数：`plainText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：密文

```objectscript
ClassMethod JavaSm4EcbPkcs5Encrypt(plainText As %String, keyStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm4EcbPkcs5Encrypt("1ab姚鑫@#！","975C7   238C3824FB0")
7ad4159325eb143c2e7338cd42514ea3
```

### JavaSm4EcbPkcs5Decrypt

- 描述：Java SM4 ECB PKCS5 解密。
- 参数：`cipherText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：明文

```objectscript
ClassMethod JavaSm4EcbPkcs5Decrypt(cipherText As %String, keyStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm4EcbPkcs5Decrypt("7ad4159325eb143c2e7338cd42514ea3","975C7238C3824FB0")
1ab姚鑫@#！
```

### JavaSm4EcbPkcs7Encrypt

- 描述：Java SM4 ECB PKCS7 加密。
- 参数：`plainText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：密文

```objectscript
ClassMethod JavaSm4EcbPkcs7Encrypt(plainText As %String, keyStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm4EcbPkcs7Encrypt("1ab姚鑫@#！","975C7   238C3824FB0")
7ad4159325eb143c2e7338cd42514ea3
```

### JavaSm4EcbPkcs7Decrypt

- 描述：Java SM4 ECB PKCS7 解密。
- 参数：`cipherText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：明文

```objectscript
ClassMethod JavaSm4EcbPkc7Decrypt(cipherText As %String, keyStr As %String, keyType As %String = "text", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm4EcbPkcs7Decrypt("7ad4159325eb143c2e7338cd42514ea3","975C7238C3824FB0")
1ab姚鑫@#！
```

## 4. RSA

### JavaRsaEncryptByPublicKey

- 描述：Java RSA 公钥加密（支持 pemFile / encoding）。
- 参数：`content`,`publicKey`,`keyType`,`outputFormat`,`encoding`
- 返回值：密文

```objectscript
ClassMethod JavaRsaEncryptByPublicKey(content As %String, publicKey As %String, keyType As %String = "base64", outputFormat As %String = "base64", encoding As %Integer = 2) As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaRsaEncryptByPublicKey("1ab姚鑫@#！","MI   IBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAhZuxSMZ2WkQBkJL183S3lcefQBL1Yor/fS0kQqllcOWSUTPGa+MAdnUtA5K+QheYNURMKxJfRout6qUHijXybnh4g4LEtDhOyn7YppEd9EtzeorYdUCplYFgEN+MEAV9gqH9xp0fhHet7w8aAfVuBU2VVIZ3dKeaSZepSbmXaSlEMQ3kE1+mdVv63SW1PyAS7m8v0rKtP7yu97227SK9M2djjqu8MYNFMHNrxv2VWOklrsllUzV4/jWxa9bQqH91/ws9kmOKYdMSKpzhKcnAa6LJroHHZbRXPIPn+zcPunRzoPg1sAdmRJ+dgZiB6dgVwdgMZCYmsc3+LcKYKjyXNwIDAQAB")
FS/WyGnOeEDaiYvFEIuXVtlRPpK5pF56SjOnG37Gqu8OxvH9n48knCmZ6hMMy43GNymBhUhz8lK/+SQSIgE1QNDBVdyGlWA4OJ5xeJFqyvZ4cdMgZDUpZrUKw0EvsRAu9sJ5Gh1g516WzSHzGFXh2FauXFAjrlUlO7yD4uG84ACaAel4Tb0pm6BgsxNGNI88InjN1nq5r+Dyw4Rt4CpcuNgfcobni9oYlPSfiaV5qHrQtr/tIHTRtU0opgon/9wRK8H027F3ksWnH9d8q7H+sJMFkFixQCakWkCsaGmjustZW9TnbwcBVS8/vn0ijdDxYIlYpYMT0VosvhPFncBmow==
```

### JavaRsaDecryptByPrivateKey

- 描述：Java RSA 私钥解密（支持 pemFile / encoding）。
- 参数：`content`,`privateKey`,`keyType`,`outputFormat`,`encoding`
- 返回值：明文

```objectscript
ClassMethod JavaRsaDecryptByPrivateKey(content As %String, privateKey As %String, keyType As %String = "base64", outputFormat As %String = "base64", encoding As %Integer = 2) As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaRsaDecryptByPrivateKey("Dr3B3lk02lXnQAjyArby8ZX5Pbj90/PrHFZdqxeN5nLQXYVJYaD7JSd5Am5y43Jhh6yPqiUHnNAscKCUcIOW60kyT0wBm1lKgbGP2e0fb8U6IHrQarIPPd3dlLMj4pHCN9JwP1Jm+Tb9T/e5xmEkLrZpC3RfEf/NvSl6IdGXzPjQcrwgY8U6+WgwPtUPIezt0ujJwdW1ZmFUq7hO2qpGHyX9z/0phCW7zPYV3np+PRxl/Dqxep3EkLNQKRf1ihPLerMctKzeEengNPCERURytCCcXD0gHZfLiGC3gbNnSvWHNTGMeVrW/AE0c815eOm+HQq1onrlmgFSwp8MF7QM7w==","MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCFm7FIxnZaRAGQkvXzdLeVx59AEvViiv99LSRCqWVw5ZJRM8Zr4wB2dS0Dkr5CF5g1REwrEl9Gi63qpQeKNfJueHiDgsS0OE7KftimkR30S3N6ith1QKmVgWAQ34wQBX2Cof3GnR+Ed63vDxoB9W4FTZVUhnd0p5pJl6lJuZdpKUQxDeQTX6Z1W/rdJbU/IBLuby/Ssq0/vK73vbbtIr0zZ2OOq7wxg0Uwc2vG/ZVY6SWuyWVTNXj+NbFr1tCof3X/Cz2SY4ph0xIqnOEpycBrosmugcdltFc8g+f7Nw+6dHOg+DWwB2ZEn52BmIHp2BXB2AxkJiaxzf4twpgqPJc3AgMBAAECggEAEPnaSbvlt8xiQoNZusg+t0o44sRF53JvyfDdZZbua6zPrX+dm4GpQmPbB1Qy1mT3EvWNk/9umaEPxPuY/KekGQM3lMYdxiRNZo89adSQcMTRdGWF4UgJBBT/JsWwnyyDaQC6JO073vHx6KkLjeooQ4Y7DhVTwj+1a9pYSSTKpzLHC/SCEA2tfPZU5wcca0/y+686vtDucWhwAP+SLX7SeeddEM5PgNK2IcKucsD3muypg8TFqQctwmQw+zCQvp3w6L3As4FLKkI8YUTnxntkec1OQqgcgxpfcxaVGbt8TDaTg/lnch2dcFW1sIjLmcDgKX5XRkKH4kHWaQmSXSdMeQKBgQDBBImE3Hgz8elsljKm+LduKgjpBuP0O06afLc1oVUWkV3xhN1ho57HJpj4lrB+yf+mNSefsS+BUB3bVCW/NFVbaFSrCta4ylduNHz7+uGkswEV7HgOwU6vH39K9Fi3fZpotvBIIa2UFHyltAdiNVO0/x8EHn+80H+txSYfEqvhNQKBgQCxNHZp9Rcs1E7d7BJWuWCsCKyK7AlXpIOuD58EedtdAtJqcIIR51GCe1UzqNVFKyRECPLcHMlZHnYtHYQflxFeuc0/VHxJ9zx5VemCRRklLydvFBShRUfZcTkeBC0EGjU0fjdp3cJU2VARV93LLL1g9ujYaruHpW0o8CDnMUbwOwKBgA2jm3AXACtzgbIZnvSriJKxR7Xntb3xXumNvIh+oPuaRBAn+ljG7hZWhOK0Cz66WWVORkGDjL7PgXyZIp2zPgDai3kWp/ug2LLB5L8NiFpSB9abwhQQ1tWLHTyXrZkxt/KEUtBWCOT42aH/6bGn4QVeLbvlx9L4zLzjvIDfmeOtAoGAahDHy9YagAe4CRczRtuApJgwhpqPYPkkpDvPZ4N0rLByt6kOAZ9eZ2Zg8iHdPaB7/YkJrHxCfGhCPfDL04i9qeA2nPB50F/+v3WP5hxr15jo1pDDZGAuiFU/5dqEA0+YhwoBKwnENrs4NJlONT1bQT2o01jXVHLM6tMILrmNB18CgYApIBXPauzUbxAXpVHJqbBQl/EX4pSt5K5mzAwXnWxKir+GeBkqBzDx+8eZcaQLT9pgZGPSXeg+g12Ik2nIcPaCK09vypHsajwkV3g06ZQ5UaMeoh9F3Fg0LJRhZi/RMvpQlH3ZzaQWoN35saRd7y/z9L/tQImxBWVbhdtP5bsPtg==")
1ab姚鑫@#！
```

### JavaSignByha256WithRsa

- 描述：Java SHA256withRSA 签名。
- 参数：`content`,`privateKey`,`keyType`,`outputFormat`
- 返回值：签名

```objectscript
ClassMethod JavaSignBySha256WithRsa(content As %String, privateKey As %String, keyType As %String = "base64", outputFormat As %String = "base64") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSignBySha256WithRsa("appid=&nonce=fc8d5a2d-be00-4e9f-852d-1a5935246e0c&timestamp=1750660051161","MIIEvwIBADANBgkqhkiG9w0BAQEFAASCBKkwggSlAgEAAoIBAQCnG2oVd38TAzh1KD0kna4P3dJhYVLrf2HRbiZAhqp9RuZDn//O/x69FmclsJlxBzbHgiUfAlMi1UnkELNTOhq6eJgrx7C6vUBFIFgYmsx6NBSIiJdAlhNa9ASA1Fvn7HlIdRTau/oV/D9PYM5QV86J2tiywbFWTHVir7uymueHFpinygXN4HBYSxn+2m9rX8O2IWNcGv8aCEvSdbflEn0pusjsMRT3NvZmWo/T8kw6WCcbkEeGKowBsRiObcFilCmO+MLzYUMQb4wI55aj9Y4RTdifxzinmv8Bvi2IWGlxZOpT3mQCE8CK/dez3wMqgVFlDENxNsLDGUBbKG/EKTN7AgMBAAECggEAH4futmo70gyThJe5IcWW6GuEnNdOXB1HCcts8FP4q3bLUAtKq3Y8CJXHlLcD3O3tiiumcXlw0mvIa34zOAsIrBLBM9GUKUg4blKyDMJ4vr5A+Zo8X/VxZYIRr3ViehqGsANXkgZSI//aulGb3FEVKbHfnasqmQwIQjzCf+r2sOhl7Vay2iy/ii5URhXmuG2mUOtBAFlcqnlmRPNnw6AB7b/VZjjM1tLQZwxkPi9BwYEbSQgqNo2PNd/2bqzqC8VaN4Ptdvepfd5Sa8glYFD2gEhGEXmjSFHxi3wiwyB1YFmDuTueWZCjKhQHCUCURh5ZiAy6mOjZ8sUFoNw14sVTkQKBgQDqj3qBWV+vwOnpzch/B7jPnr5hCNKl0Q1LEatA4PDxm74x4IW7Azqw1YlyXDXjVg3cmVada3PHKmELLSKfq4QxKLyi+loUFvGXksvo5t19mqZGFu1F/kL/8vbJdNkdGMhdhQR2effSEkjZSJDq9ELcUS1eANfia55+qfaTbew2/wKBgQC2YZUdApv15na1dksvEWADZiVRqc8jgT84mYFfT9JufoGt+29V0gqdDgwMJiOsEw8Gmn/RduzOSviEpr3Dlwc7/m6zOkxxUDIOQPmpgYHAXNgMjpI4grrfGxUZ0Bug9XjmWVxbzplz8VVJVWkO7EimYZ8pjkaN9LYUgJcJR/hfhQKBgQCw6Lg32MWflDuYOLnQfW15QjxKiVH++DYzeUcVrtJrF9ESY0nZq+zXNKbu1vdZ2CyqRgiawFFZVPBOcqNblAwm25eywGmyHz/l1zTuGznQoxRnZqFcmhHEY2aYuQWLuYZdapbcGM+95EaHgwCyBLps2tkBvlcVEaA/3kb4GP8A1wKBgQCnu205BfpLl84bK5UPz6n+1kWCKmrvm2F6e2sJLk85Aa3gRbrqMcdDE/UugzERg2GxUAw3p2k4fKi8zuD9bfvgSCqlOPuuxvOSOl2icBHVyU2FluWRhWG56J1qZQPT745mQ072vDZS9GPckumRKOvT4TpRLKFk0udWScEebwtVRQKBgQCCS+gxsOLoJ64Lf1Tfa0GCJCzYJKol9BmoShw8Tvt8JMDm7vxt+o2E4ZRuzw0gdO4YsAMEe4jSXwK/+2BmsyjaTHqLeJL/jXTXNGEECPd/GYlaMOT9WuEb6oADS1DFPVCzzXj+lMe6kYyY4Uv87VejrOxBqsjzWdg9TZ61fvSjJg==")
K54e8FNcXKlJgOHwnOT5B5ee4p8R6R91gBLanJsh+REgiB3FaQzV9c8wd+P/4aowfQ/L6l+lXZnO6I7AEA51+JjpMQzSvV8Z+Q00Zn1iohx6v2+hLhe16CRyPg6pDmTwNduadskUubqgKBHqBuGcZ8+lAH+9LnSPH/+YAa6adSA5EFbfehp06Jc3/uIvNVXmwqH+3nvhaJuSeyW0Z4SvPXEtYkL+J3Q5/sB8WlUoD3N56Xwv/gsHLTNhL9SzMpFdHWnss38Qv2sajUuUojnkFOsdO9y1lVtsuRsKV7blRX8dhKWYOhMF/U9wXYPY7Yc0sR7L4iT4K62tbaggGhElmg==
```

### JavaSignBySha1WithRsa

- 描述：Java SHA1withRSA 签名。
- 参数：`content`,`privateKey`,`keyType`,`outputFormat`
- 返回值：签名

```objectscript
ClassMethod JavaSignBySha1WithRsa(content As %String, privateKey As %String, keyType As %String = "base64", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSignBySha1WithRsa("1ab姚鑫@#！","MIIEvg   IBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQCxoREOdwF3UC7jcaZlYt7GnwLyGI09xZsg1x6bCYR2fc0/NI6XA76dTYKCwcuROSROoHvJyZ1jeEpMZzq6h+GGO0gW4rlMnSjoymW2IXW7wwWLqHTLpjC5KqYiQwcvAU4eumCPVa3MApFdrm+hgvZfDrRlslldSAlZ0gYXJFJpaHg9TdPP+jD1mIyJ19/7YWycWp/cnPzt3tKQZLyihDVce/UeG84WV9OXrhz6R5SMfmCu2VgghKDj3dFpK8GzKFXKvBO9+gvfQOne6+kps3CfvB7ZTKRIpoqIA2VLhYOYBWXoIJoVtmzNHCGaiaGOB7FzJBsN1V5sViG3juReu2fBAgMBAAECggEBALFylBV9MUu+IHk6id3y3VZSd6DeggrZo1U1Ue/TnC67EhU4LdIS/ZMrYVu6ueAD049wpvk4njBGdQLKhVLed5+fDS8/o5kzzzBvMRi3aGQAOUQBL1xaU4ZHYtwLVdvRU/dCfT/zecG6Nvn9Tqtspy7jA7gsaTXUxvKh60+nC2UcOFa8NVAq30biByQ0ClXpG5+HAoJSyQuomWRW1Dm0uTfNzJOOzcp/TYgxzL9Pw9h589oIJiBf0o0EYVOKG4CznF4Gn7bjOPluDB3VGj3au1BYTGD71ksZsbmCf2lbbXVlAKPyZ6bfYtkV6wLXGLzNS0HkOkFR4PIlIspy2IoeZ/kCgYEA866HC3VyKQwowMeQsX1AHweMTxAEe4wsbWJiuha/Ii+qi/06NipjZUyYiquwTQJo/qkBqH7EIKEsWQvcoqkH1dlnzXM7RhYkscCe4DQv5MAr3C70vMhC+NVj6L9lz5GfNNXf6clT3zLvLFHDVKc5iX1fA6YeobndbVPQyna6t18CgYEAupvBlPboKUBG4S+SLUneYsuj0c6FtSqai/sSqEX2IOXUF3B2uwWz+aWDjbhMBPqunTMdW2+FmiaHRySzF8Vxrc2EDN5t6ROOZAG8c5wNgkYDD4nhUabRfTYjK5NkvQHkkZ4xuNdPVO0XkaxV3FXiwzacUr93Og7Eavk7AR6l1N8CgYBqpmjuZ/GV639umClItSO6MOiEteLwW7IaEaRaA5iVkr0W1baDfFvSOwrMLkZT/gkL49YY85pNGZ06P8nJ2ybVvngC4DsB+rEGpuIiCFUpzb2keVydvxwooeQ/On2Jshc23aBJRtcRac5p3EMcKrAw75EFHNBtQdaagcNwyTQBCwKBgQCxyv64mDqAOw6NNI7YeX3ZsV4m4tb/0lSnNBMFooqrs23M20k0TW25WJorp8E+KT2+5tl8qZeoVDclcHD2IBd8WcgLns0neYt7+y97Et7IFT6LSnoUGpWT78W4mdksP6Zvm0KScwnRx4diMskngejox5pPOL824KUBqu4t1e54DwKBgHyRXbFpDxo1ylqitmV7auGTDnoPprU8QFOeWLgCJvkDLLXjgXz21iQ2CmmWCvVgHrnPUaWT19Q9F1fnpslYaY4AIYaZgeejLo5QidmYga8FP3/npnx5WNS6cejx4qOebigWhDHZag34j9CWDrC8+l/WRlcBvFbONv2YKTxJof+5")
7f1fecca1b737f97db39166984b6f31fdf9d83cb29728c29adc7a56ef377a82e4e17c3d00c15affcbd4edd0d9f3935da26741b8fdc63792c013c7f8f293e690c16bf279600e35eb72004dc06950b550a786e5438d61011c6ccfecd076ac53205ff19fbdcf3bb773201263face5c01329c274f4274a222346a93479ccdedeca9fcb1827471f98c0b7ea111bcae907f45d08a0773a941ac14f86f85e41a3765045307c5291a93abb97b50780d26b33ff078698fe39b09805c5e561cafc2c952efed921f01814acde3fcac3e654a1ec35f7c42ac5320f47bc24e7847349373a7a90be03411617d756648ad6f2f265eeb51d34adb8e7a69b3395b1fba2ba2ad64ff5
```

### JavaVerifyBySha1WithRsa

- 描述：Java SHA1withRSA 验签。
- 参数：`content`,`sign`,`publicKey`,`keyType`,`outputFormat`
- 返回值：验证结果

```objectscript
ClassMethod JavaVerifyBySha1WithRsa(content As %String, sign As %String, publicKey As %String, keyType As %String = "base64", outputFormat As %String = "hex") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaVerifyBySha1WithRsa("1ab姚鑫@#！","7f1f   ecca1b737f97db39166984b6f31fdf9d83cb29728c29adc7a56ef377a82e4e17c3d00c15affcbd4edd0d9f3935da26741b8fdc63792c013c7f8f293e690c16bf279600e35eb72004dc06950b550a786e5438d61011c6ccfecd076ac53205ff19fbdcf3bb773201263face5c01329c274f4274a222346a93479ccdedeca9fcb1827471f98c0b7ea111bcae907f45d08a0773a941ac14f86f85e41a3765045307c5291a93abb97b50780d26b33ff078698fe39b09805c5e561cafc2c952efed921f01814acde3fcac3e654a1ec35f7c42ac5320f47bc24e7847349373a7a90be03411617d756648ad6f2f265eeb51d34adb8e7a69b3395b1fba2ba2ad64ff5","MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAsaERDncBd1Au43GmZWLexp8C8hiNPcWbINcemwmEdn3NPzSOlwO+nU2CgsHLkTkkTqB7ycmdY3hKTGc6uofhhjtIFuK5TJ0o6MpltiF1u8MFi6h0y6YwuSqmIkMHLwFOHrpgj1WtzAKRXa5voYL2Xw60ZbJZXUgJWdIGFyRSaWh4PU3Tz/ow9ZiMidff+2FsnFqf3Jz87d7SkGS8ooQ1XHv1HhvOFlfTl64c+keUjH5grtlYIISg493RaSvBsyhVyrwTvfoL30Dp3uvpKbNwn7we2UykSKaKiANlS4WDmAVl6CCaFbZszRwhmomhjgexcyQbDdVebFYht47kXrtnwQIDAQAB")
1
```

## 5. DES

### JavaDesEcbPkcs5Encrypt
- 描述：Java DES ECB PKCS5 加密。
- 参数：`plainText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：密文
```objectscript
ClassMethod JavaDesEcbPkcs5Encrypt(plainText As %String, keyStr As %String, keyType As %String = "base64", outputFormat As %String = "base64") As %String
```
- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaDesEcbPkcs5Encrypt("1ab姚鑫@#！","MTIzN   DU2Nzg=")
Kvo+vm9pScNRllrCV8LMkA==
```

### JavaDesEcbPkcs5Decrypt
- 描述：Java DES ECB PKCS5 解密。
- 参数：`cipherText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：明文
```objectscript
ClassMethod JavaDesEcbPkcs5Decrypt(cipherText As %String, keyStr As %String, keyType As %String = "base64", outputFormat As %String = "base64") As %String
```
- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaDesEcbPkcs5Decrypt("Kvo+vm9pScNRllrCV8LMkA==","MTIzNDU2Nzg=")
1ab姚鑫@#！
```

## 6. 3DES

### JavaDesedeEcbPkcs5Encrypt

- 描述：Java 3DES ECB PKCS5 加密。
- 参数：`plainText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：密文

```objectscript
ClassMethod JavaDesedeEcbPkcs5Encrypt(plainText As %String, keyStr As %String, keyType As %String = "base64", outputFormat As %String = "base64") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaDesedeEcbPkcs5Encrypt("1ab姚鑫@#！","5f   e300c90369642d3a15d234c0668f74")
ZMb9ru7du2h4LfcEC/i4qA==
```

### JavaDesedeEcbPkcs5Decrypt

- 描述：Java 3DES ECB PKCS5 解密。
- 参数：`cipherText`,`keyStr`,`keyType`,`outputFormat`
- 返回值：明文

```objectscript
ClassMethod JavaDesedeEcbPkcs5Decrypt(cipherText As %String, keyStr As %String, keyType As %String = "base64", outputFormat As %String = "base64") As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaSm4EcbPkcs7Decrypt("7ad4159325eb143c2e7338cd42514ea3","975C7238C3824FB0")
1ab姚鑫@#！
```

### JavaDesedeCbcPkcs7Encrypt

- 描述：Java 3DES CBC PKCS7 加密。
- 参数：`plainText`,`keyStr`,`ivStr`,`keyType`,`outputFormat`,`url`
- 返回值：密文

```objectscript
ClassMethod JavaDesedeCbcPkcs7Encrypt(plainText As %String, keyStr As %String, ivStr As %String, keyType As %String = "base64", outputFormat As %String = "base64", url As %String = {..#StandardAlgUrl}) As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaDesedeCbcPkcs7Encrypt({"ScheduleOperatorID":"10999","ScheduleOperatorName":"陈医生","MedRecNO":"123","Timestamp":"14904   15566"}.%ToJSON(),"eWorldTomTaw#7=*","TomTaw#7")
VcyCfRqZ2TOpXoC+qOzAjQJ1195tlaleBEjCie4u9RPvfhua37PGs2r8+1pJUCOmv/thVnUF2PP3K5z5fypx12lKnHHulxNlYi5rN194RDntjv9yEgh01UhkwZxvku4wosD1ECxMIXTMNt88EhipHA==
```

### JavaDesedeCbcPkcs7Decrypt

- 描述：Java 3DES CBC PKCS7 解密。
- 参数：`cipherText`,`keyStr`,`ivStr`,`keyType`,`outputFormat`,`url`
- 返回值：明文

```objectscript
ClassMethod JavaDesedeCbcPkcs7Decrypt(cipherText As %String, keyStr As %String, ivStr As %String, keyType As %String = "base64", outputFormat As %String = "base64", url As %String = {..#StandardAlgUrl}) As %String
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).JavaDesedeCbcPkcs7Decrypt("VcyCfRqZ2TOpXoC+qOzAjQJ1195tlaleBEjCie4u9RPvfhua37PGs2r8+1pJUCOmv/thVnUF2PP3K5z5fypx12lKnHHulxNlYi5rN194RDntjv9yEgh01UhkwZxvku4wosD1ECxMIXTMNt88EhipHA==","eWorldTomTaw#7=*","TomTaw#7")
{"ScheduleOperatorID":"10999","ScheduleOperatorName":"陈医生","MedRecNO":"123","   Timestamp":"1490415566"}
```

# JAVA 加密服务 - 非标准加密

### SignSm3WithSm2

- 描述：SM2+SM3 签名（对象返回）。
- 参数：`plainText`,`key`,`privateKey`
- 返回值：`%DynamicObject`

```objectscript
ClassMethod SignSm3WithSm2(plainText As %String, key As %String, privateKey As %String) As %DynamicObject
```

- 调试示例：

```
USER>w ##class(Util.EncryptionUtils).SignSm3WithSm2("1ab姚鑫","1HDFSDPCO01P3F60C  80A00005238496E","I86Px211CbkGhovKjwAZ5zTjWRPAPkeIQKgXixxYSa4").%ToJSON()
{"msg":"","code":0,"data":{"cipherText":"hbv9F7i/QGkTSfjR6Lo+vHhQEKH8/gb7oHyQm7XUmGdxX4IQOrkCuDKiPxIo0C7ejARMPsS21G3ZpoTxT3Vl0g=="}}
```

### SignSm3WithSm2Stream

- 描述：SM2+SM3 签名（流版本）。
- 参数：`plainText`,`key`,`privateKey`
- 返回值：`%Stream.GlobalCharacter`

```objectscript
ClassMethod SignSm3WithSm2Stream(plainText As %Stream.GlobalCharacter, key As %String, privateKey As %String) As %Stream.GlobalCharacter
```

### SignSm3WithSm2StreamV2

- 描述：SM2+SM3 签名 V2。
- 参数：`data`,`appId`,`appSecret`,`publicKey`,`privateKey`
- 返回值：`%Stream.GlobalCharacter`

```objectscript
ClassMethod SignSm3WithSm2StreamV2(data As %Stream.GlobalCharacter, appId As %String, appSecret As %String, publicKey As %String, privateKey As %String) As %Stream.GlobalCharacter
```

### JavaNonstandardYlswgdEncrypt
- 描述：YLSWGD 非标准加密。
- 参数：`plainText`,`publicKeyPath`
- 返回值：密文
```objectscript
ClassMethod JavaNonstandardYlswgdEncrypt(plainText As %String, publicKeyPath As %String) As %String
```
- 调试示例：`w ##class(Util.EncryptionUtils).JavaNonstandardYlswgdEncrypt("abc","E:\Desktop\key\gnete_2006.cer")`

### JavaNonstandardYlswgdDecrypt
- 描述：YLSWGD 非标准解密。
- 参数：`cipherText`,`privateKeyPath`
- 返回值：明文
```objectscript
ClassMethod JavaNonstandardYlswgdDecrypt(cipherText As %String, privateKeyPath As %String) As %String
```
- 调试示例：`w ##class(Util.EncryptionUtils).JavaNonstandardYlswgdDecrypt("<cipher>","E:\Desktop\key\gnete_2006.pfx")`

---

# 二、编码方法

各方法单参数含义（`str`、`hex`、`content` 等）见文档开头 **「参数约定与填写说明」第 9 节**。

## 1. Base64

### Base64Encode

- 描述：Base64 编码。
- 参数：`str`
- 返回值：Base64 字符串
```objectscript
ClassMethod Base64Encode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).Base64Encode("abc")`
- 调试结果：`YWJj`

### Base64URLEncode
- 描述：URL 安全 Base64 编码。
- 参数：`str`
- 返回值：编码字符串
```objectscript
ClassMethod Base64URLEncode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).Base64URLEncode("abc")`
- 调试结果：`YWJj`

### Base64Decode
- 描述：Base64 解码。
- 参数：`str`
- 返回值：原文
```objectscript
ClassMethod Base64Decode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).Base64Decode("YWJj")`
- 调试结果：`abc`

### Base32Encode
- 描述：Base32 编码。
- 参数：`str`
- 返回值：编码字符串
```objectscript
ClassMethod Base32Encode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).Base32Encode("abc")`
- 调试结果：`MFRGG===`

### Base32Decode
- 描述：Base32 解码。
- 参数：`str`
- 返回值：原文
```objectscript
ClassMethod Base32Decode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).Base32Decode("MFRGG===")`
- 调试结果：`abc`

## 2. HEX

### Hex2ByteEncode

- 描述：Hex 文本转字节串。
- 参数：`content`
- 返回值：字节串

```objectscript
ClassMethod Hex2ByteEncode(content As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).Hex2ByteEncode("abc")`
- 调试结果：`006100620063`

### Hex2ByteDecode

- 描述：字节串转 Hex 文本。
- 参数：`str`
- 返回值：Hex

```objectscript
ClassMethod Hex2ByteDecode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).Hex2ByteDecode("006100620063")`
- 调试结果：`abc`

### HexEncode

- 描述：Hex 编码。
- 参数：`str`
- 返回值：Hex

```objectscript
ClassMethod HexEncode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).HexEncode("abc")`
- 调试结果：`616263`

### HexDecode

- 描述：Hex 解码。
- 参数：`hex`
- 返回值：原文

```objectscript
ClassMethod HexDecode(hex As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).HexDecode("616263")`
- 调试结果：`abc`

## 3. UTF8

### UTF8Encode

- 描述：UTF8 编码。
- 参数：`str`
- 返回值：编码结果

```objectscript
ClassMethod UTF8Encode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).UTF8Encode("姚鑫")`
- 调试结果：`"??§"_$c(154)_"é"_$c(145)_"??"`

### UTF8Decode

- 描述：UTF8 解码。
- 参数：`str`
- 返回值：原文

```objectscript
ClassMethod UTF8Decode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).UTF8Decode(##class(Util.EncodingUtils).UTF8Encode("姚鑫"))`
- 调试结果：`姚鑫`

## 4. Unicode

### UnicodeEncode

- 描述：Unicode 转义编码。
- 参数：`str`
- 返回值：编码字符串

```objectscript
ClassMethod UnicodeEncode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).UnicodeEncode("姚鑫")`
- 调试结果：`\u59DA\u946B`

### UnicodeDecode

- 描述：Unicode 转义解码。
- 参数：`str`
- 返回值：原文

```objectscript
ClassMethod UnicodeDecode(str As %String) As %String
```

- 调试示例：`w ##class(Util.EncodingUtils).UnicodeDecode("\u59DA\u946B")`
- 调试结果：`姚鑫`

## 5. URL

### URLEncode

- 描述：URL 编码。
- 参数：`str`
- 返回值：编码字符串
```objectscript
ClassMethod URLEncode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).URLEncode("a b")`
- 调试结果：`a%20b`

### URLDecode
- 描述：URL 解码。
- 参数：`str`
- 返回值：原文
```objectscript
ClassMethod URLDecode(str As %String) As %String
```
- 调试示例：`w ##class(Util.EncodingUtils).URLDecode("a%20b")`
- 调试结果：`a b`

