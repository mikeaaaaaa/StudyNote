# crypto学习



![1666848504325](C:\Users\豪哥\Desktop\assets\1666848504325.png)



## 一：pycryptodome库

安装：pip install pycryptodome

![1666837575911](C:\Users\豪哥\Desktop\assets\1666837575911.png)

| 包名             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| Crypto.Hash      | 该包中主要存放的是单向加密对应的各种哈希算法/散列算法的实现模块，如MD5.py, SHA.py，SHA256.py等。 |
| Crypto.Cipher    | 该包中主要存放的是对称加密对应的各种加密算法的实现模块，如DES.py, AES.py, ARC4.py等；以及公钥加密对应的各种加密算法的实现模块，如PKCS1_v1_5.py等。 |
| Crypto.PublicKey | 该包中主要存放的是公钥加密与签名算法的实现模块，如RSA.py, DSA.py等。 |
| Crypto.Signatue  | 该包中主要存放的是公钥签名相关算法的实现模块，如PKCS1_PSS.py, PKCS1_v1_5.py。 |
| Crypto.Random    | 该包中只有一个随机数操作的实现模块 random.py                 |
| Crypto.Protocol  | 该包中存放的是一些加密协议的实现模块，如Chaffing.py, KDF.py等。 |
| Crypto.Util      | 该包存放的是一些有用的模块和函数                             |

>这里需要说明的是，Crypto.PublicKey子包下的RSA.py和DSA.py模块只是用来生成秘钥对的，而基于公钥的加密与解密功能是由Crypto.Cipher子包下的PKCS1_v1_5.py或PKCS1_OAEP.py以这个密钥对儿为密钥来实现的；同样，签名与验证相关算法的功能是由Crypto.Signature子包下的PKCS1_v1_5.py和PKCS1_PASS.py以这个密钥对而为密钥来实现的。 



有什么不会的可以去看官方文档！！！

![1666848244752](C:\Users\豪哥\Desktop\assets\1666848244752.png)

## 二：对称加密（以AES为例）

### 0.原理

![1666850630483](C:\Users\豪哥\Desktop\assets\1666850630483.png)

常常配合：CBC模式、ECB模式

我们只需要提前把 data 补充到相应的字节数，之后通过 设置AES.new(key,模式,iv)具体的参数我们可以看一下AES类我们就知道了

### 1.Crypto.Random

一般用于产生随机密钥

```python
from Crypto.Random import get_random_bytes

key = get_random_bytes(16)

print(key)
```



2.Crypto.Cipher

默认为ECB模式；在new的时候可以指明要使用的模式	

![1666841236942](C:\Users\豪哥\Desktop\assets\1666841236942.png)

![1666841205265](C:\Users\豪哥\Desktop\assets\1666841205265.png)

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

key = get_random_bytes(16)

cipher = AES.new(key) #通过AES处理初始化密码字符串，并返回cipher对象

#要加密的bytes（字节数必须为16的倍数）！！！
#输出为 bytes
s = cipher.encrypt(b'1234567891234567')

#注意返回的s为bytes！！！
print(s)

```



### 3.crypto.Util

该模块可以对不符合长度规范的明文进行填充！！！

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad

data = b'123456'
key = get_random_bytes(16)
#设置为CBC模式的加密方式！！！
cipher = AES.new(key,AES.MODE_CBC)

encrypted_data = cipther.encrypt(pad(data,AES.block_size))

print(encrypted_data)
```



## 二：公钥加密（以RSA为例）



### 1.Cyrpto.PublicKey.RSA生成公私密钥对

该模块中有直接生成密钥的函数，而对称加密AES则没有，需要利用 Crypto.Random模块生成！！！

```python
from Crypto.PublicKey import RSA

#利用generate返回的是一个私钥对象，通过export_key()这个方法才能返回这个密钥
key = RSA.generate(2048) #参数为指定密钥的二进制位数

private_key = key.export_key()

public_key = key.publickey().export_key()

print(key)
print(pubic_key)
print(private_key)
```



### 2 RSA.import_key 获得公私密钥

![1666840342598](C:\Users\豪哥\Desktop\assets\1666840342598.png)

==使用PKCS1_OAEP加密时必须传入这个对象！！！==,所以要使用import_key这个方法来生成公钥实例化对象

### 3.RSA加密

==虽然我们获得了公钥与私钥，但是我们并不是直接利用公私钥来加密！！！==



```python
from Crypto.PublicKey import RSA

key = RSA.generate(2048)

public_key = key.publickey().export_key()

private_key = key.export_key()

#注意还是要采用对称加密来对密文加密！！！
from Crypto.Cipher import PKCS1_OAEP
data = b'123456'
#创建公钥实例化对象
publicKey = RSA.import_key(public_key)

cipher = PKCS1_OAEP.new(publiccKey)

encrypted_data = cipher.encrypt(data)
print(data)
```

### 4.RSA解密

```python
#解密

privateKey = RSA.import_key(private_key)
cipther = PKCS1_OPEA.new(privateKey)
data = cipther(encripted_data)
print(data)
```



## 三：数字签名 与 验签

### 原理：

简单来说，A使用自己的私钥对消息进行签名，将签名后的额消息以及公钥发给B，B使用公钥验证签名是否属于A

![1666841603561](C:\Users\豪哥\Desktop\assets\1666841603561.png)

这份摘要相当于一份指纹，能够唯一的识别文件！！！（其实是取决于hash函数的好坏），用户B 利用两次的摘要是否相等来确定==文件有没有被修改过== 。

![1666841775040](C:\Users\豪哥\Desktop\assets\1666841775040.png)

==使用场合==：核心问题时 确保收到的文件没有被修改过！！！

![1666842130578](C:\Users\豪哥\Desktop\assets\1666842130578.png)

这里我们基本要使用==Crypto.PublicKey、Crypto.Hash、Crypto.Signature三个库== 

### 1.Crypto.Hash获取消息的Hash值

常用的hash算法有很多，使用那种不重要，重要的是在验签是使用同一种hash函数就行

```python
data = b'123456'

#digest 翻译过来为摘要的意思
digest = MD5.new(data)
```

### 2.Crypto.Signature 对Hash值使用私钥进行签名

本质上是使用私钥对HASH值进行加密，为了方便我把公钥私钥以及消息分别保存在public_key.pem、private_key.pem、data.txt三个文件中，这里有坑了哦！

==坑一== ：别忘了获取公私钥要用import_key函数；
==坑二== ：从data.txt中读取消息后要使用相应的编码格式，于是上步就变成了：digest = MD5.new(data.encode('utf-8'))
  好了，在Crypto.Signature中随便选择一个模式，这里我选择pkcs1_15模式对消息就行签名，示例：

` signature = pkcs_15.new(private_key).sign(digest)`

### 3.签名验证

` pkcs1_15.new(public_key).verify(digest,signature)`

### 4.实验

![1666846639065](C:\Users\豪哥\Desktop\assets\1666846639065.png)

任务一的完成

```python
from Crypto.Publickey import RSA


key = RSA.generate(2048)

private_key = key.export_key()
public_key = key.publickey().export_key()

data = 'I love you'

with open('private_key.pem','wb') as f1,open('public_key.pem','wb') as f2,open('data.txt','w') as f3:
    f1.write(private_key)
    f2.write(public_key)
    f3.write(data)

```

任务二（签名产生）

```python
def signature(private_key,data):
    digest = MD5.new(data.encode())
    
    signature = pkcs_15.new(private_key).sign(digest)
    
    #将签名结果写入文件
    f = open('sig_result.txt','wb')
    f.write(signature)
    f.close()
    return signature
```

任务三 (签名验证)

```python
def verfier(public_key,data,signature):
    digest = MD5.new(data.encode())
    try:
        pkcs1_15.new(public_key).verify(digest,signature)
        print("yes")
    except:
        print('NO')
```



任务四（测试）

```python
with open('private_key.pem','rb') as f1,open('data.txt','r') as f2:
    private_key = RSA.import_key(f1.read()）
    data = f2.read().decode()
                                 
  	signaturer(private_key,data)
                                 
                                 
with open('public_key.pem','rb') as f1,open('data.txt') as f2, open('sig-results.txt','rb') as f3:
     public_key = RSA.import_key(f1.read())
     data = f2.read().encode()
     signature = f3.read()
                                 
   verifier(public_key,data,signature)
```

## base64模块（python自带）

![1666848111506](C:\Users\豪哥\Desktop\assets\1666848111506.png)





![1666848142227](C:\Users\豪哥\Desktop\assets\1666848142227.png)

Python中有一个内置的base64模块可直接用来进行base64的编码和解码工作-- ==即提供 “二进制数据” 与 “可打印（可见的ASCII字符”== 之间的转换功能。常用的函数有以下几个： 

![1666849862372](C:\Users\豪哥\Desktop\assets\1666849862372.png)

## 关于hash的一些名词解释

#### ***		HASH：***



-  一般翻译为“散列”（也有直接音译为“哈希”），就是把任意长度的输入（又叫做预映射，pre-image），通过散列算法，变成固定长度的输出，该输出值就是散列值。这种转换是一种压缩映射，也就是散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，而不可能从散列值来唯一确认输入值。**\*简单来说，hash算法就是一种将任意长度的消息压缩为某一固定长度的消息摘要的函数***。

  ### **\*MD5：***

  

-  全称为 Message Digest algorithm 5，即信息摘要算法。该算法可以生成定长的数据指纹，被广泛应用于加密和解密技术，常用于文件和数据的完整性校验。

  #### **\*SHA：***

-  全称为 Secure Hash Algorithm，即安全散列算法/安全哈希算法。该算法是数字签名等密码学应用中的重要工具，被广泛应用于电子商务等信息安全领域。根据生成的密文的长度而命名的各种具体的算法有：SHA1（160bits）、SHA224（224bits）、SHA256（256bits）、SHA384（384bits）等。

  #### **\*HMAC：***

-  全称为 Hash Message Authentication Code，即散列消息鉴别码。HMAC是基于密钥的哈希算法认证协议，主要是利用哈希算法（如MD5, SHA1），以一个密钥和一个消息作为输入，生成一个消息摘要作为输出，因此其具体的算法名称为HMAC-MD5、HMAC-SHA1等。可见HMAC算法是基于各种哈希算法的，只是它在运算过程中还可以使用一个密钥来增强安全性。

## secrets模块的学习

### 1.先了解一下random模块

![1666848769927](C:\Users\豪哥\Desktop\assets\1666848769927.png)

choice（）函数也可以指定选择的个数

==完全不能用于加密==

### 2.secret模块

![1666848972715](C:\Users\豪哥\Desktop\assets\1666848972715.png)

**\*实例2：*** 生成一个由10位数字和字母组成的随机密码，要求至少有一个小写字符，至少一个大写字符 和 至少3个数字

```python
import secrets
import string

alphanum = string.ascii_letters + string.digits
while True:
    password = ''.join(secrets.choice(alphanum) for i in range(10))
    if (any(c.islower() for c in password)
            and any(c.isupper() for c in password)
            and len(c.isdigit() for c in password) >= 3):
        break
```

**\*实例3：*** 生成一个用于找回密码应用场景的、包含一个安全令牌的、很难猜到的临时URL

```
import secrets
url = 'https://mydomain.com/reset=' + secrets.token_urlsafe()
```

### 字节串操作

![1666849447280](C:\Users\豪哥\Desktop\assets\1666849447280.png)

所有操作和字符串一摸一样，只是操作的单位从单个字符变成了 字节，照样能使用索引，使用字符串的以下方法：

![1666849638887](C:\Users\豪哥\Desktop\assets\1666849638887.png)

由于一个中文使用utf8是占3个字节，所以len的结果为15，貌似字符串的功能他都能使用！！！！

### crypto库的几个使用案例：



### **\*实例1：*** 使用SHA256算法获取一段数据的摘要信息

```
from Crypto.Hash import SHA256

hash = SHA256.new()
hash.update('Hello, World!')
digest = hash.hexdigest()
print(digest)
```

输出结果：

```
dffd6021bb2bd5b0af676290809ec3a53191dd81c7f70a4b28688a362182986f
```

### **\*实例2：*** 使用AES算法加密，解密一段数据

```
from Crypto.Cipher import AES

# 加密与解密所使用的密钥，长度必须是16的倍数
secret_key = "ThisIs SecretKey" 
# 要加密的明文数据，长度必须是16的倍数
plain_data = "Hello, World123!"
# IV参数，长度必须是16的倍数
iv_param = 'This is an IV456'

# 数据加密
aes1 = AES.new(secret_key, AES.MODE_CBC, iv_param)
cipher_data = aes1.encrypt(plain_data)
print('cipher data：', cipher_data)

# 数据解密
aes2 = AES.new(secret_key, AES.MODE_CBC, 'This is an IV456')
plain_data2 = aes2.decrypt(cipher_data)  # 解密后的明文数据
print('plain text：', plain_data2)
```

输出结果：

```
('cipher data\xef\xbc\x9a', '\xcb\x7fd\x03\x12T,\xbe\x91\xac\x1a\xd5\xaa\xe6P\x9a')
('plain text\xef\xbc\x9a', 'Hello, World123!')
```

### **\*实例3：*** 随机数操作

```
from Crypto.Random import random

print('random.randint: ', random.randint(10, 20))
print('random.randrange: ', random.randrange(10, 20, 2))
print('random.randint: ', random.getrandbits(3))
print('random.choice: ', random.choice([1, 2, 3, 4, 5]))
print('random.sample: ', random.sample([1, 2, 3, 4, 5], 3))
list = [1, 2, 3, 4, 5]
random.shuffle(list)
print('random.shuffle: ', list)
```

输出结果：

```
('random.randint: ', 10L)
('random.randrange: ', 10L)
('random.randint: ', 5L)
('random.choice: ', 5)
('random.sample: ', [5, 4, 2])
('random.shuffle: ', [5, 2, 1, 3, 4])
```

### **\*实例4：*** 使用RSA算法生成密钥对儿

生成秘钥对：

```
from Crypto import Random
from Crypto.PublicKey import RSA

# 获取一个伪随机数生成器
random_generator = Random.new().read
# 获取一个rsa算法对应的密钥对生成器实例
rsa = RSA.generate(1024, random_generator)

# 生成私钥并保存
private_pem = rsa.exportKey()
with open('rsa.key', 'w') as f:
    f.write(private_pem)

# 生成公钥并保存
public_pem = rsa.publickey().exportKey()
with open('rsa.pub', 'w') as f:
    f.write(public_pem)
```

私钥文件rsa.key的内容为：

```
-----BEGIN RSA PRIVATE KEY-----
MIICXAIBAAKBgQCo7vV5xSzEdQeFq9n5MIWgIuLTBHuutZlFv+Ed8fIk3yC4So/d
y1f64iuYFcDeNU7eVGqTSkHmAl4AihDXoaH6hxohrcX0bCg0j+VoQMe2zID7MzcE
d50FhJbuG6JsWtYzLUYs7/cQ3urZYwB4PEVa0WxQj2aXUMsxp6vl1CgB4QIDAQAB
AoGAS/I5y4e4S43tVsvej6efu1FTtdhDHlUn1fKgawz1dlwVYqSqruSW5gQ94v6M
mZlPnqZGz3bHz3bq+cUYM0jH/5Tygz4a+dosziRCUbjMsFePbJ4nvGC/1hwQweCm
+7sxog4sw91FrOfAg/iCcoeho0DghDolH9+zzwRYPIWUyUECQQDFGe+qccGwL9cU
v+GmZxtF8GkRL7YrXI7cvnZhnZZ7TANjxlYukLGEpiFGIDd0Aky1QhkK18L8DTO4
+iGXTpgJAkEA22o03/1IqeRBofbkkDmndArHNUnmv5pyVFaLKPoVgA4A1YsvqxUL
DK6RwFGONUMknBWY59EDKCUdIf3CsVIhGQJAJKDMRB19xBMv4iBCe9z/WYDy1YnL
TcWWmvkeIMfbVjBrFNif3WlwQ9lnp5OHGpzuymRtKPGtv49ohECfi3HEmQJAPI+n
AoAdk07+Up8b3TccoinrbCj2uMH/dongpTHJx2uWDVr6kEUhpKF2d1fLYaYjr7VC
XBHTxjvgO6aYG2to2QJBAIzDugOSTeQFpidCoewfa0XX4guF+WRf8wzyBC/XE6TY
3cIY05sjbpfiVwW/Cb8Z2ia8EgBTGN8HSIFOUQ2jRl4=
-----END RSA PRIVATE KEY-----
```

公钥文件rsa.pub的内容为：

```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCo7vV5xSzEdQeFq9n5MIWgIuLT
BHuutZlFv+Ed8fIk3yC4So/dy1f64iuYFcDeNU7eVGqTSkHmAl4AihDXoaH6hxoh
rcX0bCg0j+VoQMe2zID7MzcEd50FhJbuG6JsWtYzLUYs7/cQ3urZYwB4PEVa0WxQ
j2aXUMsxp6vl1CgB4QIDAQAB
-----END PUBLIC KEY-----
```

### **\*实例5：*** 公钥加密算法的实现

前面说过，公钥加密算法是由Crypto.Cipher子包下的PKCS1_v1_5.py或PKCS1_OAEP.py模块以已经存在的密钥对儿为密钥来实现的，现在常用的是PKCS1_v1_5。另外，我们前面提到过，使用对方的公钥加密，使用对方的私钥解密才能保证数据的机密性，因此这里以上面生成的公钥进行加密数据，以上面生成的私钥解密数据：

```
from Crypto import Random
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5 as Cipher_PKCS1_v1_5
import base64

# 数据加密
message = "This is a plain text."
with open('rsa.pub', 'r') as f:
    public_key = f.read()
    rsa_key_obj = RSA.importKey(public_key)
    cipher_obj = Cipher_PKCS1_v1_5.new(rsa_key_obj)
    cipher_text = base64.b64encode(cipher_obj.encrypt(message))
    print('cipher test: ', cipher_text)

# 数据解密
with open('rsa.key', 'r') as f:
    private_key = f.read()
    rsa_key_obj = RSA.importKey(private_key)
    cipher_obj = Cipher_PKCS1_v1_5.new(rsa_key_obj)
    random_generator = Random.new().read
    plain_text = cipher_obj.decrypt(base64.b64decode(cipher_text), random_generator)
    print('plain text: ', plain_text)
```

输出结果：

```
('cipher test: ', 'oq1sOSz4lS9PgrKmiwuAHs7iUhmWMvWdEbXLTOdhGtyIAr6xwmjtnBNpuvMVIM2Mz/O/xVzPu5L8nzUVW2THKpQinNwC7JWF0wnxrTHwKrmfXIIxxibQJS02obxkoEeqrjRo0b8V7yktYIV3ig2SlU3yjcr+lOFmRX+h6dE2TAI=')
('plain text: ', 'This is a plain text.')
```

### **\*实例6：*** 数据签名与签名验证的实现

同样，签名与验证相关算法的功能是由Crypto.Signature子包下的PKCS1_v1_5.py和PKCS1_PASS.py以这个密钥对而为密钥来实现的。数据签名的目的是为了防止别人篡改发送人的原始数据，其原理是：

- 1）先以单向加密方式通过某种哈希算法（如MD5，SHA1等）对要发送的数据生成摘要信息（数据指纹）；
- 2）然后发送方用自己密钥对儿中的私钥对这个摘要信息进行加密；
- 3）数据接收方用发送的公钥对加密后的摘要信息进行解密，得到数据摘要的明文A；
- 4）数据接收方再通过相同的哈希算法计算得到数据摘要信息B；
- 5）数据接收方对比数据摘要A与数据摘要B，如果两者一致说明数据没有被篡改过。

```
from Crypto.Hash import SHA
from Crypto.Signature import PKCS1_v1_5 as Signature_PKCS1_v1_5
message = "This is the message to send."
# 数据签名
with open('rsa.key', 'r') as f:
    private_key = f.read()
    rsa_key_obj = RSA.importKey(private_key)
    signer = Signature_PKCS1_v1_5.new(rsa_key_obj)
    digest = SHA.new()
    digest.update(message)
    signature = base64.b64encode(signer.sign(digest))
    print('signature text: ', signature)

# 验证签名
with open('rsa.pub', 'r') as f:
    public_key = f.read()
    rsa_key_obj = RSA.importKey(public_key)
    signer = Signature_PKCS1_v1_5.new(rsa_key_obj)
    digest = SHA.new(message)
    is_ok = signer.verify(digest, base64.b64decode(signature))
    print('is ok: ', is_ok)
```

输出结果：

```
('signature text: ', 'Bb4gvPU9Ji63kk3SSTiAVLctDbdb91DQuQKecbTcO2Jvpwbr7fr9sKZO+vZ8LIuSOdJkhbGX6swsSNwDI/CoT0xCdjiasfySPgsLyTcSWLyy9P7SrDuveH1ABUR/oYisvT1wFsScu0NMOBR8sLpboPk2DiW6n400jZq7t09xUyc=')
('is ok: ', True)
```



## 七、总结

------

上面讲了很多内容，现在我们简单总结下：

- 数据加密方式大体分为3类：单向加密、对称加密 和 公钥加密（非对称加密）。
- 这3类加密方式都各自包含不同的加密算法，如单向加密方式中包含MD5、SHA1、SHA256等，这些算法又称为“哈希算法”或“散列算法”或“数据摘要算法”
- Python内置的hashlib和hmac只提供了单向加密的各种算法实现，如果要做对称加密或者公钥加密操作需要安装第三方扩展模块，常用的是pycrypto模块。另外，hmac允许在使用哈希算法计算数据摘要时使用一个密钥。
- 随机数操作可以通过三个模块来做，Python内置的random模块和secrets模块（Python 3.6中才可用），还可以通过pycrypto模块中的Crypto.Random子包中的模块来完成。
- base64只适合编码小段数据，且不能用于数据加密（算法是公开的，且没有密钥，所有人都可以解码）
- pycrypto是一个加密算法库，几乎所有的加密算法都可以在它里面找到相应的实现模块。



## Crypto.Util package的使用

### Crypto.Util.Padding module

![1667011550246](C:\Users\豪哥\Desktop\assets\1667011550246.png)

函数使用：

`Crypto.Util.Padding.pad(*data_to_pad*, *block_size*, *style='pkcs7'*)`   style 没有设置默认使用 pkcs7的填充方式，第一个参数为 字节串数据，第二个参数是一组多少字节

### Cropty.Util.strxor Module 模块

对于字节串快速作异或运算

```python
Crypto.Util.strxor.strxor(term1, term2, output=None)
```

第三个参数可有可无，有的话只能是**output** (*bytearray/memoryview*) 

term1与term2必须有相同的长度

```python
Crypto.Util.strxor.strxor_c(term, c, output=None)
```

参数说明：

term为字节串，c为int类型，意思是term的每个字节都要与这个int类型的值进行异或，最后返回

### Cropty.Util.Counter Module模块

这个是为CTR模式专门设立的一个emm对象模块，是用来创建计时器的

![1667012532243](C:\Users\豪哥\Desktop\assets\1667012532243.png)

估计也用不到，就这吧

### Cropty.Util.number Module模块

#### 最大公因数 GCD（x,y）

#### 字节串转大整数：bytes_to_long(s)

![1667012798401](C:\Users\豪哥\Desktop\assets\1667012798401.png)

#### 大整数转字节串： long_to_bytes(n)

#### ceil_div (x,y)整除，但是向上保留，所以是ceil

#### 获得大质数：getPrime(N):

获得Nbits的大整数，返回的为int类型，默认使用的是get_random_bytes函数

#### 求逆：inverse （u,v）

#### 判断是否是素数：isPrime （N）

#### 获取整数的位数size （N）

#### 

## Cyrpto.Random Package 的使用

### Crypto.Random.get_random_bytes(*N*) 

返回N ==byte== 的字节串数组

### `Crypto.Random.random` module

#### 获取最多Nbit长的整数：`Crypto.Random.random.``getrandbits`(*N*) 

#### 获取范围内的大整数：`randint`(*a*, *b*) 

a\b都是大整数

#### `choice`(*seq*) 从序列随机选择

#### 从序列随机选择k个样本`sample`(*population*, *k*) 



## 实例：

### AES加密：

The following code generates a new AES128 key and encrypts a piece of data into a file. We use the [EAX mode](http://en.wikipedia.org/wiki/EAX_mode) because it allows the receiver to detect any unauthorized modification (similarly, we could have used other [authenticated encryption modes](http://blog.cryptographyengineering.com/2012/05/how-to-choose-authenticated-encryption.html) like [GCM](http://en.wikipedia.org/wiki/GCM_mode), [CCM](http://en.wikipedia.org/wiki/CCM_mode) or [SIV](http://tools.ietf.org/html/rfc5297)). 

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

data = b'secret data'

key = get_random_bytes(16)
cipher = AES.new(key, AES.MODE_EAX)
ciphertext, tag = cipher.encrypt_and_digest(data)

file_out = open("encrypted.bin", "wb")
[ file_out.write(x) for x in (cipher.nonce, tag, ciphertext) ]
file_out.close()
```

接收方：

```python
from Crypto.Cipher import AES

file_in = open("encrypted.bin", "rb")
nonce, tag, ciphertext = [ file_in.read(x) for x in (16, 16, -1) ]

# let's assume that the key is somehow available again
cipher = AES.new(key, AES.MODE_EAX, nonce)
data = cipher.decrypt_and_verify(ciphertext, tag)
```



### 产生RSA一对密钥

```python
from Crypto.PublicKey import RSA

key = RSA.generate(2048)
private_key = key.export_key()
file_out = open("private.pem", "wb")
file_out.write(private_key)
file_out.close()

public_key = key.publickey().export_key()
file_out = open("receiver.pem", "wb")
file_out.write(public_key)
file_out.close()
```

### RSA加密：

```python
from Crypto.PublicKey import RSA
from Crypto.Random import get_random_bytes
from Crypto.Cipher import AES, PKCS1_OAEP

data = "I met aliens in UFO. Here is the map.".encode("utf-8")
file_out = open("encrypted_data.bin", "wb")

recipient_key = RSA.import_key(open("receiver.pem").read())
session_key = get_random_bytes(16)

# Encrypt the session key with the public RSA key
cipher_rsa = PKCS1_OAEP.new(recipient_key)
enc_session_key = cipher_rsa.encrypt(session_key)

# Encrypt the data with the AES session key
cipher_aes = AES.new(session_key, AES.MODE_EAX)
ciphertext, tag = cipher_aes.encrypt_and_digest(data)
[ file_out.write(x) for x in (enc_session_key, cipher_aes.nonce, tag, ciphertext) ]
file_out.close()
```

```
from Crypto.PublicKey import RSA
from Crypto.Cipher import AES, PKCS1_OAEP

file_in = open("encrypted_data.bin", "rb")

private_key = RSA.import_key(open("private.pem").read())

enc_session_key, nonce, tag, ciphertext = \
   [ file_in.read(x) for x in (private_key.size_in_bytes(), 16, 16, -1) ]

# Decrypt the session key with the private RSA key
cipher_rsa = PKCS1_OAEP.new(private_key)
session_key = cipher_rsa.decrypt(enc_session_key)

# Decrypt the data with the AES session key
cipher_aes = AES.new(session_key, AES.MODE_EAX, nonce)
data = cipher_aes.decrypt_and_verify(ciphertext, tag)
print(data.decode("utf-8"))
```