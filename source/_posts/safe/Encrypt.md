# 加密和解密

## 哈希算法

> [散列函数](https://zh.wikipedia.org/wiki/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B8)

哈希算法又称摘要算法、散列算法，是把任意长度的输入通过哈希算法变换成固定长度的输出（散列值）。

> value = hash(key)

哈希算法的特点：

1. 相同的输入一定得到相同的输出
2. 不可逆，无法直接通过输出推导出输出值

一般情况下，不同的输入得到的散列值是不一样的，但这是不一定的。

> 如果两个不同的输入得到相同的输出，那这种情况称为“哈希碰撞”，好的哈希算法碰撞的概率非常低

### 常见哈希算法

| 算法名称 | 输出大小（bit） | 是否会发生碰撞 |
| :------: | :-------------: | :------------: |
|   MD5    |       128       |       是       |
|  SHA-1   |       160       |       是       |
| SHA-256  |       256       |       是       |
| SHA-512  |       512       |       否       |

#### 如何使用

- **Java**

```java
// md5
public static String MD5Demo(String message) throws NoSuchAlgorithmException, IOException{
  // 获取相应的加密实例
  // 支持的算法名称包括：MD2、MD5、SHA-1、SHA-256、SHA-384、SHA-512
  MessageDigest md5 = MessageDigest.getInstance("MD5");
  md5.update(message.getBytes());
  StringBuilder hCode = new StringBuilder();
  // 得到哈希值二进制形式
  byte[] result = md5.digest();
  for(byte item:result){
	int value = item & 0xff;
	if(value < 16) hCode.append(0);
	hCode.append(Integer.toHexString(value));
  }
  return hCode.toString();
}
```

### 应用场景

- 验证信息是否被篡改： 比如验证下载的文件是否被修改，只需要对比文件内容的MD5值就可以了
- 存储用户口令

## 对称加密算法

**对称加密算法**又称为 **共享密钥加密算法**，在 **对称加密算法** 中，使用的密钥只有一个，发送方和接收方都使用同一个密钥进行加密和解密。

![encrypt-decrypt-01](https://user-images.githubusercontent.com/7795335/106727973-fb9c4b00-6646-11eb-88d6-2fa10e9bfbab.jpg)

### 常见对称加密算法

|                           算法名称                           |  密钥长度   |       分组模式       |                填充模式                 | 安全性 |
| :----------------------------------------------------------: | :---------: | :------------------: | :-------------------------------------: | ------ |
| [DES](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E5%8A%A0%E5%AF%86%E6%A8%99%E6%BA%96) |    56/64    | ECB/CBC/PCBC/CTR/... |       NoPadding/PKCS5Padding/...        | 不安全 |
| [AES](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86) | 128/192/256 | ECB/CBC/PCBC/CTR/... | NoPadding/PKCS5Padding/PKCS7Padding/... | 安全   |
| [IDEA](https://zh.wikipedia.org/wiki/%E5%9C%8B%E9%9A%9B%E8%B3%87%E6%96%99%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95) |     128     |         ECB          |      PKCS5Padding/PKCS7Padding/...      | 不安全 |

### 如何使用

- Java

```java
// AES 算法的使用
public class AesImp{
    /**
     * 加密
     * @param key 密钥
     * @param input 要加密的内容
     */
    public static byte[] encrypt(byte[] key,byte[] input) throws Exception{
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        SecretKey secretKey = new SecretKeySpec(key,"AES");
        // 加密模式
        cipher.init(Cipher.ENCRYPT_MODE,secretKey);
        return cipher.doFinal(input);
    }

    /**
     * 解密
     * @param key 密钥
     * @param input 要解密的内容
     */
    public static byte[] dencrypt(byte[] key,byte[] input) throws Exception{
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        SecretKey secretKey = new SecretKeySpec(key,"AES");
        // 解密模式
        cipher.init(Cipher.DECRYPT_MODE,secretKey);
        return cipher.doFinal(input);
    }
}
```

## 非对称加密算法

**对称加密算法**虽然解决了内容的加解密问题，但是它要求发送方和接收方都必须拥有同一个密钥，这就产生了一个如何将密钥安全的传输到对方的问题。

**非对称加密算法****（又称**公开密钥加密算法**）解决了以上问题，它需要两个密钥，一个称为**公开密钥**，即**公钥**，

另一个称为**私有密钥**，即**私钥**。

![encrypt-decrypt-02](https://user-images.githubusercontent.com/7795335/106728076-140c6580-6647-11eb-8a20-89457300deb7.jpg)

发送方使用公钥加密文件，接收发使用私钥进行解密。公钥是公开发布的，这就不涉及到密钥如何安全传输的问题了，因为没有私钥内容是无法解密的。

### 常见非对称加密算法

| 算法名称                                                     | 优点                     | 缺点         |
| ------------------------------------------------------------ | ------------------------ | ------------ |
| [RSA](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95) | 安全性高，几乎不可能破解 | 加密速度较慢 |

## 数字签名算法

> [数字签名是什么？](https://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)

数字签名算法能够验证数据完整性、认证数据来源可信度以及起到抗否认。

![encrypt-decrypt-03](https://user-images.githubusercontent.com/7795335/106728184-2e464380-6647-11eb-8ae2-30f43ae56507.jpg)

数字签名的流程：

1. **发送方**先将要发送的内容用**私钥**加密；
2. 再将加密内容使用哈希算法生成**摘要**，而后将加密内容和摘要一同发送给接收方；
3. **接受方**收到信息后，使用同一哈希算法生成密文摘要与发送方的摘要对比，以确保内容不被篡改
4. 确认内容没有被篡改后，**接受方**再使用**公钥**对密文进行解密

## 相关文章

- [一文搞懂对称加密：加密算法、工作模式、填充方式、代码实现](https://segmentfault.com/a/1190000040964999)