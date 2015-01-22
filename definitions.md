# 定义

## 类型定义
全部发向网络的数据都遵守大端序，这意味着字节将按照高位到地位的顺序发送。大部分现在的计算机都是使用的小端序，所以有可能需要在发送信息前交换字节顺序。

除了字符串类型和元数据以外的类型，将会被使用一个自定义的方法解码，这些数据类型将会使用Java的[DataInputStream](http://download.oracle.com/javase/1.4.2/docs/api/java/io/DataInputStream.html)和[DataOutputStream](http://download.oracle.com/javase/1.4.2/docs/api/java/io/DataOutputStream.html)

<table>
	<tr>
		<th>类型</th>
		<th>大小(B)</th>
		<th>范围</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>bool</td>
		<td>1</td>
		<td>0或者1</td>
		<td>值只可能为true(0x01)或者false(0x00)</td>
	</tr>
	<tr>
		<td>byte</td>
		<td>1</td>
		<td>-128 到 127</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>short</td>
		<td>2</td>
		<td>-32768 到 32767</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>int</td>
		<td>4</td>
		<td>-2147483648 到 2147483647</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>long</td>
		<td>8</td>
		<td>-9223372036854775808 到 9223372036854775807</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>128位长整形</td>
		<td>16</td>
		<td>0 到 340282366920938463463374607431768211455</td>
		<td>有符号，补码存储，在[0x2C](#Spawn Global Entity)。在原版服务端中其实是通过发送两个长整形实现的</td>
	</tr>
	<tr>
		<td>float</td>
		<td>4</td>
		<td>同Java中的float</td>
		<td>单精度32位IEEE754浮点类型</td>
	</tr>
	<tr>
		<td>double</td>
		<td>8</td>
		<td>同Java中的double</td>
		<td>双精度64位IEEE754浮点类型</td>
	</tr>
	<tr>
		<td>string</td>
		<td>长于2，短于240</td>
		<td>---</td>
		<td>UTF-8编码的字符串，首位是字符串长度</td>
	</tr>
	<tr>
		<td>VarInt</td>
		<td>可变</td>
		<td>https://developers.google.com/protocol-buffers/docs/encoding#varints </td>
		<td>中文解释：http://zlx19900228.iteye.com/blog/1058659</td>
	</tr>
	<tr>
		<td>VarLong</td>
		<td>可变</td>
		<td></td>
		<td>除了变成长整形以外和VarInt一样</td>
	</tr>
	<tr>
		<td>metadata</td>
		<td>可变</td>
		<td>http://wiki.vg/Entities#Entity_Metadata_Format</td>
		<td>中文翻译TODO</td>
	</tr>
	<tr>
		<td>Slot Data</td>
		<td>可变</td>
		<td>http://wiki.vg/Slot_Data</td>
		<td>中文翻译TODO</td>
	</tr>
	<tr>
		<td>Position</td>
		<td>8</td>
		<td></td>
		<td>见下方解释</td>
	</tr>
	<tr>
		<td>UUID</td>
		<td>16</td>
		<td></td>
		<td>由两个长整形组成，this.writeLong(uuid.getMostSignificantBits()); this.writeLong(uuid.getLeastSignificantBits());</td>
	</tr>

</table>

## Position
被分成三部分的64位长整形

x: 26位最高有效位

z: 26位最低有效位

y: 其中的12位

按照以下方式编码

```java
((x & 0x3FFFFFF) << 38) | ((y & 0xFFF) << 26) | (z & 0x3FFFFFF)
```

可以按照以下方式解码

```java
long val; // val是一个坐标
x = val >> 38;
y = (val >> 26) & 0xFFF
z = val << 38 >> 38
```
## 定点小数
一些数据是以[定点小数](https://en.wikipedia.org/wiki/Fixed-point_arithmetic)存储的, where a certain number of bits represents the signed integer part (number to the left of the decimal point) and the rest represents the fractional part (to the right). Floating points (float and double), in contrast, keep the number itself (mantissa) in one chunk, while the location of the decimal point (exponent) is stored beside it.

Essentially, while fixed-point numbers have lower range than floating points, their fractional precision is greater for higher values. This makes them ideal for representing global coordinates of an entity in Minecraft, as it's more important to store the integer part accurately than position them more precisely within a single block (or meter).

Coordinates are often represented as a 32-bit integer, where 5 of the least-significant bits are dedicated to the fractional part, and the rest store the integer part.

Java lacks support for fractional integers directly, but you can represent them as integers. To convert from a double to this integer representation, use the following formulas:

```
 abs_int = (int)double * 32;
 ```
And back again:
```
 double = (double)abs_int / 32;
```

## 协议版本

请查看[协议版本号](http://wiki.vg/Protocol_version_numbers)来获取旧版本的信息。

<table>
  <tr>
    <th>Minecraft版本</th>
    <th>协议版本</th>
  </tr>
  <tr>
    <td>1.8.1</td>
    <td rowspan="2">47</td>
  </tr>
  <tr>
    <td>1.8</td>
  </tr>
  <tr>
    <td>1.7.6</td>
    <td>5</td>
  </tr>
  <tr>
    <td>1.7.2</td>
    <td>4</td>
  </tr>
</table>

## 其他定义
