# 协议常见问题解答

很多人都问关于Minecraft[协议](http://cthuwork.github.io/minecraft-protocol/)的问题，所以我们在这个文档里写一些大家常问的一些问题。如果你仍然有疑问，请到#mcdevs on irc.freenode.net 找我们。

* 1 协议文档完工了吗？
* 2 正常的客户端登陆顺序是怎样的？
* 3 我觉得我什么都没问题，但是………
  * 3.1 ……玩家不生成！
  * 3.2 ……我的客户端收不到完整的区块！
  * 3.3 ……所有连着的客户端都发送垃圾信息而且好像失控了！

## 协议文档完工了吗？
根据你的定义来说的话，**是的**！所有已知的数据包类型以及他们的布局都写在那篇文档里。其中可能忽略了一些细节，但我们已经把你编程要用的都写出来了。我们有监测新版本并且自动通知的的检测器，这使得我们可以很快的完成新数据包文档的撰写。

## 正常的客户端登陆顺序是怎样的？
```这里的信息并非最新的
```
我们推荐的登陆顺序如下，C表示客户端，S表示服务端。

 1. 客户端连接服务器
 2. **C->S**：握手状态=2
 3. **C->S**：登陆开始
 4. **S->C**：请求加密Key
 5. （客户端认证）
 6. **C->S**：回应加密Key
 7. （服务端认证，两边都启用了加密）
 8. **S->C**：登陆成功
 9. **S->C**：加入游戏
 10. **S->C**：出生坐标（“家”的坐标，不是客户端登陆的出生点）
 11. **S->C**：玩家能力
 12. **S->C**：告诉客户端已经准备好通过发送坐标以及视角包来出生
 13. **C->S**：发送坐标+视角数据包来确认出生位置
 14. **S->C**：背包，地图，区块，实体等等

## 我觉得我什么都没问题，但是………

### ……玩家不生成！
经过发送一系列一般情况下的数据包（握手，登陆，背包，方向，区块）后，你需要发给玩家初始位置来让他们离开“载入地图”屏幕。

#### 注意，以下步骤发生后，Minecraft客户端才会生成玩家。

 1. 握手（参照协议加密）
 2. 发送出生位置PDU
 3. 发送玩家位置和视角PDU
以上是1.4.5的形式，发送PDU给客户端是一个很好的在允许玩家生成前通知周围玩家的形式 

## ……我的客户端收不到完整的区块！
*主文章：[如何写一个客户端](http://wiki.vg/How_to_Write_a_Client)*

标准的Minecraft服务端只会在你的客户端发送玩家状态更新包后发送完整的区块（任何通过位置和视角(0x0D)的玩家(0x0A)）。

## ……所有连着的客户端都发送垃圾信息而且好像失控了！
对于新版本的客户端来说，你的服务端需要先发送49个区块，而不是一个区块。发送一个7x7的方形区块，在玩家出生前标记好中心。

