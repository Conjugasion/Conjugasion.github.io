---
title: Java开发常用的加密算法
tag: [加密算法]
category: 加密算法
excerpt: 常用的信息加密算法
cover: /image21.jpg
---
### **加密算法分类**
加密算法可以分为`单向加密`和`双向加密`，双向加密又分为`对称加密`和`非对称加密`。
双向加密是将明文加密后形成密文，可以通过算法还原成明文。
单向加密只是对信息进行了摘要计算，不能通过算法生成明文。

### **双向加密之对称加密**
`对称加密`：采用单钥密码系统的加密方法，同一个密钥可以同时用作信息的加密和解密。需要对加密和解密使用相同密钥的加密算法。由于其速度，对称性加密通常在消息发送方需要加密大量数据时使用。对称性加密也称为密钥加密。加密算法是一组规则，规定如何进行加密和解密。因此对称式加密本身不是安全的。常用的对称加密有：`DES`、`IDEA`、`RC2`、`RC4`、`SKIPJACK`、`RC5`、`AES`算法等。
```
//KeyGenerator 提供对称密钥生成器的功能，支持各种算法
private KeyGenerator keygen;
 
//SecretKey 负责保存对称密钥
private SecretKey deskey;
 
//Cipher负责完成加密或解密工作
private Cipher c;
 
//该字节数组负责保存加密的结果
private byte[] cipherByte;

```

### **双向加密之非对称加密**
`非对称加密`：非对称加密算法需要两个密钥：公开密钥（publickey）和私有密钥 （privatekey）。公开密钥与私有密钥是一对，如果用公开密钥对数据进行加密，只有用对应的私有密钥才能解密；如果用私有密钥对数据进行加密，那么只有用对应的公开密钥才能解密。最常用的非对称加密算法是`RSA`(安全性很好)。
```
/**
 * @author tangdongfan
 * @date 2020/4/23 18:45
 * 基于非对称算法RSA加密/解密用户Pin
 * 公钥加密，私钥解密
 */
public class EncryptUtil {

    public static final String PRIVATE_KEY = "privateKey";
    public static final String PUBLIC_KEY = "publicKey";

    // 生成公钥和私钥
    public static Map<String, Key> generatorKeys() {
        try {
            Map<String,Key> keys = new HashMap<>();
            //KeyPairGenerator类用于生成公钥和私钥对，基于RSA算法生成对象
            KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance("RSA");
            //初始化密钥对生成器，密钥大小为1024位
            keyPairGen.initialize(1024);
            //生成一个密钥对，保存在keyPair中
            KeyPair keyPair = keyPairGen.generateKeyPair();
            //得到私钥
            Key privateKey = keyPair.getPrivate();
            //得到公钥
            Key publicKey = keyPair.getPublic();
            keys.put(PRIVATE_KEY, privateKey);
            keys.put(PUBLIC_KEY, publicKey);
            return keys;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    // 加密算法
    public static byte[] encrypt(Key publicKey, String srcMsg) {
        try {
            byte[] srcMsgByte = srcMsg.getBytes();
            //Cipher基于RSA进行加密
            Cipher cipher = Cipher.getInstance("RSA");
            //根据公钥，对Cipher对象进行初始化
            cipher.init(Cipher.ENCRYPT_MODE, publicKey);
            //加密
            return cipher.doFinal(srcMsgByte);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    // 解密算法
    public static String decrypt(Key privateKey, byte[] encryptMsgByte) {
        try {
            //Cipher基于RSA进行解密
            Cipher cipher = Cipher.getInstance("RSA");
            //根据公钥，对Cipher对象进行初始化
            cipher.init(Cipher.DECRYPT_MODE, privateKey);
            //解密
            byte[] srcMsgByte = cipher.doFinal(encryptMsgByte);
            return new String(srcMsgByte);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```


### **单向加密**
一旦数据被加密就没有办法颠倒这一过程。因此密码的验证包括对用户输入内容的重新加密，并将它与保存的密文进行比对，看是否匹配。一种简单的单向加密方式是`MD5`校验码。
`MD5`：Message-Digest Algorithm 5（信息-摘要算法 5），用于确保信息传输完整一致。是计算机广泛使用的杂凑算法之一（又译摘要算法、哈希算法），主流编程语言普遍已有MD5实现。将数据（如汉字）运算为另一固定长度值，是杂凑算法的基础原理，MD5的前身有MD2、MD3和MD4。MD5的作用是让大容量信息在用数字签名软件签署私人密钥前被"压缩"成一种保密的格式(就是把一个任意长度的字节串变换成一定长的十六进制数字串)。 
除了MD5以外，其中比较有名的还有`sha-1`、`RIPEMD`以及`Haval`等 