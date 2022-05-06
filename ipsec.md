![IPsec](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-framework-protocols.png).

概述

**1. IKE 密钥交换**

  IKE构建通信隧道，但不验证，加密数据。

  - IKE阶段1
  
  协商加密，身份验证，散列等参数。生成参数集合SA(安全关联)。
  
  ![IKE1](https://cdn.networklessons.com/wp-content/uploads/2015/08/r1-r2-ike-phase-1-tunnel.png)
  
  - IKE阶段2
  
  在IKE阶段1隧道内，建立IKE阶段2隧道
  
  ![IKE2](https://cdn.networklessons.com/wp-content/uploads/2015/08/r1-r2-data-through-ipsec-tunnel.png)

**2. 使用另外协议保证身份验证，和完整性，加密**
 
  AH,ESP. 都提供身份验证和完整性。ESP还提供加密。
  
	
具体
**IKE**

IKE有两个版本，IKEv1,IKEv2;

IKE阶段1：

step1:协商

* **散列算法**：MD5，SHA。完整性
*	**身份验证**：预共享密钥，或数字证书
*	**DH**：DH密钥交换参数。
* **Lifetime**：隧道生命周期
* **加密算法**：DES，AES

step2:DH密钥交换

生成共享密钥。

step3:身份验证

双方经算法验证成功后，完成了1阶段隧道建立。此时，双方可以在隧道上接收和发送。

**阶段1的三个step可用两种模式完成：**

1. main mode

IKEv1使用6个msg完成。

**协商**

![msg1](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-1.png)

* 可看到发起者192.168.12.1想要UDP传输。

* SPI标识SA集合，即标识这次隧道建立。

* prosoal num.表示第1次提议。

* 下面有很多发送方提议的参数。

![msg2](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-2.png)

响应者同意参数，并设置自己的SPI。

**DH密钥交换**

![msg3](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-4.png)

![msg4](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-4.png)

* key exchange data 就是 g**a;
* nonce 

**身份验证**

![msg5](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-5.png)
![msg6](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-6.png)

因为已经建立了共享密钥，这两个消息被加密，无法看到。

2. Aggressive mode


  
  
  
