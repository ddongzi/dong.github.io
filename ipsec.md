
# 概述

## IKE 密钥交换

  * IKE构建通信隧道，但不验证，加密数据。

  ### IKE阶段1
  
  协商加密，身份验证，散列等参数。生成参数集合SA(安全关联)。
  
  ![IKE1](https://cdn.networklessons.com/wp-content/uploads/2015/08/r1-r2-ike-phase-1-tunnel.png)
  
  ### IKE阶段2
  
  在IKE阶段1隧道内，建立IKE阶段2隧道
  
  ![IKE2](https://cdn.networklessons.com/wp-content/uploads/2015/08/r1-r2-data-through-ipsec-tunnel.png)

## 2. 使用另外协议保证身份验证，和完整性，加密
 
  AH,ESP. 都提供身份验证和完整性。ESP还提供加密。
  
  都支持两种模式：传输模式，隧道模式。
  
	
# IKE

- IKE有两个版本，IKEv1,IKEv2;

## IKE阶段1：建立阶段1隧道

### 3步

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

### 阶段1可用两种模式完成：

1. main mode

- IKEv1使用6个msg完成,每个步骤2msg。

1.1. **协商**

![msg1](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-1.png)

* 可看到发起者192.168.12.1想要UDP传输。

* SPI标识SA集合，即标识这次隧道建立。

* prosoal num.表示第1次提议。

* 下面有很多发送方提议的参数。

![msg2](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-2.png)

* 响应者同意参数，并设置自己的SPI。

1.2. **DH密钥交换**

![msg3](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-4.png)

![msg4](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-4.png)

* key exchange data 就是 g**a;
* nonce 

1.3. **身份验证**

![msg5](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-5.png)
![msg6](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-main-mode-message-6.png)

因为已经建立了共享密钥，这两个消息被加密，无法看到。

2. Aggressive mode

- 需要3个msg

![激进模式 msg1](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-aggressive-mode-message-1.png)
* 发送者发送 协商参数，DH密钥交换
* 身份标识

![激进模式 msg2](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-aggressive-mode-message-2.png)
* 响应者DH密钥交换
* 发送自己身份的hash

![激进模式 msg2](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ikev1-aggressive-mode-message-31.png)
* 发送自己身份的hash

  
## IKE阶段2：建立阶段2隧道

### 阶段2只有一种模式：quick mode

step1:协商

类似于阶段1.协商参数：

* IPsec协议：AH还是ESP？
* 封装模式：传输模式还是隧道模式？
* 加密算法：DES，AES？
* 身份验证算法：MD5,SHA?
* Lifetime:
* （可选）DH交换：即生成新的共享密钥

需要3个msg完成，因为此时隧道1数据被加密，所以看不到。

![IKE阶段2隧道-msg1](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ike-quick-mode-message-1.png)
![IKE阶段2隧道-msg2](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ike-quick-mode-message-2.png)
![IKE阶段2隧道-msg3](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ike-quick-mode-message-2.png)

<br/>

# IPsec协议

- AH，ESP是用来实际保护数据的两种协议。
- 传输模式：协议位于IP协议上层，添加新的头部和尾部
- 隧道模式：协议位于IP协议下层，在IP数据包之后添加添加新的头部尾部

## AH（认证头）协议

- 提供身份验证和完整性，不提供加密。

1. **运输方式**

- 在IP头部后面添加另外添加一个AH头。

- AH协议位于IP协议上层

![AH协议运输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-ah-transport-mode-ip-packet.png)
![AH协议运输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ipsec-ah-transport-mode.png)

- 可以看到在ICMP头部和IP头部之间添加了AH头。
- 可以mac目前所有内容
- AH头5个字段：
* Next header:指向上层协议。
* Length：AH头长度
* SPI（安全参数索引）：标识属于哪个隧道
* Sequence：
* ICV：整个数据包的Hash

2. **隧道模式**

- 在原始IP数据包之前添加新的IP头部。

- AH协议位于原始IP协议下层

![AH协议隧道模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-ah-tunnel-mode-ip-packet.png)

- 可以看到上面两部分为添加的新IP头，AH头
- IPheader：
* Protocol：指向上层协议
- AHheader：
* Next header：指向上层协议。IPIP标识新的IP头

## ESP协议：

1. **传输模式**

- “ESP协议位于IP协议上层“

- 在原始IP头后添加一个ESP头
- ESP trailer 和前面一起加密
- ESP auth和前面一起 认证
- 加密和认证都不包含IP头，即不能认证整个IP数据包

![ESP协议传输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-esp-transport-mode-ip-packet.png)
![ESP协议传输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ipsec-esp-transport-mode.png)

2. **隧道模式**

![ESP协议隧道模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-esp-tunnel-mode-ip-packet-1024x256.png)

![ESP协议隧道模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-esp-tunnel-mode-ip-packet-1024x256.png)

## AH和ESP同时使用
- ESP提供加密
- AH提供所有数据的Mac

1. **传输模式**

![AH,ESP同时使用 传输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-ah-esp-transport-mode-ip-packet-1024x257.png)

![AH,ESP同时使用 传输模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ipsec-ah-esp-transport-mode.png)

2. **隧道模式**
![AH,ESP同时使用 隧道模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/ipsec-ah-esp-tunnel-mode-ip-packet-1024x224.png)
![AH,ESP同时使用 隧道模式](https://cdn.networklessons.com/wp-content/uploads/2015/08/wireshark-capture-ipsec-ah-esp-tunnel-mode.png)


  
