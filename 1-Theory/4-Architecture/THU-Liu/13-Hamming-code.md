> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zh.wikipedia.org](https://zh.wikipedia.org/wiki/%E6%B1%89%E6%98%8E%E7%A0%81?useskin=vector)

在电信领域中，**汉明码**（英语：**hamming code**），也称为**海明码**，是 (7,4)汉明码（英语：Hamming (7,4)）推广得到的一种线性纠错码，由理查德·卫斯里·汉明于 1950 年发明。相比而言，简单的奇偶检验码除了不能纠正错误之外，也只能侦测出奇数个的错误。汉明码是完备码（英语：perfect code），它在于它分组长度相同、最小距离为 3 的码中能达到最高的码率。

用数学术语来说，汉明码是一种二元线性码。对于所有整数 的非零列向量构成的。

历史
--

汉明码的发明者理查德·卫斯里·汉明在 1948 年，运用贝尔模型 V（Bell Model V）电脑于贝尔实验室（Bell Labs）工作。输入端是依靠打孔卡（Punched Card），这不免会造成些读取错误。在工作日，当机器检测到错误将停止并闪灯（flash lights），使得操作员能够解决这个错误。在周末和下班期间，没有操作者的情况下，机器只会简单地转移到下一个工作。

汉明在周末工作，他对于不可靠的读卡机发生错误后，总是不得不重新启动程序变得愈来愈沮丧。在接下来的几年中，他为了解决侦错的问题，开发了功能日益强大的侦错算法。在 1950 年，他发表了今日所称的**汉明码**，并且时至今日仍在修正错误内存上显示其应用价值。

### 汉明码之前

人们在汉明码出现之前使用过多种检查错误的编码方式，但是没有一个可以在和汉明码在相同空间消耗的情况下，得到相等的效果。

#### 奇偶

奇偶校验是一种添加一个奇偶位用来指示之前的数据中包含有奇数还是偶数个 1 的检验方式。如果在传输的过程中，有奇数个位发生了改变，那么这个错误将被检测出来（注意奇偶位本身也可能改变）。一般来说，如果数据中包含有**奇数个**1 的话，则将奇偶位设定为**1**；反之，如果数据中有**偶数个**1 的话，则将奇偶位设定为**0**。换句话说，原始数据和奇偶位组成的新数据中，将总共包含偶数个 1.

奇偶校验并不总是有效，如果数据中有偶数个位发生变化，则奇偶位仍将是正确的，因此不能检测出错误。而且，即使奇偶校验检测出了错误，它也不能指出哪一位出现了错误，从而难以进行更正。数据必须整体丢弃并且重新传输。在一个噪音较大的媒介中，成功传输数据可能需要很长时间甚至不可能完成。虽然奇偶校验的效果不佳，但是由于他只需要一位额外的空间开销，因此这是开销最小的检测方式。并且，如果知道了发生错误的位，若将该位取反，奇偶校验还可以恢复数据。

#### 五取二代码

五取二代码使用由 3 个 0 和 2 个 1 组成的五个位，以此提供十种可能的组合来表示数字 0-9。 该方案可以检测所有单比特错误、所有奇数字错误和一些偶数字错误（例如两个 “1”位的翻转）。 但是它无法自行纠正这些错误。

汉明码
---

如果一条信息中包含更多用于纠错的位，且通过妥善安排这些纠错位使得不同的出错位产生不同的错误结果，那么我们就可以找出出错位了。在一个 7 位的信息中，单个位出错有 7 种可能，因此 3 个错误控制位就足以确定是否出错及哪一位出错了。

汉明研究了包括五取二码在内的编码方案，并归纳了他们的想法。

### 通用算法

下列通用算法可以为任意位数字产生一个可以纠错一位（英语：**S**ingle **E**rror **C**orrecting）的汉明码。

1.  从 1 开始给数字的数据位（从左向右）标上序号, 1，2，3，4，5...
2.  将这些数据位的位置序号转换为二进制，1, 10, 11, 100, 101, 等。
3.  数据位的位置序号中所有为二的幂次方的位（编号 1，2，4，8，等，即数据位位置序号的二进制表示中只有一个 1）是校验位
4.  所有其它位置的数据位（数据位位置序号的二进制表示中至少 2 个是 1）是新的数据位
5.  每一位的数据包含在特定的两个或两个以上的校验位中，这些校验位取决于这些数据位的位置数值的二进制表示
    1.  校验位 1 覆盖了所有数据位位置序号的二进制表示倒数第一位是 1 的数据：1（校验位自身，这里都是二进制，下同），11，101，111，1001，等
    2.  校验位 2 覆盖了所有数据位位置序号的二进制表示倒数第二位是 1 的数据：10（校验位自身），11，110，111，1010，1011，等
    3.  校验位 4 覆盖了所有数据位位置序号的二进制表示倒数第三位是 1 的数据：100（校验位自身），101，110，111，1100，1101，1110，1111，等
    4.  校验位 8 覆盖了所有数据位位置序号的二进制表示倒数第四位是 1 的数据：1000（校验位自身），1001，1010，1011，1100，1101，1110，1111，等
    5.  简而言之，所有校验位覆盖了数据位置和该校验位位置的二进制与的值不为 0 的数。

采用奇校验还是偶校验都是可行的。偶校验从数学的角度看更简单一些，但在实践中并没有区别。

校验位一般的规律可以如下表示：

表中只给出了 20 个编码后的位（5 个奇偶校验位，15 个数据位）。观察上表可发现一个比较直观的规律：第 i 个检验位是第 2i-1 位，从该位开始，检验 2i-1 位，跳过 2i-1 位……依次类推。例如上表中第 3 个检验位 p4 从第 23-1=4 位开始，检验 4、5、6、7 共 4 位，然后跳过 8、9、10、11 共 4 位，再检验 12、13、14、15 共 4位……

要检查某一位的错误，则需检查某一位所包含的所有奇偶校验位。这种错误的模式被叫做伴随式错误。如果所有奇偶校验位是正确的，就没有错误。除此以外的情况，错误的奇偶校验位的位置的和将识别错误的位。例如，如果位置为 1、2、8 的奇偶校验位指示了一个错误，那么位置为 1+2+8=11 的位出错了。如果只有一个奇偶校验位指示了错误，那么该奇偶校验位自身出错了。

### 例子

对 11000010 进行汉明编码，求编码后的码字。

1. 列出表格，从左往右（或从右往左）填入数字，但 2 的次方的位置不填。

2. 把**数据**行有 1 的列的**位置**写为二进制。

3. 收集所有二进制数字，求异或。  ![0011\oplus 0101\oplus 1011=1101](https://wikimedia.org/api/rest_v1/media/math/render/svg/eecae15ebb137c6bb4f92f2887ffe8114d060cdd)

4. 把 1101 依次填入表格中 2 的次方的位置（低位在左）。

5. 所以编码后的码字是 101110010010。

带附加奇偶校验码的汉明码（SECDED）
--------------------

加一个比特在数列的最前面，采用奇校验码或偶校验码， 用以检验后面的汉明码是否有错。

(7,4)汉明码
--------

![](//upload.wikimedia.org/wikipedia/commons/thumb/b/b0/Hamming%287%2C4%29.svg/300px-Hamming%287%2C4%29.svg.png)Graphical depiction of the 4 data bits and 3 parity bits and which parity bits apply to which data bits

1950 年，汉明发明了 (7,4)代码。其编码由 4 资料比特到 7 位，增加三个奇偶校验码。(7,4)汉明码可以检测并纠正单比特差错，且也能检测双比特差错。

建立奇偶检验矩阵
--------

矩阵  ![{\mathbf  {G}}:={\begin{pmatrix}I_{k}|-A^{T}\\\end{pmatrix}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/1c5901ec71556ba64072c614ca021e2f12c0d195)被称为（标准）生成矩阵线性 (n, k)码。

和  ![{\mathbf  {H}}:={\begin{pmatrix}A|I_{{n-k}}\\\end{pmatrix}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/8510d77052063c9c73828f1a897eabbb0fcfd84b)被称为奇偶检验矩阵。

编码
--

范例

从上述矩阵我们有 2k=24=16 码词。

二进制码  ![\overrightarrow {x}](https://wikimedia.org/api/rest_v1/media/math/render/svg/bf766c96d7cd220945cafa78abe16f4e59a70014)的码词可以从  ![\overrightarrow {x}=\overrightarrow {a}G](https://wikimedia.org/api/rest_v1/media/math/render/svg/865b654d20cdc33880e8154bdda3e025577b3aa4) 得到。对  ![\overrightarrow {a}=a_{1}a_{2}a_{3}a_{4}](https://wikimedia.org/api/rest_v1/media/math/render/svg/bebbcd68c13ff8bfdf8b87332cf5d4be953f2177)和  ![a_{i}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0bc77764b2e74e64a63341054fa90f3e07db275f)存在  ![F_{2}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0fd17e0779153d765b40ebef91533489b87b2e37) (一个只有 0 和 1 的二元域）。

故此码表即是所有 4 个三元组（k 个三元组）。

因而，（1,0,1,1）编码为（_0_,_1_, 1,0,_0_, 1,1）。

(8,4)汉明码
--------

![](//upload.wikimedia.org/wikipedia/commons/thumb/1/13/Hamming%288%2C4%29.svg/300px-Hamming%288%2C4%29.svg.png)The same (7,4) example from above with an extra parity bit

(7,4)汉明码可以很容易地编码为一个 (8,4)码，通过在 (7,4)编码词（_参见 (7,4)汉明码_）上附加一个额外的奇偶位。

这可以用下面修正的矩阵相加：

 ![{\mathbf  {G}}:={\begin{pmatrix}1&1&1&0&0&0&0&1\\1&0&0&1&1&0&0&1\\0&1&0&1&0&1&0&1\\1&1&0&1&0&0&1&0\end{pmatrix}}_{{8,4}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/01ac5f7c7e2a96bb81818a781b16b6a9c0c404e3)

和

 ![{\mathbf  {H}}:={\begin{pmatrix}1&0&1&0&1&0&1&0\\0&1&1&0&0&1&1&0\\0&0&0&1&1&1&1&0\\1&1&1&1&1&1&1&1\end{pmatrix}}_{{4,8}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/4c3be5ead336970f86aca609d5161d3918d690bb)。

注意，  ![\mathbf{H}](https://wikimedia.org/api/rest_v1/media/math/render/svg/f017b876ed763037d8818ec5dfbbdc6703e0f683)并非用标准形式表示。为了得到  ![{\mathbf  {G}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/f6d9c60d3cf462a9812e9a9d021d17c7bc272a5a)，原子行操作能够被用来获得一个等价的矩阵对陈形式的  ![\mathbf{H}](https://wikimedia.org/api/rest_v1/media/math/render/svg/f017b876ed763037d8818ec5dfbbdc6703e0f683)：

 ![{\mathbf  {H}}=\left(\left.{\begin{array}{cccc}0&1&1&1\\1&0&1&1\\1&1&0&1\\1&1&1&0\end{array}}\right|{\begin{array}{cccc}1&0&0&0\\0&1&0&0\\0&0&1&0\\0&0&0&1\end{array}}\right)_{{4,8}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/51bbbfa877cdbd9d28cc61497b75247b968720e1)。

(11,7)汉明码
---------

相关条目
----

*   格雷码
*   汉明距离
*   前向纠错
*   里德-所罗门码

参考文献
----

*   Moon, Todd K. Error Correction Coding. New Jersey: John Wiley & Sons. 2005 [2009-06-18]. ISBN 978-0-471-64800-0. （原始内容存档于 2008-04-06）. 
*   MacKay, David J.C. Information Theory, Inference and Learning Algorithms. Cambridge: Cambridge University Press. 2003-09. ISBN 0-521-64298-1. （原始内容存档于 2016-02-17）. 
*   D.K. Bhattacharryya, S. Nandi. An efficient class of SEC-DED-AUED codes. 1997 International Symposium on Parallel Architectures, Algorithms and Networks（ISPAN '97）: 410–415. doi: 10.1109/ISPAN. 1997.645128. 

外部链接
----

*   CGI script for calculating Hamming distances（from R. Tervo, UNB, Canada） （页面存档备份，存于互联网档案馆）

1.  **^** See Lemma 12 of (PDF). [2016-09-01]. （原始内容存档 (PDF)于 2021-04-17）.