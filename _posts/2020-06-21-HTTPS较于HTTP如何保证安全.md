---
layout: post
title: HTTPS较于HTTP如何保证安全
categories: Computer Network
description: 介绍HTTPS是如何工作的，以及相对于HTTP是如何保证传输过程中的数据安全。
keywords: Http、Https、对称加密、非对称加密
---

我们在工作中经常会听到和使用HTTP及HTTPS，并且我们都知道HTTPS能确保通信安全，但为什么HTTPS能保证安全，可能我们就无法将HTTPS工作的详细流程说出来了。基于此，本文就HTTPS工作的详细流程作一一介绍。

首先，在介绍HTTPS之前，我们需要先来回顾一下HTTP。  

### HTTP
HTTP协议是Hyper Text Transfer Protocol(超文本传输协议)的缩写，基于TCP/IP通信协议运行在应用层的协议。默认端口为80。其中，HTTP协议采取明文传输。 如图：  

![HTTP的正常明文传输](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWd9VfVEOtzmZAWn*JPB3HLP9bkeECw0JL9lJ*tEaDs1FzOTiyeYD37qAVCMbiSPqrQ!!/b&bo=bgM1AQAAAAADB3s!&rf=viewer_4)  


HTTP采用明文传输，客户端小明与服务端小红之间的信息传输没有做任何加密，很容易被中间人劫持。如图：  

![被劫持过的HTTP传输](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWTSuf*OejbMkVLY9UKrttsPfiH22FieWQ0aZiJ0oXNNFyP5SmcAigoXcuVG13WJGuw!!/b&bo=IQMuAgAAAAADByw!&rf=viewer_4)   

中间人小刚截取到了客户端小刚给服务端小红发送的消息并且进行了篡改导致服务端小红接收到了错误的信息产生误会。那我们这里就出现了一个问题，如何保证客户端小明和服务端小红之间信息传输的安全性呢？

### Q1：HTTP信息传输，如何保证其安全性？
我们可以想到加密，在客户端Client进行加密，在Server端进行解密，这样就能保持信息的安全。对通信内容加解密我们采用对称加密来完成，即客户端Client生成一个用于对称加密的秘钥Key，然后使用这个Key对通信内容进行加密，服务端Server再使用这个客户端生成的秘钥Key对加密内容进行解密，得到通信明文内容。如果中间人对我们通信的内容进行劫持，那么他拿到的是加密之后的内容，他没有对称秘钥Key，所以即使劫持了传输内容也无法解密出来。如图：  

![AES对称加密之后的传输过程](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWb3hZXNBTE4sQEMtkZGTrQsViTG1aE0smp5t5Zw3C.OetKVjLo6nn*XXBiNC7o8YzA!!/b&bo=OQMTAjkDEwIDByI!&rf=viewer_4)  

可是现在我们又会出现一个问题，我们该如何保证客户端生成的秘钥Key能顺利的发送给服务端并且不被中间人知晓并篡改呢？因为如果第一次传输秘钥Key采用明文，秘钥Key被中间人截获，中间人就可以对传输内容进行加解密进行篡改，那我们之后使用该秘钥Key做的一切内容加解密都白搭。所以现在问题就转化为了客户端如何将秘钥安全的传输告知给服务端。  

### Q2：如何安全的将对称加密的秘钥Key从客户端传输给服务端？  
由于对称加密无法首次安全的将秘钥Key传输给服务端，所以我们这里引入了另外一种加密方式：非对称加密。  

非对称加密算法需要两个密钥：公开密钥（publickey:简称公钥）和私有密钥（privatekey:简称私钥）。公钥与私钥是一对，如果用公钥对数据进行加密，只有用对应的私钥才能解密。因为加密和解密使用的是两个不同的密钥，所以这种算法叫作非对称加密算法。 非对称加密算法实现机密信息交换的基本过程是：甲方生成一对密钥并将公钥公开，需要向甲方发送信息的其他角色(乙方)使用该密钥(甲方的公钥)对机密信息进行加密后再发送给甲方；甲方再用自己私钥对加密后的信息进行解密。甲方想要回复乙方时正好相反，使用乙方的公钥对数据进行加密，同理，乙方使用自己的私钥来进行解密。(百度百科：[非对称加密](https://baike.baidu.com/item/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95/1208652?fr=aladdin))  

由上可知，我们可以在服务器端生成一对非对称加密秘钥（Public Key和Private Key）。然后，客户端生成对称加密的秘钥Key，客户端使用服务器生成的公钥Public Key对秘钥key进行加密，由于只有服务器端才拥有私钥。所以即便中间人截获了客户端发给服务器端的内容也无法解密出秘钥key,从而保证了秘钥Key的传输安全。过程如图：  

![对称秘钥Key的安全传输过程](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWTG3iMlSDixWrtG4lKyGCK3Ap81bwvJsE08PknfJRuA8dg1siNFtKDfgbZ9yL.uE2w!!/b&bo=vAScArwEnAIDByI!&rf=viewer_4)  

看到这里，我们可能会有2个疑问。  
* Q1：既然非对称加密能确保安全，那为什么我们还需要对称加密，为什么不都使用非对称加密来进行通信呢？  

* A1：非对称加解密耗时会远大于对称加密，对性能有很大的损耗。所以我们不可能对我们所有的通信内容都采用非对称加密。而只使用非对称加解密来进行秘钥的传递，通过秘钥来进行对称加密来进行通信内容的传递。

* Q2：图中的传输过程看起来确实可以确保中间人无法解密出秘钥Key，可是在过程2中也即服务器端传输公钥Public Key给客户端中，如果中间人截取到了公钥Public Key，并且使用自己的公钥Public Key2发送给客户端呢？由于客户端无法确定接收到的Public Key的真实性(也即是否是服务器端的Public Key)，这个时候如果采用中间人的公钥Public Key2对秘钥Key进行加密发送过去，中间人就可以使用他的私钥Private Key2来解密得到客户端的秘钥Key，同时中间人再伪装成客户端，使用从服务端截获的公钥Public Key对Key进行加密发送给服务端。此时由于中间人知道客户端与服务端通信加密的秘钥Key，就可以在通信的过程中进行数据的篡改。整个过程可能有点绕，我们还是通过图来理解： 

![非对称秘钥首次传输被截获过程](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWXikiUKTamIFByAxxqVv94u3P.WoIu7yYhwh7uKxyBV6cdTKarHK5639gdLedh6UuA!!/b&bo=bwQEAwAAAAADB04!&rf=viewer_4)  

所以非对称加密的问题是，服务器无法确保传输公钥给客户端的过程中不被中间人截取篡改。也即问题的本质是，无法保证网络间第一次通信不被中间人得知信息并篡改。就像之前我们为了第一次传输对称秘钥不被截获而引入非对称秘钥一样，现在我们又陷入了同样的困惑。那是不是需要再次采用一次非对称加密来确保首次公钥传输的安全性呢？那这样就会陷入鸡生蛋蛋生鸡的窘境了。

### Q3：如何解决确保使用非对称加密传输秘钥过程中被中间人截获篡改的风险问题？
为了解决上面的使用非对称加密传输秘钥过程中公钥可能被中间人篡改的问题，我们再次引入一个CA(Certificate Authority)证书颁发机构的概念。  

证书颁发机构（CA, Certificate Authority）即颁发数字证书的机构。是负责发放和管理数字证书的权威机构，并作为电子商务交易中受信任的第三方，承担公钥体系中公钥的合法性检验的责任。CA中心为每个使用公开密钥的用户发放一个数字证书，数字证书的作用是证明证书中列出的用户合法拥有证书中列出的公开密钥。CA机构的数字签名使得攻击者不能伪造和篡改证书。(出自于[CA认证百度百科](https://baike.baidu.com/item/CA%E8%AE%A4%E8%AF%81/6471579?fr=aladdin))  

有了CA之后，服务器会首先向CA机构申请数字证书，CA会使用CA私钥对服务器端的公钥进行非对称加密，然后加密后的公钥、证书颁发机构以及服务器端网址等信息通过摘要算法(例如SHA1，MD5等)生成摘要，再使用CA私钥对摘要进行加密形成证书签名，也即数字签名。然后将证书颁发机构、服务端网址、CA私钥加密的服务器公钥以及证书签名一起形成证书发送给服务器。  

服务器有了CA发的证书之后，等客户端请求服务端时就不再直接传送公钥过去了，而是将证书发给客户端。客户端拿到证书之后首先通过CA公钥进行解密。这里需要注意的一点是，客户端知道证书颁发机构之后就可以通过系统或者浏览器直接拿到CA的公钥，因为系统或者浏览器都会内置各CA机构的信息和公钥信息。  

客户端使用CA公钥解密之后会得到服务端网址、CA私钥加密的服务器公钥、以及CA私钥加密的证书签名。此时，客户端需要确保证书中的公钥信息没有被中间人篡改替换，所以需要进行验签。客户端会使用CA公钥再一次对加密后的证书签名进行解密得到摘要Abstract1。然后客户端再通过这些已知的明文信息采用同样的摘要算法自己重新生成摘要Abstract2。将两个摘要进行对比，如果一致则表明数据没有被篡改，可以放心的使用该公钥(即服务器的公钥)进行将对称加密秘钥Key进行加密，然后再发送给服务器端。(由于只有服务器有私钥，所以中间人及时截获也无法解密出秘钥Key)  

在这里，我们可能又会有几个疑问。

* Q1：中间人可能得到CA秘钥然后自己生成证书吗？
* A1：CA的秘钥是CA机构的公信力的象征，一旦CA私钥发生泄漏，那么该CA机构的公信力将不再存在。同时，浏览器会将该CA机构的公钥等信息移除。

* Q2：在服务器发送给客户端的证书中，中间人可能只修改加密的公钥信息却不修改其他信息来达到欺骗客户端的目的吗？
* A2：如果中间人只修改加密的公钥信息，由于中间人没有CA机构的私钥，所以他无法伪造数字签名。即如果他修改证书中的信息，客户端在验签的过程中生成的摘要和数字签名中的摘要就会不一致，无法通过验签，此时客户端就知道证书中的内容被修改。  

具体的过程，我们同样可以看图理解。  

![HTTPS安全传输过程](http://m.qpic.cn/psc?/V11GmsHW1oZK9k/ENmuKd2PHQoigBx2P9ktWczN6QPbRZqtv*yzdLrMsM2nP2pZqeVNhUCoQV6bSAdJrQos*x5lxw1wIrRoi*lmWw!!/b&bo=uQTuAbkE7gEDByI!&rf=viewer_4)  

至此，我们已经通过一步步的分析解决了HTTP明文通信的不安全问题。那么回过头来，我们之前提到的HTTPS又是怎样工作的呢。

### HTTPS协议
HTTPS （全称：Hyper Text Transfer Protocol over SecureSocket Layer），是以安全为目标的 HTTP 通道，在HTTP的基础上通过传输加密和身份认证保证了传输过程的安全性。HTTPS 在HTTP 的基础下加入SSL 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。 HTTPS 存在不同于 HTTP 的默认端口及一个加密/身份验证层（在 HTTP与 TCP 之间）。这个系统提供了身份验证与加密通讯方法。它被广泛用于万维网上安全敏感的通讯，例如交易支付等方面 。 (出自于[HTTPS百度百科](https://baike.baidu.com/item/https/285356?fr=aladdin))  

其实我们会发现HTTPS中的SSL证书也即我们之前提到的CA证书，其实HTTPS的流程和我们之前一步步的流程一样，也即上述的流程即为HTTPS的通信流程。但HTTPS具体的工作流程比我们上述的流程更复杂，但是这里我们是大概的了解认知HTTPS的工作过程以及它相比于HTTP如何保障网络传输的安全性。  


### 引用

* [漫画：什么是 HTTPS 协议？](https://zhuanlan.zhihu.com/p/57142784)
* [全网最透彻HTTPS](https://mp.weixin.qq.com/s/21JaXwdfSjItj5SgOwhapg)

