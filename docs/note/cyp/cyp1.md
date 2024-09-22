# 可证明安全

## 原理

算法的安全证明由以下三部分构成：

    Security Definition 安全性定义

    Mathematic Assumption 利用公认的数学难题设计算法

    Reduction 如果加密算法π可以被A算法破解，那么就可以归纳出一个算法B=R(A)解出上述难题

所以如果不存在B算法，也就不会找到A算法。

即只要利用的数学难题不被攻破，这个算法就是安全的。

下面详细讨论这三部分：

## 安全性定义

在A与B使用加密通信的过程中，明文为M，密钥SK，密文为C，存在一个窃听者。

我们把π = {Gen, Enc, Dec, M, sk}称作一个密码体系

现在我们讨论窃听者在什么情况下不能得到明文就可以认为加密是安全的：

**1. 密钥sk不能被窃听者获取**

即使没有密钥，一些简单的古典加密仍然可以通过词频分析等方式破解密文，因此这种情况不能确保安全。

**2. 传输的所有信息都被严格保密无法窃取**

显然现在的传输手段难以实现绝对保密。

**3. 不会得到先验信息以外的信息，即得到密文后不会获得更多信息**

- **香农理论**

一个密码体制具有完善保密性，如果 \\( \forall t \in M, c \in C \\)，有：
$$ Pr[m=t] = Pr[m=t \mid \text{Enc}(sk,m)=c] $$

    P: 是标准的概率符号，通常在更基础的概率论中使用。例如，P(A)表示事件A发生的概率。这是最常见的表示方式。
    Pr: 也用于表示概率，但它往往出现在更加形式化或算法相关的文献中，特别是密码学、计算机科学等领域。Pr 强调概率是一个函数或运算符，通常用来表达复杂的概率事件。这里Pr[...] 的形式，表示对特定条件下的概率计算。

也就是说，给定密文c,明文m的后验概率等于明文m的先验概率。


- **完美安全**

对于任意的 \\(m_1, m_2 \in M, c \in C\\)，我们有：

$$
Pr[\text{Enc}(sk, m_1) = c] = Pr[\text{Enc}(sk, m_2) = c]
$$

**定理：π是完美安全，当且仅当他是香农安全。**

符合上面的一个例子：

给定集合 \\( M = \[0,1\]^n \\) 和 \\( K = \[0,1\]^n \\)，定义如下：

- 生成密钥函数 \\( \text{Gen}() \\) 生成一个密钥 \\( sk \in K \\)
- 加密函数 \\( \text{Enc}(sk, m) = sk \oplus m \\)
- 解密函数 \\( \text{Dec}(sk, c) = sk \oplus c \\)

不难证明(应该不难)，这个加密是完美安全的。

我们发现，在这个例子中，sk的大小和M相等，那么，我们是否可以让sk变小呢。

答案是不能的，例如在维吉尼亚加密中，密钥是重复使用的，如asdasdasd……循环使用，这样虽然避免了词频破解，但是当明文足够大的时候，会出现相同词语遇上同一串密钥的情况，产生了多个相同的密文，这样就有可能通过计算相同密文出现间隔的公因数来得到密钥可能的长度。这里其实已经不符合“得到密文后不会获得更多信息”的要求了。

下面是在CTF学习中的一个简单的维吉尼亚密码问题：

题目细节不表，需要的时候在下面指出。

    ur {gY ),yPT	Yb;85SY8 o:|k'y^> %n` f@XP b	zbJY?] 8Q" [CP	|;o {gY w*)`qN
    8 28S1}Q^:L *-< T3" WF0 UQqg_ /LHO 6W^R|C.]X ,y w#:@/f X&t (l5H {( vJA.]V+r 0I7m(.a( 	Y ;_5 q:]?:7 K&V0}p
    %\@ m?N Gf[+5 1|W{ <BP3 0F_ $BrUW\ u6 K4923GE L.[ 6c\ :p"~ m{ \FX>_Aw B{}7:(-L S]W Fy!9Cf|[U' ^Y& r& G{c6 	jA;V+o|RW".eZK m	G% +5{*7:+ )C %|;	@ 2YF!{ Yw>JT E2ZC$ Gp7 *-<0}Z
    m#f@X QP> _4S2w sQx ~qsYFqLKHQ
    * 0U qNt ie[ k\W" 1j y7nqVu`BF!. Yw>hyE9 W&|lN )k cz^G 0w-) ,aQu kt*,5^`z% 9nh( m5}c YY:lX) l"9ww K{*x ?L[ .q7 	yR~ m{ &0Z 7t]%x x8]a"7huk8EEf wXy pF,X	\t]8x &:% v|hQ&8EqI W# !]FQ /V!( 2S lf+3?C c:i {nIdF")* Z>Y} 9^ 
    Vc%d)hy xTq {gYm? rkT DAxu BCyl L,A j76GM" Mr0'mws> px	86G7 G |h6 8T:p"g"?O hFTxt848wWn$ ?L[ .q7dG}- o ]X)?y7Jt] ^! <+6 Xj9	|! Z"BBm".DNXOMI _8E 6W6p B#47!,_)aVv|+m~/ 
    8 8}!Uw]6p1 .q7dG} )oU ZrQ ^J xe[O Vl E[ L:Xs, DWW 0&"ou/t` Pw Y8W :,~uI !Tc""Y){W\ m>Y}*
    &k <+6 qC[;z;o*" ": W\0 Gk%LlgZ {( L[-Pt hFV0g 8|' 07/t8;l8w <f " Db:7!a_ W# ?.:`q.tf[92AN$ {"n A-WE+_D"oD7z 	P> K/?/AN\W(-[ #d<EGc "yF! fJN }bL9 LJ7"<K 	S
    T9_ "yF! "`Nxt8;8N Y8W Rth %|T+ K`cfeZK Z^J /E W`}:+: }.
    <1p_D< :lX sX >4L B^n c6d1B#, T* {gY e/) X&
    } f5l
    {:u:7 P37 ay=PCa"zpKNxtf;_5wn\lN [-AO^ }u|WBf0 fF >zt@Y/Ld7 %.[ .]w}:I"~ 8?0"oqWn ;_5 UwW R| *OV!p_{@? U]m?P bb	9 HC7 %.[ Hy{O I=IXK ,f( wVpU .AY o(3)V ]71q {gY @Zy u6 }E8 SU:W?:[CP3 6}c{eBZ _mh 	Y3xLBA5\ G.fBq47 ,@" .)8,hNX> p[28SJ7 <@ B#
    }}E%gWX |Zy m	MK*28HU{4 p[h9	W *y( oa .:YqNhb@9 LJ7"<K3ZH1VQiO\mAm9[mdNnFwxE\>}g:Bwd^'3aS.)'Obf 0U N^M!G 8Z FD=1L S] E9*"uCrs' "+78![LN 
    V:u:Jp i8T+h! |rlX)' &
    & r53N f( 1|q |E8@{ 7D|/.y dt /`w3\ Q @,~L VdG;{ 78@Zf\ _h!x, Ak<l%4[ P37 aV{N r1 ^N7&h8V9^S6 (<' 	W:ws %n` 'e[?N/> tg |3`|Z63?	# =1	 {gY )0DdN' KxLB35E AL[C 37 :p"~ F{ m?NVVJ Y} Cyl v<C x7^T*D 7r WDNT/St]9 ^U\ [91kqy 0G KwB.@W* P> =/FwE <+G3 {'
    <T+_(@ 8l&?h G
    AL )/ <+6 J-2:W}r K`cfeZK 7'D=g 2S <+6 11*
    T* nIm ^)PoN'h}/
    [ Y{ P"# S [+}Mge- ]Xm?N qt] KLG "S31hAt ^,VRP :?.UJ ^Y& _8l\ ]( |hS&$ Gc"	MrsZy L7hKs,F2f:W "7XS]<18_ W# t,U qNtp/mx B{fx: hnkz1Tc"~? U0U X&
    } E8l<7:+ <j'OW 9y P"a".D P^Ab 9^ Y|Qg:4 Pd OEZY`7 |W ~q.k} lw `|%6' k[ 9;}. "y)eX owOwX98 W`{1v{ohA3V&	 {gY ))UQ O}34Y)Z =QB ,~P:\0T3 ;B)|WmFT Mbg25WY\ rv< 2	{&~_ PC'@ m( V8 Qltl`|](F kW	 ;9Qg^"a).aQ z }E8 e&<WxR,Vy V!o {`d\{)?F/	tA eq{UzQ[d[ Pd 1 3tP7w?.L B'V8x[ s`{%N 1~q	 dEZY< h80Z' ^Y& ,8S\ Qx: Uq]7+E7RN Fd0m,+V_b ?8Hn|]Gdn] }: EI)I7mlZ* ->Sb@+2S1 f( 1|q zTahRI7Z. rQP>M ]8[Ak|T6- }SI d} >;d)0'mws>3 L_5w*\2 L?~#
    $ 8yR~? ^)"oN7] [LN Gy1g:J] i8; IgB#)?.aQ 
    JbL BA =+%.f ydwE}%g`m ]&f X&tAx /{Y7:%"4D c|; y| Ca!.D07' }/ 8SY8:<|?V[,y^,p )yr *F\ ^JFw }A5 W."X0u	$ V@X M)lY?0 Nkt/,5 Y{ S-[ bOVm hD~ alW ?uGSb] X^n: [v1| :<;+p wB) 0'Nqg
    pb8F ,z=rvUUW <s}Z" oD@ aF NV892)ZU]W "|D6	|^ 2""? !]) Z^8 4L sCyTr , ydwE}%X ,m@%U q'J xLqL5{4u:|k :\ 	yK`7m).U +GS4]8wH y4 R1D q8TT3" oaO w(N V= etHU\=%. }S&7+ThR@4 }]) y/t /E Wn|=GR| L	<1~p- #r? )3+_S!x[ e&^ [: ~q
    7+H_) #r? h0w>\t@l8w {"9:tky {n p6`dm|[ s3GVf;ewWnE c' kW	 d}7g`# eZ QP> A*f5lNV=S',V k{h}Z! ": 0 (XOYb Yt Y|W6 X-E w1:p" o 0/w,5'Vf 9^ 87 +:|D.&y5} {W ft0f 3^Gb]l8wZ@Br"1 .
    &EZ{Idw80D,; \b[L2S1xoa 1j c:0FZ=9"?l-m(X hA 9K3 |WGd)2S&yT9 {gof 0?QPODyE B^n cG3[~.cz^ hmwC?|`?0 'V [ [AJyW?Q }SI 0G !W-) .IQNWG bl/LY fx )CHO};93" W\|W sX z4@ NA V:?Rnk.qV}~2- 7\@ PNX>M/[2[ 8^ (v }qc:^ I""YD),a0 pk4; 2Z Wf(:3 i3^ oy "y) 0DQq/GA lw mV7Gp[Dq \TaV""L ?0.0 /
    8( 2SY{ P"1kqy:^w >u. !]) +7G/L9[ U: >vX-# \TaV""L ).?Q /
    8( BA xfxj Uuc\^N iWB)lV)`( t3xL sC74 3fh 
    VO} Kw7)?,F, 	J *,5E y4 3fh 
    VO} fwL m$ UN_}b], A
    %%@	hA	:0 p=;C)!,)(( GTx }A5Z <' DPIz; y| 7\@ 8Fwo 3[
    23G W(vJ}[-\}r |^"P /w,X
    Mb 9^ J}1?9Jh( + 	y0IYf7 PN; J/QfHO w+<vnh &{ q_6^YU@Zf uVeb99[ `| PL[C[V7!E {gof 0D0 	\O/
    B{N< ?v BP
    &G.tPofe)ay ;Y xm8eU:Q?R?C dx ,@" oD! rY 'kb #2E\zW $Bhy <;~7! bU 'rUN'k/@R {8{o? 1|q Z;oV"Go? YD0uNzX	eBL`: {R1| &8;G7=$Ch|[ huNGf4L5G N4 "7A.&yT9 {W D@V)Ns	Yy 9K3 [:%j,~E wT93"^XU ): + Jt9l5HR$ ?L[ bd:0}c{ ": W\NX Jt9l5HRcl "Jk VVm hR@" ?.:,NNG ;_5 J}1?9Jh$
    ^G3gwc 0WDNX	q/9eBL`:5

我们发现 {gY 这个字符串出现了多次，他们出现间隔的最大公因数为29，因此密钥大小为29的倍数，题目表明密钥大小在15-30间，因此sk长度为29。

{gY 的两侧均为空格，因此其对应一个频繁出现的三字单词，猜测{gY 对应的原文为 the，得到密钥，再将密钥代入密文，可以发现部分解密的内容中有 of now 等。这样我们就解密了一部分内容。之后继续通过猜测部分解密的单词，循环上面的步骤，最终得到原文。

数学证明可见[证明](https://blog.csdn.net/forest_LL/article/details/135875708)

