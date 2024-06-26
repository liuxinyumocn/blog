# HTTPS 是如何保障我们的通信安全的？

​		HTTPS是基于HTTP改进的加密通信协议，而且如今我们几乎所有的应用通信都采用了该协议实现，比如你浏览网页、看视频、发电子邮件等等。那么HTTPS为什么能够保障我们的通信？使用公司的代理也可以保障吗？

​		如果你知道他的工作原理自然就知道他是如何安全的加密并且也知道他能保障什么内容，不能保密什么信息。本文将不使用专业的学术角度，生动形象的介绍HTTPS是如何保障我们的通信安全的。



## 第一章 HTTP是怎么工作的

​		小刘同学（客户端）想浏览一个网站资源（http://小明同学/apple.txt），「 http://小明同学/apple.txt 」中 http 是协议名，小明同学是被询问的主机地址，apple.txt 是主机上的资源内容。那么小刘同学的消息将先从自己的口中（小刘同学的网卡）出发，经过小张、小李（路由器、交换机……）口口相传到小明同学的耳朵里，小明同学于是将自己的 apple.txt 内容说出去，再经过了小李、小张原路返回给小刘同学，于是小刘同学就看到了 apple.txt 的内容了，这就是 HTTP 的原理。

​		但是很明显，在上述的过程中，小刘同学的确得到的 apple.txt 资源内容，但同时面临两件事情，首先 apple.txt 是小张小李协助传达的，小张小李本身也知道了这个资源的内容，并且如果小张小李恶意的篡改了内容，小刘同学也是浑然不知的，这也就是早期 HTTP 请求的缺陷。

​		因此在 HTTP 请求中，网关、路由器、交换机等设备，均能够得知用户访问了哪里，访问了什么，甚至还可以篡改数据内容。

## 第二章 密码学的两种加密方式

​		为了更好的介绍后面内容，需要介绍两种密码学加密手段，最常见的就是对称加密，对称加密的特点是加解密的过程仅需要1个密钥即可，加密则是密钥的正向使用，解密就是密钥的反向使用。

​		随着密码学的发展，诞生了另一种加密方式，也就是非对称密钥。非对称密钥的特点是，密钥由两个完全看不出联系的密钥A、B构成，由A加密的信息，再也无法由A解密，必须使用B来解密，同理被B加密的信息也必须使用A来解密。非对称密钥是由两个密钥对构成，密钥对之间没有主次区分，但往往这2个密钥一个公开给所有人，另一个只有持有者知道，所以就分为公钥与私钥。

## 第三章 结合了非对称加密的HTTP

​		继续看第一章的案例，小明同学是服务器，他提前准备好了一对密钥AB，其中A可以公开告诉班级里的任何人，而B只有他心里知道，那么是不是一个消息经过A加密的信息，整个班级只有小明自己可以解密看到内容了。于是小刘同学这次请求前，先通过小张小李协助问一下小明同学你准备好的A是什么，于是小刘同学就拿到了A密钥，在拿到A密钥后的对小明发消息，小张小李就已经看不懂了，所以只能协助传达给小明同学后，小明使用心里的B私钥解密安全的处理消息。

​		但，这里就有个问题，由于A是公开的，所以不仅仅是小刘同学，小张小李也知道A，那么尽管小刘说的话他们听不懂，但小明如果是使用B加密的消息，还是能看懂小明说了些什么。诶！不要忘记，刚刚介绍了非对称密钥的同时还说了对称密钥，所以实际上，小刘同学拿到密钥A的时候，告诉的那段小李小张听不懂的话，实际上是小刘同学生成的一个对称密钥T，这个密钥T安全的抵达小明同学后，此后双方都使用了由小刘同学生成的密钥T加密的信息了。因此，真正的 apple.txt 资源发送前被密钥T加密后，抵达小刘同学后再使用T解密，所以无论是哪边的信息，小张小李由于没有密钥T，都是听不懂的。

## 第四章 这就安全了吗？（CA证书）

​		第三章的故事应该都看懂了吧，非常巧妙。但是……你仔细想想这样做就安全了吗？

​		让你万万没想到的是，小刘同学也是个传话的！并不是这个请求最初的请求者小新。小刘同学伪装了小明的一切能力，接收到小新的请求后，也做了加密来配合小新的各种响应，接收到信息后又伪装成了客户端去代替小新请求真正的资源地址小明这里。对于小新来讲，他完全不知道他的通话已经全都泄露了！

​		在这里小刘同学是一个伪装的中间人，发起了基于密钥的中间人攻击！

​		这个问题最关键的点在于什么？小新拿到的密钥A，他并不知道是来自小明的（正确），还是小刘替换后的（伪装成小明），而小新的通信还必须经过小刘的传话。这时候我们思考一个场景，当你去银行办理业务的时候，你是如何证明你是这个银行卡的主人？——银行柜员会要求你提供身份证来证明你。那么为什么身份证可以证明你的身份？我拿出一个美国身份证可以证明吗？显然必须是中华人民共和国为你颁发的证件才可以！所以这个过程中，银行是信任中国政府，中国政府为你颁发了身份证，而银行看到的了身份证通过验证身份证的真假就知道你的身份了。

​		那么小新与小明的关系就可以理解为银行（小新）与客户（小明），因为小新要确保小明的密钥A就是小明的。这时候就要引入一个CA证书的信任机构C，信任机构C提前给所有的电脑都预置了一个检查函数，小明的密钥AB需要提前去信任机构生成一个校验码，也就是说 小明的信息+公钥A 经过机构C的检查函数必须要等于校验码，而校验码的生成也是通过非对称密钥才能生成，那么中间人小刘同学如果篡改了公钥A，却因为没有信任机构C的校验码生成私钥无法同时给出正确的校验码，那么自然就无法通过小新自己使用检查函数的验证了！

​		至此，确保主机的身份以及双向通信的安全就全部保障了。

## 所以公司的代理能知道什么？

​		了解了HTTPS的工作原理我们自然也就知道了什么数据能被知道，什么不能。首先，小新请求小明这件事情中，目的地是是整个传话过程中所有人都知道的，大家都知道小新要问小明某些事情，只是不知道问了什么，这是无法避免的。也就是说，使用公司的代理，公司是知道你去了哪些网站，但是并不知道你访问了网站内的哪些内容。其次，上述过程中，确保通信安全的基础是CA证书的信任机构是安全的，如果你人为的安装了根证书，那么还是会被这个机构进行数据的解密，于是我们就要观察浏览器中的根证书到底是来自哪个机构的，如果是正规机构的话，说明本次通话还是安全的！公司的代理的证书只是用于验证员工身份，实际的浏览器通信的证书还是来自网站的申请的SSL证书，因此并没有监听我们的通信内容。
