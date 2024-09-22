# 密码学基础与数据安全

### 基本概念

- **常用缩写**

    sk: secret key 密钥

    m: 明文

    c: 密文

    M: 明文空间

    K: 密文空间

- **算法构成**

    密码学算法通常由以下三个部分构成：

    密钥生成 Gen()->sk

    加密 Enc(sk,m)->c

    解密 Dec(sk,c)->m

- **算法的Correctness**

    对于
    $$
    \forall m \in M, \, sk \in K, \, P[\text{Dec}(sk, \text{Enc}(sk, m)) = m] =1 
    $$
    即明文经过一次加密解密后内容不变，则可以认为算法是正确的(可行的)。

- **Power of adversary**

    算法假定的攻击者的“能力”，如是否知道加密算法。

    >Kerckhoffs原则

    >【柯克霍夫原则】（Kerckhoffs’sprinciple）由奥古斯特·柯克霍夫在19世纪提出：即使密码系统的任何细节已为人悉知，只要密钥（key）未泄漏，它也应是安全的。

<!-- ### 可证明安全

算法的安全证明由以下三部分构成：

    Security Definition 安全性定义

    Mathematic Assumption 利用公认的数学难题设计算法

    Reduction 如果加密算法π可以被A算法破解，那么就可以归纳出一个算法B=R(A)解出上述难题

所以如果不存在B算法，也就不会找到A算法。

即只要利用的数学难题不被攻破，这个算法就是安全的。 -->

<!-- ###古典密码

- **凯撒密码**

    将明文字母表向左或向右移动一个固定数目的位置得到密文：

    $$
    C = \left( M - A + sk \right)\mod 26 + A
    $$

    C为密文的ascii码，M为明文的ascii码，A为A或a对应的ascii码。

    凯撒密码一共只有26种不同的加密方式，容易被暴力破解。 -->
