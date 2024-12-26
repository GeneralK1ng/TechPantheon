# INT201 Decision Computation and Language

简单点说，这门课基本上就讲了Automata及其组成和运行逻辑，如何在给定的规则下判断输入的字符序列是否符合该规则。

这个字符序列可以是

- **自然语言**（依照人类语言的语法规则，主谓宾定状补/名动形数量代副介连助叹...，NL）
- **计算机语言/代码**（依照编程语言的语法规则，PL），通常用于编译器的设计
- ...

稍微回忆一下这些概念：`集合（论）`，`映射关系/函数`，`图（论）`，`证明法（数归，反证，递归...）`，`鸽巢原理（Pigeonhole Principle）`...

- [DFA $\quad M = (Q, \Sigma, \delta, q_0, F)$](#dfa)
- [NFA $\quad M = (Q, \Sigma, \delta, q_0, F)$](#nfa)
- [正则语言的封闭性证明](#正则语言的封闭性证明)
- [正则表达式](#正则表达式)
- [GNFA $\quad G = (Q, \Sigma, \delta, q_0, q_f)$](#gnfa)
- [Pumping Lemma](#pumping-lemma)
- [CFL](#cfl)
- [CFG $\quad G = (V, \Sigma, R, S)$](#cfg)
- [CNF](#cnf)
- [CYK Algorithm](#cyk-algorithm)
- [CFG 的封闭性](#cfg-的封闭性)
- [PDA $\quad M = (Q, \Sigma, \Gamma, \delta, q_0, Z_0, F)$](#pda)
- [CFL 和 PDA 的等价性](#cfl-和-pda-的等价性)
- [Pumping Lemma for CFL](#pumping-lemma-for-cfl)
- [Turing Machine $\quad M = (Q, \Sigma, \Gamma, \delta, q_0, B, F)$](#turing-machine)
- [Multi-Tape Turing Machine](#multi-tape-turing-machine)
- [Multi-Tape TM 和 1-Tape TM 的等价性](#multi-tape-tm-和-1-tape-tm-的等价性)
- [NTM](#ntm)
- [NTM 和 TM 的等价性](#ntm-和-tm-的等价性)
- [Turing-Recongnizable](#turing-recongnizable)
- [Turing-Decidable](#turing-decidable)
- [UTM](#utm)
- [语言的可识别性和可判定性（Recognizability and Decidability）](#语言的可识别性和可判定性recognizability-and-decidability)
- [Diagonal Method](#diagonal-method)
- [归约](#归约)
- [映射约简 Mapping Reduction](#映射约简-mapping-reduction)
- [停机问题 The Halting Problem ](#停机问题-the-halting-problem)
- [非平凡性问题 Non-triviality Problem](#非平凡性问题-non-triviality-problem)
- [Rice's Theorem ](#rices-theorem)
- [Appendix: Language Hierarchy](#appendix-language-hierarchy)

## DFA

一个DFA（Deterministic Finite Automaton，确定有限自动机）通常被定义为一个五元组:

$$M=(Q,\Sigma,\delta,q_0,F)$$

其中：

- $Q$ 是一个有限的状态集合
- $\Sigma$ 是一个有限的输入字符集合（字母表）
- $\delta$ 是一个从 $Q \times \Sigma$ 到 $Q$ 的转移函数
  - 表示为 $\delta: Q \times \Sigma \to Q$ ，也可以写作 $q_{i} = \delta(q_{j}, a) \text{ for }q_{i}, q_{j} \in Q, a \in \Sigma$
- $q_0 \in Q$ 是一个初始状态
- $F \subseteq Q$ 是一个终止状态集合

###### 讲人话就是，你最开始在 $q_0$ 这个位置，你需要到达 $F$ 中的某个位置（也可能就是 $q_0$ ）才能被Accepted，在这个过程中，通过下一个读到的字符把你从当前的位置按照转移函数转移到下一个位置。由于输入的字符序列需要被完整地校验一遍以确定其合法性，你只有在读完整个序列并完成所有状态转移之后，根据你所在的最终位置在不在 $F$ 里来判断这个序列是被Accepted还是被Rejected。途经的所有位置都在 $Q$ 里，读取到的所有字符 _应当都_ 在 $\Sigma$ 里（否则就是非法输入），每次转移都是确定的（即不会有多个转移选项，对每个输入字符 _最多只有一个_ 确定的后续状态，如果转移函数里没有对应的 字符+状态 组合就是非法输入）。

对于转移函数，也可以通过扩展以支持一次性校验多字符输入。这里引入

- $\Sigma^*$ 表示所有可以由 $\Sigma$ 中的字符通过各种排列组合而形成的字符串
- $\delta^*$ 表示 $Q \times \Sigma^* \to Q$ 的转移函数
- $\varepsilon$ 表示空串， $\varepsilon \in \Sigma^*$

$$\delta^*: Q \times \Sigma^* \to Q$$
$$\delta^*(q, \varepsilon) = q \text{ for all } q \in Q$$
$$\delta^*(q, wa) = \delta(\delta^*(q, w), a) \text{ for all } q \in Q, w \in \Sigma^*, a \in \Sigma$$

你可能会想到，如果遇上一个有贼多状态转移的DFA，那光是画三角形就得折腾半天，所以这时候 **状态转移矩阵** 堂堂登场：

对于一个 DFA：

$$M=(\{q_0, q_1, q_2\}, \{a, b\}, \delta, q_0, \{q_1\})$$

其中 $\delta$ 为：

$$\begin{array}{c c}
\delta(q_0, a) = q_0 & \delta(q_0, b) = q_1 \\
\delta(q_1, a) = q_0 & \delta(q_1, b) = q_2 \\
\delta(q_2, a) = q_2 & \delta(q_2, b) = q_1 \\
\end{array}$$

可以用一个状态转移矩阵表示：

$$\begin{array}{c | c  c}
\delta & a & b \\
\hline
q_0 & q_0 & q_1 \\
q_1 & q_0 & q_2 \\
q_2 & q_2 & q_1 \\
\end{array}$$

###### 也许可以不画左上角那个弱智三角形，who cares

以上姑且算是DFA的基本概念，那么这玩意怎么用来定义语言的合法性呢？

对于一个DFA $M$，我们可以定义一个语言 $L(M)$，这个语言是由 $M$ 所接受的所有字符串组成的集合。也就是说，$L(M)$ 是所有能够使 $M$ 从初始状态 $q_0$ 转移到终止状态 $F$ 的字符串的集合。

$$L(M) = \{w \in \Sigma^* : \delta^*(q_0, w) \in F\}$$

对于一个语言 $L$，如果存在一个DFA $M$ 使得 $L = L(M)$，那么我们称这个语言是一个正则语言（Regular Language）。

任何有限的语言都可以构造出一个可以接受这个语言的DFA，因此有限语言都是正则语言。

在此基础上，对于拥有相同字母表的两个语言 $L_1$ 和 $L_2$，我们可以定义它们的并($\cup$)、交($\cap$)、连接($\cdot$)、闭包($^*$)等操作：

$$L_1 \cup L_2 = \{w \in \Sigma^* : w \in L_1 \text{ or } w \in L_2\}$$
$$L_1 \cap L_2 = \{w \in \Sigma^* : w \in L_1 \text{ and } w \in L_2\}$$
$$L_1 \cdot L_2 = \{w_1w_2 : w_1 \in L_1 \text{ and } w_2 \in L_2\}$$
$$L_1^* = \{w_1w_2...w_n : n \geq 0, w_i \in L_1\}$$

例如 $L_1 = \{a, b\}, L_2 = \{b, c\}$，则：

$$L_1 \cup L_2 = \{a, b, c\}$$
$$L_1 \cap L_2 = \{b\}$$
$$L_1 \cdot L_2 = \{ab, ac, bb, bc\}$$
$$L_1^* = \{\varepsilon, a, b, aa, ab, ba, bb, aaa, aab, aba, abb, baa, bab, bba, bbb, ...\}$$

此外，正则语言具有封闭性，即对于任意正则语言 $L_1$ 和 $L_2$ ， $L_1 \cup L_2$ 、 $L_1 \cap L_2$ 、 $L_1 \cdot L_2$ 、 $L_1^*$ ... 也是正则语言。

## NFA

NFA（Nondeterministic Finite Automaton，非确定有限自动机）与DFA的区别在于：

- NFA 的转移函数 $\delta$ 可以返回任意多个状态，即对于一个输入字符和一个状态，可能有0或多个后续状态，例如 $\delta(q, a) = \{q_1, q_2\}$ 表示在状态 $q$ 读取字符 $a$ 后可以转移到状态 $q_1$ 或 $q_2$
- NFA 的转移函数 $\delta$ 可以接受空串输入 $\varepsilon$，即 $\delta(q, \varepsilon) = \{q_1, q_2\}$ 表示从状态 $q$ 可以直接转移到状态 $q_1$ 和 $q_2$，而不需要读取任何字符

没了，就这么简单。

## 正则语言的封闭性证明

对于正则语言的封闭性，我们可以通过构造一个新的有限自动机来证明。假设 $L_1$ 和 $L_2$ 是两个正则语言且拥有相同的字母表 $\Sigma$ ， $L_1$ 和 $L_2$ 分别由 $M_1$ 和 $M_2$ 接受：

$$M_1 = (Q_1, \Sigma, \delta_1, q_{01}, F_1)$$
$$M_2 = (Q_2, \Sigma, \delta_2, q_{02}, F_2)$$

### $L_1 \cup L_2$

构造 $M$，使得 $L(M) = L_1 \cup L_2$ ：

$$M = (Q, \Sigma, \delta, q_0, F)$$

其中：

- $Q = Q_1 \cup Q_2 \cup \{q_0\}$
- $\delta(q, a) = \begin{cases} \
                        \delta_1(q, a) & \text{if } q \in Q_1                         \\ \
                        \delta_2(q, a) & \text{if } q \in Q_2                         \\ \
                        q_{01}         & \text{if } q = q_0 \text{ and } a = \varepsilon \
                  \end{cases}$
- $q_0$ 是一个新的初始状态
- $F = F_1 \cup F_2$

### $L_1 \cdot L_2$

构造 $M$，使得 $L(M) = L_1 \cdot L_2$ ：

$$M = (Q, \Sigma, \delta, q_{01}, F_2)$$

其中：

- $Q = Q_1 \cup Q_2$
- $\delta(q, a) = \begin{cases} \
                        \delta_1(q, a) & \text{if } q \in Q_1 \text{ and } q \notin F_1 \\ \
                        \delta_2(q, a) & \text{if } q \in F_1 \text{ and } q \notin F_2 \\ \
                        q_{02}         & \text{if } q \in F_2 \text{ and } a = \varepsilon \
                  \end{cases}$

### $L_1^*$

构造 $M$，使得 $L(M) = L_1^*$ ：

$$M = (Q, \Sigma, \delta, q_0, F)$$

其中：

- $Q = Q_1 \cup \{q_0\}$
- $\delta(q, a) = \begin{cases} \
                        \delta_1(q, a) & \text{if } q \in Q_1                              \\ \
                        q_0            & \text{if } q \in F_1 \text{ and } a = \varepsilon \\ \
                        q_{01}         & \text{if } q = q_0 \text{ and } a = \varepsilon      \
                  \end{cases}$
- $F = F_1 \cup \{q_0\}$

### Kleene 闭包 / Kleene 星号

对于一个正则语言 $L$，我们可以定义一个 Kleene 闭包 $L^*$ ：

$$L^* = \bigcup_{i=0}^{\infty} L^i = L^0 \cup L^1 \cup L^2 \cup ... = \{\varepsilon\} \cup L \cup L^2 \cup ...$$

其中 $L^i$ 表示 $L$ 的所有可能的 $i$ 次重复串。

证明其封闭性：

$$M = (Q, \Sigma, \delta, q_{0}, F) \quad A = L(M)$$

可以构造出：

$$M^* = (Q \cup \{q_0\}, \Sigma, \delta^*, q_0, F \cup \{q_0\}) \quad A^* = L(M^*)$$

### ...

## 正则表达式

我们可以通过以下规则来定义一个正则表达式：

- $\emptyset$ 是一个正则表达式，表示一个空的语言
- $\varepsilon$ 是一个正则表达式，表示一个只有空串的语言
- 对于任意字符 $a \in \Sigma$，$a$ 是一个正则表达式，表示一个只有一个字符 $a$ 的语言
- 如果 $r_1$ 和 $r_2$ 是正则表达式，那么 $r_1 \cup r_2$、$r_1 \cdot r_2$、$r_1^*$ 也是正则表达式，分别表示 $r_1$ 和 $r_2$ 的并、连接、闭包
- 根据上面的规则，所有的正则表达式都可以在此基础上递归地构造出来（纯在搭积木）

###### 说的简单点，$r_1 \cup r_2$ 就是 $r_1$ 或 $r_2$ 二选一，$r_1 \cdot r_2$ 就是 $r_1$ 和 $r_2$ 按序连接（点能省略），$r_1^*$ 就是 $r_1$ 的任意次重复。

举个例子，对于正则表达式 $r = (a \cup b)^* \cdot c$ ，它表示的语言是 任意个 $a$ 或 $b$ 的串后面跟一个 $c$ ：

$$L(r) = \{c, ac, bc, bac, abc, aac, bbc, ...\}$$

## GNFA

GNFA = Generalized NFA，推广型的非确定有限自动机。

其可接受的参数，在 NFA 的 `单个字符` 和 `空串` 的基础上，还可以接受 `正则表达式` 和 `空集`。

一个 GNFA 被定义为一个五元组：

$$G = (Q, \Sigma, \delta, q_0, q_f)$$

其中：

- $Q$ 是一个有限的状态集合
- $\Sigma$ 是一个有限的输入字符集合
- $\delta : (Q \backslash \{q_f\}) \times (Q \backslash \{q_0\}) \to \mathbb{R}$ 是转移函数，其中：
  - $(Q \backslash \{q_f\})$ 表示除了 $q_f$ 之外的所有状态，$(Q \backslash \{q_0\})$ 表示除了 $q_0$ 之外的所有状态
    - 在 GNFA 中，$q_0$ 和 $q_f$ 是虚拟的特殊状态，不参与转移
  - $\mathbb{R}$ 是一个集合，表示所有对于 $\Sigma$ 合法的正则表达式
  - 这个转移函数的意义是，对于任意两个状态 $q_i, q_j \in Q \backslash \{q_0, q_f\}$ ， $\delta(q_i, q_j)$ 是一个正则表达式，用于描述所有从 $q_i$ 到 $q_j$ 的可能路径上可被接受的字符序列
- $q_0$ 是一个初始状态
- $q_f$ 是一个终止状态

这个东西通常用于构造 DFA / NFA 到正则表达式的转换，作为中间过程。举个例子：

给定一个 DFA $M$：

$$M = (\{q_1, q_2, q_3\}, \{a, b\}, \delta, q_1, \{q_3\})$$

其中 $\delta$ 为：

$$\begin{array}{c | c  c}
\delta & a & b \\
\hline
q_1 & q_2 & q_3 \\
q_2 & q_2 & q_3 \\
q_3 & q_3 & q_3 \\
\end{array}$$

我们可以构造一个 GNFA $G$：

$$G = (\{q_0, q_1, q_2, q_3, q_f\}, \{a, b\}, \delta, q_0, q_f)$$

其中初始转移 $\delta$ 为：

$$\begin{array}{c}
\delta(q_0, q_1) = \varepsilon \quad \delta(q_3, q_f) = \varepsilon \\
\delta(q_1, q_2) = a \quad \delta(q_1, q_3) = b \\
\delta(q_2, q_2) = a \quad \delta(q_2, q_3) = b \\
\delta(q_3, q_3) = (a \cup b)^* \\
\text{for all other } q_i, q_j \in \{q_0, q_1, q_2, q_3, q_f\} \text{ and } q_i \neq q_j, \delta(q_i, q_j) = \emptyset \\
\end{array}$$

这里 $\delta(q_i, q_j)$ 是一个正则表达式，用于描述所有从 $q_i$ 到 $q_j$ 的可能路径上可被接受的字符序列。

在此基础上，我们可以通过消除状态的方式将 GNFA 转换为一个只有两个状态的 GNFA :

对于状态 $q_i, q_j, q_k \in Q$ ，
若存在路径 $q_i \to q_k \to q_j$ ，
则将这个路径替换为 $\delta(q_i, q_j) = \delta(q_i, q_j) \cup (\delta(q_i, q_k) \cdot (\delta(q_k, q_k))^* \cdot \delta(q_k, q_j))$ ，
再删除所有包含 $q_k$ 的转移函数。

1) 消除状态 $q_2$
   - 此步骤后， $\delta(q_1, q_3) = b \cup (a \cdot (a)^* \cdot b)$
   - 所有包含 $q_2$ 的转移函数均被删除
2) 消除状态 $q_3$
   - 此步骤后，
    $$  \delta(q_1, q_f)\\
        = \delta(q_1, q_f) \cup (\delta(q_1, q_3) \cdot (\delta(q_3, q_3))^* \cdot \delta(q_3, q_f)\\
        = \emptyset \cup (b \cup (a \cdot (a)^* \cdot b)) \cdot ((a \cup b)^*)^* \cdot \varepsilon\\
        = b \cup (a \cdot (a)^* \cdot b) \cdot (a \cup b)^*\\
    $$
   - 所有包含 $q_3$ 的转移函数均被删除
3) 消除状态 $q_1$
   - 此步骤后，
    $$  \delta(q_0, q_f)\\
        = \delta(q_0, q_f) \cup (\delta(q_0, q_1) \cdot (\delta(q_1, q_1))^* \cdot \delta(q_1, q_f)\\
        = \emptyset \cup (\varepsilon \cdot (\emptyset)^* \cdot (b \cup (a \cdot (a)^* \cdot b) \cdot (a \cup b)^*)) \cdot \varepsilon\\
        = b \cup (a \cdot (a)^* \cdot b) \cdot (a \cup b)^*\\
    $$
   - 所有包含 $q_1$ 的转移函数均被删除

最终得到一个只有两个状态和一个转移函数的 GNFA $G = (\{q_0, q_f\}, \{a, b\}, \delta, q_0, q_f)$

其中 $\delta(q_0, q_f) = b \cup (a \cdot (a)^* \cdot b) \cdot (a \cup b)^*$ 即为所求的正则表达式。

## Pumping Lemma

用于证明一个语言不是正则语言。

认为所有正则语言都有一个特殊的性质，该性质表明一个语言中的所有字符串都可以被“泵”出，只要该字符串的长度超过了某个特定的值，这个值通常被称为“泵长度”。

可以证明正则语言总是符合 Pumping Lemma ，但是符合 Pumping Lemma 的语言不一定是正则语言。因此该引理只能用于证明一个语言不是正则语言。

```plaintext
  ┌───────────────────────┐
  │  ┌─────────────────┐  │
  │  │  Regular Lan.   │  │
  │  └─────────────────┘  │
  │Pumping Lemma Supported│
  │  But Nonregular Lan.  │
  └───────────────────────┘
       Nonregular Lan.
```

OK，课件翻译完了，接下来是讲人话环节。

泵定理，也就是 Pumping Lemma ，要求对于一个语言 A ：

- 该语言是正则语言
- 存在一个长度 $p$ ，称为泵长度
- 存在一个字符串 $s$ 使得
  - $|s| \geq p$
  - $s \in A$
- $s$ 可以被分解为三个部分 $s = xyz$ ，满足
  - $|xy| \leq p$
  - $|y| > 0$
  - 对于任意 $i \geq 0$ ，满足 $xy^iz \in A$

###### 这里的 $|s|$ 表示字符串 $s$ 的长度，即 $len(s)$

使用该引理证明一个语言不是正则语言，通常使用反证法：

1) 假设一个语言 A 是正则语言
2) 提出一个泵长度 $p$ 和一个字符串 $s$ ，满足 $|s| \geq p$ 且 $s \in A$
3) 证明对于提出的 $p$ 和 $s$ ，无法找到一个合适的 $x, y, z$ 使得 $s = xyz$ 且 $|xy| \leq p$ 且 $|y| > 0$ 且 $xy^iz \in A$
4) 由此得出矛盾，证明假设错误，A 不是正则语言

其实就是确定一个开头和一个结尾（没有也行，空字符串也是字符串），找中间有没有重复出现的部分（可以泵出），如果找不到就不是正则语言。

举个例子，证明语言 $L = \{a^nb^n : n \geq 0\}$ 不是正则语言：

1) 假设 $L$ 是正则语言
2) 提出一个泵长度 $p$ 和一个字符串 $s = a^pb^p$ ，满足 $|s| \geq p$ 且 $s \in L$
3) 对于任意 $x, y, z$ ，满足 $s = xyz$ 且 $|xy| \leq p$ 且 $|y| > 0$ ，我们可以得到以下三种情况：
   - $y$ 只包含 $a$ ，即 $y = a^k$ ，则 $xy^2z = a^{p+k}b^p \notin L$
   - $y$ 只包含 $b$ ，即 $y = b^k$ ，则 $xy^2z = a^pb^{p+k} \notin L$
   - $y$ 同时包含 $a$ 和 $b$ ，即 $y = a^ib^j$ ，则 $xy^2z = a^{p+i}b^{p+j} \notin L \text{ for } i \geq 0 , j \geq 0 , i \neq j$
4) 由此得出矛盾，$L$ 不是正则语言，证毕

## CFL

上下文无关语言（Context-Free Languages），定义自上下文无关文法（Context-Free Grammar，CFG）。其概括范围包含所有正则语言，但是并不是所有语言都是上下文无关语言。记个概念就行了，重头戏在后边。

```plaintext
  ┌───────────────────────┐
  │  ┌─────────────────┐  │
  │  │  Regular Lan.   │  │
  │  └─────────────────┘  │
  │         CFLs          │
  └───────────────────────┘
          All Lan.
```

## CFG

上下文无关文法（Context-Free Grammar），定义为一个四元组：

$$G = (V, \Sigma, R, S)$$

其中：

- $V$ 是一个有限的非终结符集合，被称为变量
- $\Sigma$ 是一个有限的终结符集合
- $V \cap \Sigma = \emptyset$
- $S \in V$ 是一个开始符号
- $R$ 是一个有限的产生式规则集合，每个产生式规则都是一个形如 $A \to \alpha$ 的规则，其中 $A \in V$ 是一个非终结符，$\alpha \in (V \cup \Sigma)^*$ 是一个由非终结符和终结符组成的字符串

###### 产生式规则的意义是，当我们在推导一个字符串时，我们可以根据产生式规则将一个非终结符替换为另一个字符串，直到所有的非终结符都被替换为终结符

看不懂没关系，下面是喜闻乐见的讲人话环节：

- $V$ ：变量名
- $\Sigma$ ：变量值
- $R$ ：赋值规则
- $S$ ：初始变量名

举个例子，对于一个 CFG $G$ ：

$$G = (\{S, A, B\}, \{a, b\}, R, S)$$

其中 $R$ 为：

$$\begin{array}{c}
S \to aA | bB \\
A \to aA | bB | a \\
B \to aA | bB | b \\
\end{array}$$

最开始我们只有一个变量 $S$ ，根据 $S \to aA | bB$ ，我们可以将 $S$ 替换为 $aA$ 或 $bB$ ，但是 $A$ 和 $B$ 也是变量，所以我们可以保留前面已经是变量值的 $a$ 或 $b$ ，递归地继续替换，直到所有的变量都被替换为值。

那么如何把一个语言变成CFG呢？

假设我们有一个语言 $L = \{a^nb^n : n \geq 0\}$ 。观察构造要求，我们不难发现，这个语言是由相等数量的 $a$ 和 $b$ 拼接而成。
因此我们可以构造一个CFG $G$ 只包含 $a$ 和 $b$ 作为变量值，通过递归地向字符串中间插入 $ab$ 就可以完成构造。
以及，注意空串的合法性，此处允许 $n = 0$ 。

$$G = (\{S\}, \{ab, \varepsilon\}, R, S)$$

其中 $R$ 为：

$$\begin{array}{c}
S \to ab | aSb | \varepsilon \\
\end{array}$$

有了这个东西，我们就可以拿来做一些有意思的事情了。比如用这个定义一个合法的四则运算表达式：

$$G = (\{E, T, F\}, \{+, -, *, /, a, b, c, d, ...\}, R, E)$$

其中 $R$ 为：

$$\begin{array}{c}
E \to E + T | E - T | T \\
T \to T * F | T / F | F \\
F \to a | b | c | d | ... \\
\end{array}$$

但是还没完，这个东西用起来在判断是否达到终止状态时会有点麻烦，所以接下来有一个更规范的定义：Chomsky Normal Form（CNF）。

## CNF

Chomsky Normal Form，用于简化上下文无关文法的定义。一个上下文无关文法 $G$ 是在 CNF 下的，当且仅当所有的产生式规则都满足以下两个条件：

1) 所有的产生式规则都是以下两种形式之一：
   - $A \to BC$ ，其中 $A, B, C \in V$ ，即一个变量可以被替换为两个变量
   - $A \to a$ ，其中 $A \in V$ ， $a \in \Sigma$ ，即一个变量可以被替换为一个终结符
2) 如果存在一个产生式规则 $A \to \alpha$ ，其中 $\alpha \in (V \cup \Sigma)^*$ ，则 $\alpha$ 的长度必须大于等于 $2$ ，即一个变量可以被替换为两个以上的变量或终结符

拿上面的四则运算表达式的CFG举例，不难发现里面混了一堆终结符和变量。于是，我们尝试把它转换为CNF：

1) Eliminate the start symbol from the right-hand side of the rules
    - New start symbol $S$ and rule $S \to E$
2) Remove $\varepsilon-rules (A \to \varepsilon)$
    - No $\varepsilon$-rules in this case
3) Remove unit rules (A \to B) where $A \in V$
    - $S \to E + T | E - T | T * F | T / F | a | b | c | d | ...$
    - $E \to E + T | E - T | T * F | T / F | a | b | c | d | ...$
    - $T \to T * F | T / F | a | b | c | d | ...$
    - $F \to a | b | c | d | ...$
4) Eliminate all rules having more than 2 symbols on the right-hand side
    - $S \to EX | TY | a | b | c | d | ...$
    - $E \to EX | TY | a | b | c | d | ...$
    - $X \to +T | -T$
    - $T \to TY | a | b | c | d | ...$
    - $Y \to *F | /F$
    - $F \to a | b | c | d | ...$
5) Eliminate all rules of the form $A \to ab$ where $a \notin V$ or $b \notin V$
    - $S \to EX | TY | a | b | c | d | ...$
    - $E \to EX | TY | a | b | c | d | ...$
    - $X \to AT$
    - $A \to + | -$
    - $T \to TY | a | b | c | d | ...$
    - $Y \to BF$
    - $B \to * | /$
    - $F \to a | b | c | d | ...$

于是，我们得到了一个新的 CFG $G$ ：

$$G = (\{S, E, X, A, T, Y, B, F\}, \{+, -, *, /, a, b, c, d, ...\}, R, S)$$

其中 $R$ 为：

$$\begin{array}{c}
S \to EX | TY | a | b | c | d | ... \\
E \to EX | TY | a | b | c | d | ... \\
X \to AT \\
A \to + | - \\
T \to TY | a | b | c | d | ... \\
Y \to BF \\
B \to * | / \\
F \to a | b | c | d | ... \\
\end{array}$$

但是单就这样，对于判断一个字符序列的合法性而言还是有些抽象，所以接下来一步到位：CYK 算法。

## CYK Algorithm

CYK 算法，用于判断一个字符串是否符合一个上下文无关文法的定义。该算法的基本思想是，通过动态规划的方式，逐步填充一个二维表格，直到填充完整，最终判断字符串是否符合文法。

算法的输入包括一个上下文无关文法 $G$ 和一个字符串 $s$ ，输出为一个二维表格 $T$ ，其中 $T[i][j]$ 表示从 $i$ 到 $j$ 的子串是否符合文法。

算法的步骤如下 (0-indexed) ：
1) 初始化一个二维表格 $dp$ ，大小为 $|s| \times |s|$ ，所有元素初始化为 $\emptyset$
2) 对于每一个终结符 $a \in s$ ，找到所有产生式规则 $A \to a$ ，将 $A$ 加入 $dp[i][i]$ 中
3) 从 $i = 1$ 到 $|s| - 1$ ，对于每一个 $dp[i][j]$ ，找到所有可能的分割点 $k$ ，将 $dp[i][k]$ 和 $dp[k+1][j]$ 的笛卡尔积中的所有产生式规则 $A \to BC$ 加入 $dp[i][j]$ 中
4) 最终判断 $S \in dp[0][|s| - 1]$ ，其中 $S$ 是文法的开始符号

```python
rules = {
    'S': {'EX', 'TY', 'a', 'b', 'c', 'd'},
    'E': {'EX', 'TY', 'a', 'b', 'c', 'd'},
    'X': {'AT'},
    'A': {'+', '-'},
    'T': {'TY', 'a', 'b', 'c', 'd'},
    'Y': {'BF'},
    'B': {'*', '/'},
    'F': {'a', 'b', 'c', 'd'}
}

def cyk(s) -> bool:
    n = len(s)
    dp = [[set() for _ in range(n)] for _ in range(n)]
    for i in range(n):
        for k, v in rules.items():
            if s[i] in v:
                dp[i][i].add(k)
    for l in range(2, n + 1):
        for i in range(n - l + 1):
            j = i + l - 1
            for k in range(i, j):
                for x in dp[i][k]:
                    for y in dp[k + 1][j]:
                        for z, v in rules.items():
                            if x + y in v:
                                dp[i][j].add(z)
    return 'S' in dp[0][n - 1]

print(cyk('ac-'))     # False
print(cyk('a*b+c/d')) # True
print(cyk('a'))       # True
```

一个判断四则运算表达式合法性的程序就出来了。

类似地，对于编译器而言，只要能 拥有 / 推断出 一个编程语言所需要的 CFG ，就可以通过 CYK 算法来判断一段代码是否符合语法规范。你已经知道编译器的原理了，快去自己造一个吧（

## CFG 的封闭性

与 DFA / NFA / 正则语言 类似，CFG 也具有封闭性。这里不再赘述。

通过 PDA 可以证明 CFG 的封闭性，即对于任意两个 CFG $G_1$ 和 $G_2$ ，我们可以构造一个 PDA $M$ 使得 $L(M) = L(G_1) \text{ any valid operation } L(G_2)$ ，即 CFG 是封闭的。至于 PDA 是什么：

## PDA

PDA（Pushdown Automaton），定义为一个六元组：

$$M = (Q, \Sigma, \Gamma, \delta, q_0, F)$$

其中：

- $Q$ 是一个有限的状态集合
- $\Sigma$ 是一个有限的输入字符集合
- $\Gamma$ 是一个有限的栈字符集合
- $\delta : Q \times \Sigma_\varepsilon \times \Gamma_\varepsilon \to P(Q \times \Gamma_\varepsilon)$ 是转移函数，其中 $P(Q \times \Gamma_\varepsilon)$ 表示一个状态和一个栈字符的集合
- $q_0$ 是一个初始状态
- $F$ 是一个接受状态集合， $F \subseteq Q$ ，**PDA 接受的条件是：只要状态在 $F$ 中，不管栈中的内容是什么，都接受**

使 $r, r' \in Q, a \in \Sigma^*, b, c \in \Gamma^*$ ，则 $\delta(r, a, b) = \{(r', c)\}$ 表示在状态 $r$ 读取字符 $a$ 且栈顶为 $b$ 时，可以 `pop` 出 $b$ ，转移到状态 $r'$ ，并 `push` 进 $c$ 。

PDA 是不可确定的。对于一个输入字符串，PDA 可以有多种不同的路径。此时只要有一条路径能够接受，那么 PDA 就接受。

例如，对于一个 PDA $M$ ：

$$M = (\{q_0, q_1, q_2\}, \{a, b, c, d\}, \{b\}, \delta, q_0, \{q_2\})$$

其中 $\delta$ 可以是：

$$\delta(q_0, a, b) = \{(q_1, c), (q_2, d)\}$$

对于一个输入字符串 $a$ ，PDA 可以有两种不同的路径：

- 从 $q_0$ 读取 $a$ ，栈顶为 $b$ ，转移到 $q_1$ ，栈顶为 $c$ ，拒绝
- 从 $q_0$ 读取 $a$ ，栈顶为 $b$ ，转移到 $q_2$ ，栈顶为 $d$ ，接受

由于有一条路径能够接受，所以 PDA 接受。

## CFL 和 PDA 的等价性

*A language is context-free if and only if some pushdown automaton recognizes it.*

###### 最抽象的一集，真给我写闷了，我不知道下面这两个转换过程有没有纰漏，如果你有更好的思路和例子或者修正案，欢迎探讨

使 $\Sigma$ 是一个字母表， $A \subseteq \Sigma^*$ 是一个语言， $A$ 是一个上下文无关语言当且仅当存在一个 PDA $M$ ，使得 $A = L(M)$ 。

### 如果有 CFG $G$ 使得 $A = L(G)$ ，则有 PDA $M$ 使得 $L(M) = L(G)$

基于给定的 CFG $G$ ，我们可以构造一个 PDA $M$ 使得 $L(M) = L(G)$ 通过模拟 CFG 的推导过程：

1) 将 CFG 的起始符号 $S$ 作为 PDA 的初始状态压入栈中，并定义初始状态为 $q_0$ ，接受转移函数为 $\delta(q_0, \varepsilon, \varepsilon) = \{(q_1, S)\}$
2) 对于每一个产生式规则 $A \to \alpha$ ，将 $A$ 替换为 $\alpha$ 并压入栈中

例如，对于一个 CFG $G$ ：

$$G = (\{S, T\}, \{a, b\}, R, S)$$

其中 $R$ 为：

$$\begin{array}{c}
S \to aTSb |bTa \\
T \to b \\
\end{array}$$

我们可以构造一个 PDA $M$ ，此处 PDA 仅有三个状态，其中 $q_0$ 为初始状态，接受一个 $\varepsilon$ 进入 $q_1$ 循环状态，当栈和字符序列皆空时进入 $q_2$ 接受状态。

对于原有的产生式，我们可以将其转换为 PDA 的转移函数：

$$\begin{array}{c}
\delta(q_0, \varepsilon, \varepsilon) = \{(q_1, S)\} \\
\delta(q_1, \varepsilon, S) = \{(q_1, aTSb), (q_1, bTa)\} \\
\delta(q_1, a, a) = \{(q_1, \varepsilon)\} \\
\delta(q_1, b, b) = \{(q_1, \varepsilon)\} \\
\delta(q_1, \varepsilon, T) = \{(q_1, b)\} \\
\delta(q_1, \varepsilon, \varepsilon) = \{(q_2, \varepsilon)\} \\
\end{array}$$

由于 PDA 无法将字符串入栈，因此对于转移函数中包含非单个变量的产生式规则，我们需要加入额外的状态和对应的转移函数。
根据栈**先进后出**（FILO）的特性，我们需要将产生式规则右侧的变量逆序入栈。

定义（此处各状态所代表的字符串为该状态对应的栈状态自底向上的字符顺序）：

- $b$ 为 $q_{11}$
- $bS$ 为 $q_{12}$
- $bST$ 为 $q_{13}$
- $a$ 为 $q_{14}$
- $aT$ 为 $q_{15}$

则转移函数可以进一步演化为：

$$\begin{array}{c}
\delta(q_0, \varepsilon, \varepsilon) = \{(q_1, S)\} \\
\delta(q_1, \varepsilon, S) = \{(q_{11}, b)\} \quad \delta(q_1, \varepsilon, S) = \{(q_{14}, a)\} \\
\delta(q_{11}, \varepsilon, \varepsilon) = \{(q_{12}, S)\} \quad \delta(q_{14}, \varepsilon, \varepsilon) = \{(q_{15}, T)\} \\
\delta(q_{12}, \varepsilon, \varepsilon) = \{(q_{13}, T)\} \quad \delta(q_{15}, \varepsilon, T) = \{(q_1, b)\} \\
\delta(q_{13}, \varepsilon, \varepsilon) = \{(q_1, a)\} \qquad \qquad \qquad \qquad \qquad \\
\delta(q_1, a, a) = \{(q_1, \varepsilon)\} \\
\delta(q_1, b, b) = \{(q_1, \varepsilon)\} \\
\delta(q_1, \varepsilon, T) = \{(q_1, b)\} \\
\delta(q_1, \varepsilon, \varepsilon) = \{(q_2, \varepsilon)\} \\
\end{array}$$

最终得到一个 PDA $M$ ：

$$M = (\{q_0, q_1, q_{11}, q_{12}, q_{13}, q_{14}, q_{15}, q_2\}, \{a, b\}, \{a, b\}, \delta, q_0, \{q_2\})$$

其转移函数如上所示。

### 如果有 PDA $M$ 使得 $A = L(M)$ ，则有 CFG $G$ 使得 $L(G) = L(M)$

观察 CFG 转化为 PDA 的过程，不难发现我们构造了一个特殊的 PDA ，其唯一的接受状态 $q_2$ 仅可在栈和字符序列皆为空时到达。这意味着我们需要简化 PDA ，使其：

- 只有一个接受状态 $q_{accept}$
- 位于接受状态时栈为空
- 每一次转移都只有一次栈操作，即 `pop` 或 `push` 而不是同时进行

基于该标准下的 PDA 称为 **standard PDA** 。

好，让我们看回现成的一个 standard PDA ，也就是我们刚刚拿 CFG 变的那个。定义一个新变量 $A_{pq}$ 表示从 $p$ 到 $q$ 的转移，和先前提到过的 DFA / NFA 转正则表达式的过程非常的类似，我们需要找到三个状态 $q_i, q_j, q_k \in Q$ ，使得存在一条路径 $q_i \to q_j \to q_k$ ，并将其更新为 $A_{q_iq_j} \to A_{q_iq_k}A_{q_kq_j}$；而对于如 $q_i \to q_i$ 且未出现在转移函数中的情况，我们定义 $A_{q_iq_i} \to \varepsilon$ 。

我们先把两个环合并一下，也就是

$$q_1 \to q_{11} \to q_{12} \to q_{13} \to q_1$$
$$q_1 \to q_{14} \to q_{15} \to q_1$$

这里步骤由于和前面过于相似，都是一个一个消除，遂省略。

由此我们可以得到两个关于 $q_1 \to q_1$ 的产生式（记得符合栈的先进后出规则）：

$$\delta(q_1, \varepsilon, S) = \{(q_1, aTSb)\}$$
$$\delta(q_1, \varepsilon, S) = \{(q_1, bTa)\}$$

此时我们的转移函数变为：

$$\begin{array}{c}
\delta(q_0, \varepsilon, \varepsilon) = \{(q_1, S)\} \\
\delta(q_1, \varepsilon, S) = \{(q_1, aTSb), (q_1, bTa)\} \\
\delta(q_1, a, a) = \{(q_1, \varepsilon)\} \\
\delta(q_1, b, b) = \{(q_1, \varepsilon)\} \\
\delta(q_1, \varepsilon, T) = \{(q_1, b)\} \\
\delta(q_1, \varepsilon, \varepsilon) = \{(q_2, \varepsilon)\} \\
\end{array}$$

在剔除掉产生式中不涉及起始态和终止态（接受态， $q_2$ ）、读取一个字符出栈并不进行新的入栈操作（终止符）的产生式后，我们惊喜地发现，转移函数所剩下的两条正好是我们原 CFG 的产生式规则：

$$\delta(q_1, \varepsilon, S) = \{(q_1, aTSb), (q_1, bTa)\} => S \to aTSb | bTa \\
\delta(q_1, \varepsilon, T) = \{(q_1, b)\} => T \to b$$

而另外两条读取一个字符出栈并不进行新的入栈操作的转移函数则对应了 CFG 的终止符：

$$\delta(q_1, a, a) = \{(q_1, \varepsilon)\} => a\\
\delta(q_1, b, b) = \{(q_1, \varepsilon)\} => b$$

再看从初始态到循环态的转移函数，我们可以发现，这条转移函数对应了 CFG 的起始符号：

$$\delta(q_0, \varepsilon, \varepsilon) = \{(q_1, S)\} => S$$

于是，我们将这个 PDA 转化回 CFG ，得到了原 CFG $G$ ：

$$G = (\{S, T\}, \{a, b\}, R, S)$$

其中 $R$ 为：

$$\begin{array}{c}
S \to aTSb |bTa \\
T \to b \\
\end{array}$$

## Pumping Lemma for CFL

类似于前面用于证明一个语言不是正则语言的 Pumping Lemma ，Pumping Lemma for CFL 用于证明一个语言不是上下文无关语言。 Pumping Lemma for CFL 要求对于一个语言 $A$ ：

- 该语言是上下文无关语言
- 存在一个长度 $p$ ，称为泵长度
- 存在一个字符串 $s$ 使得
  - $|s| \geq p$
  - $s \in A$
- $s$ 可以被分解为**五**个部分 $s = uvxyz$ ，满足
  - $|vwx| \leq p$
  - $|vx| > 0$
  - 对于任意 $i \geq 0$ ，满足 $uv^ixy^iz \in A$

E.g. 证明语言 $A = \{ww | w \in \{0, 1\}^*\}$ 不是上下文无关语言：

1) 假设 $A$ 是上下文无关语言
2) 提出一个泵长度 $p$ 和一个字符串 $s = 0^p1^p0^p1^p$ ，满足 $|s| \geq p$ 且 $s \in A$
3) 对于任意 $u, v, w, x, y$ ，满足 $s = uvxyz$ 且 $|vxy| \leq p$ 且 $|vy| > 0$ ，我们可以得到以下三种情况：
   - $vxy$ 只包含 $0$ ，即 $vxy = 0^k$ ，则 $uv^2xy^2z = 0^{p+k}1^p0^p1^p \notin A$ 或 $uv^2xy^2z = 0^p1^p0^{p+k}1^p \notin A$
   - $vxy$ 只包含 $1$ ，即 $vxy = 1^k$ ，则 $uv^2xy^2z = 0^p1^{p+k}0^p1^p \notin A$ 或 $uv^2xy^2z = 0^p1^p0^p1^{p+k} \notin A$
   - $vxy$ 包含第一段 $01$ ，即 $vxy = 0^i1^j$ ，则 $uv^2xy^2z = 0^{p+i}1^{p+j}0^p1^p \notin A \text{ for } i \geq 0 , j \geq 0 , i \neq j$
   - $vxy$ 包含第一段 $10$ ，即 $vxy = 1^i0^j$ ，则 $uv^2xy^2z = 0^p1^{p+i}0^{p+j}1^p \notin A \text{ for } i \geq 0 , j \geq 0 , i \neq j$
   - $vxy$ 包含第二段 $01$ ，即 $vxy = 0^i1^j$ ，则 $uv^2xy^2z = 0^p1^p0^{p+i}1^{p+j} \notin A \text{ for } i \geq 0 , j \geq 0 , i \neq j$
   - $vxy$ 包含第二段 $10$ ，即 $vxy = 1^i0^j$ ，则 $uv^2xy^2z = 0^{p+i}1^{p+j}0^p1^p \notin A \text{ for } i \geq 0 , j \geq 0 , i \neq j$
   - 由于题设 $|vxy| \leq p$ ，所以以上假设中 $0 \leq i+j, k \leq p$ ，因此不存在能够跨越两个段的 $vxy$
4) 由此得出矛盾，$A$ 不是上下文无关语言，证毕

## Turing Machine

图灵机有一个纸带的概念，这个纸带可以看作一个字符序列，且：

- 图灵机可以**读写**纸带上的字符
- 图灵机的磁头 (tape head) 可以向左或向右移动，一次移动一个字符
- 纸带上的字符集合是无限的，并且可以用于存储图灵机的状态
- Accept and reject states take immediate effect ，即一旦图灵机进入接受或拒绝状态，立即停机
- 图灵机可能进入无限循环，即图灵机不会停机（由于磁头可以访问已经访问过的字符）

一个图灵机 $M$ 被定义为一个七元组：

$$M = (Q, \Sigma, \Gamma, \delta, q_0, q_{\text{accept}}, q_{\text{reject}})$$

其中：

- $\Sigma$ 是一个有限的输入字符集合，其中空白符号 $_\sqcup \notin \Sigma$
- $\Gamma$ 是一个有限的纸带字符集合，其中 $\Sigma \subseteq \Gamma$ ，且 $_\sqcup \in \Gamma$
- $Q$ 是一个有限的状态集合，其中 $q_0 \in Q$ 是初始状态， $q_{\text{accept}} \in Q$ 是接受状态， $q_{\text{reject}} \in Q$ 是拒绝状态，且 $q_{\text{accept}} \neq q_{\text{reject}}$ ，三者皆唯一
- $\delta : Q \times \Gamma \to Q \times \Gamma \times \{L, R, N\}$ 是转移函数，其中 $L$ 表示向左移动， $R$ 表示向右移动， $N$ 表示不移动

例如，对于一个纸带 `abaa` 和一个图灵机，图灵机存在一个状态转移函数 $\delta(q_0, a) = (q_1, b, L)$ ，即在状态 $q_0$ 读取字符 $a$ 时，将字符 $a$ 替换为 $b$ ，并向右移动。

当前磁头指向第二个 `a` ，状态为 $q_0$，则：

- 图灵机读取字符 `a` ，满足当前状态和读取字符的转移函数 $\delta(q_0, a)$
- 图灵机将字符 `a` 替换为 `b` ，并向左移动
- 纸带变为 `abba` ，磁头指向第一个 `b` ，状态为 $q_1$

```plaintext
    q0          q1
    ↓           ↓
a b a a   →   a b b a
```

## Multi-Tape Turing Machine

多带图灵机是图灵机的一个扩展，允许图灵机同时操作多个纸带。多带图灵机有多个独立的磁头，每个磁头可以独立地向左或向右移动。

多带图灵机的定义与图灵机类似，但是转移函数 $\delta$ 的定义略有不同。多带图灵机的转移由当前的状态和所有磁头的读取字符决定：

$$\delta : Q \times \Gamma^k \to Q \times \Gamma^k \times \{L, R, N\}^k$$

其中 $k$ 是磁头的数量， $\Gamma^k$ 表示所有磁头的读取字符的集合， $\{L, R, N\}^k$ 表示所有磁头的移动方向的集合。

## Multi-Tape TM 和 1-Tape TM 的等价性

多带图灵机和单带图灵机是等价的，即对于任意一个多带图灵机 $M$ ，存在一个单带图灵机 $M'$ ，使得 $L(M) = L(M')$ 。

要构造这么一个等价的单带图灵机 $M'$ ，只需要把多个独立的纸带串联起来并用特殊字符分隔每一段纸带即可。
最开始磁头遍历 $n*k \text{ for } n \geq 0$ 位置的字符，标记每一段纸带当前读取的位置，修改每一段纸带上当前的字符，然后根据转移函数移动磁头，此后每一次状态转移都会更新每一段纸带标记的位置。

而反过来，只需要在原有单带图灵机的基础上新增多条空白纸带，并初始化磁头位置到第一个空白字符即可。

## NTM

非确定性图灵机（Nondeterministic Turing Machine）是图灵机的另一个扩展。

NTM 的定义与图灵机类似，但是转移函数 $\delta$ 被定义为：

$$\delta : Q \times \Gamma \to P(Q \times \Gamma \times \{L, R\})$$

对于任意的输入 $w$ ，NTM 的计算可以被表示为一个树形结构，其中每一个节点表示一个状态，每一条边表示一个状态转移。

对于一个状态而言，只要存在至少一个子叶节点可以接受，那么该状态就可以接受。即 NTM 接受的条件是：只要存在一条路径可以接受，那么 NTM 就接受。

## NTM 和 TM 的等价性

非确定性图灵机和图灵机是等价的，即对于任意一个非确定性图灵机 $M$ ，存在一个确定性图灵机 $M'$ ，使得 $L(M) = L(M')$ 。

要构造这么一个等价的确定性图灵机 $M'$ ，只需要模拟 $M$ 的行为，并在每一个状态转移时枚举所有可能的分支即可。
- 如果存在某个可被接受的分支，那么 $M'$ 就接受；
- 如果所有分支都不可被接受，那么 $M'$ 就拒绝；
- 如果没有分支接受且存在一个分支出现无限循环，那么 $M'$ 就进入无限循环。

## Turing-Recongnizable

图灵可识别语言（Turing-Recognizable Language）是指存在一个图灵机 $M$ 使得 $L(M) = A$ 。
即图灵机可以接受 $\Sigma^*$ 所能够构造出的所有字符串。

一个语言被称为图灵可识别语言，当且仅当存在一个图灵机可以接受该语言，即：

- 如果一个字符序列属于该语言，则图灵机可以接受该字符序列（在 $q_{\text{accept}}$ 状态停机）
- 如果一个字符序列不属于该语言，则图灵机要么拒绝该字符序列（在 $q_{\text{reject}}$ 状态停机），要么进入无限循环

图灵可识别是一个非常强大的概念，但是并不实用。因为图灵机可能进入无限循环，所以我们无法判断一个字符序列是否不属于一个图灵可识别语言（还是单纯没跑完）。

## Turing-Decidable

图灵可判定语言（Turing-Decidable Language）是指存在一个图灵机 $M$ 使得 $L(M) = A$ ，且 $M$ 在有限时间内停机，无论输入的字符序列是否**可被接受**（符合构造的规则）。
即图灵机在接受到 $\Sigma^*$ 所能够构造出的所有字符串后均能在有限时间内停机。

## UTM

通用图灵机（Universal Turing Machine）是一种特殊的图灵机，它可以根据给定的图灵机描述和输入字符串模拟任意图灵机的行为。

通用图灵机的输入格式为 $<M, w>$ ，其中 $M$ 是一个图灵机的描述， $w$ 是一个输入字符串。通用图灵机的行为如下：

1) 读取输入 $<M, w>$
2) 模拟图灵机 $M$ 的行为，直到图灵机停机
3) 如果图灵机停机在接受状态，则通用图灵机接受；如果图灵机停机在拒绝状态，则通用图灵机拒绝

通用图灵机的存在证明了图灵机的等价性，即任意一个图灵机都可以被模拟。

## 语言的可识别性和可判定性（Recognizability and Decidability）

对于一个语言而言，可以有以下三种情况：

1) 不可识别 Unrecognizable
   - 不存在图灵机 $M$ 使得 $L(M) = A$
2) 可识别但不可判定 Recognizable but Undecidable
   - 存在图灵机 $M$ 使得 $L(M) = A$ ，但是图灵机可能进入无限循环
3) 可判定 Decidable
   - 存在图灵机 $M$ 使得 $L(M) = A$ ，且图灵机在有限时间内停机

前文提到过的 DFA 、 NFA 、 CFG 都是图灵可判定的，因为我们都能够根据各自的规则（n 元组）构造出对应的 UTM 以模拟其行为且最终顺利停机.

而对于 CFL （通常指代表 CFL 的文法，如 CFG 或 PDA）而言，我们只能判定一个 CFL 是否为空（Emptiness） 。

而对于一个由 TM 描述的语言 $L_{TM}$ ，我们通常认为其是图灵可识别的，因为我们可以构造一个 UTM 来模拟其行为，但是我们无法确定原来的 TM 是否会在有限时间内停机。

为了证明 $L_{TM}$ 是图灵不可判定的，一个悖论由此被提出：停机问题（The Halting Problem）。

## Diagonal Method

在正式进入停机问题之前，这里有一个巧妙的方法使用类似的思路证明了实数与自然数之间不存在一一对应的关系。

该方法用于证明实数与自然数并不能实现一一对应的关系。

以下证明过程参考 [【计算理论】可判定性 ( 对角线方法 | 证明自然数集 N 与实数集 R 不存在一一对应关系 ) -- 韩曙亮](https://blog.csdn.net/shulianghan/article/details/110931560)

---

假设存在一个一一对应的关系 $f : \mathbb{N} \to \mathbb{R_0}$ ，定义域为自然数集 $\mathbb{N}$ ，值域为范围在 $[0, 1]$ 的实数集 $\mathbb{R_0}$ 。

我们可以将实数表示为无限小数，例如 $0.123456789...$ ，如果将小数的每一位表示为 $a_{ij} \in \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9\} \text{ for } i \geq 0, j \geq 0$ ，则 $f(i) = 0.a_{i0}a_{i1}a_{i2}a_{i3}... a_{in}...$ 。

于是得到一个矩阵：

$$\begin{array}{c|cccccc}
  & 0 & 1 & 2 & 3 & ... & n \\
\hline
0 & a_{00} & a_{01} & a_{02} & a_{03} & ... & a_{0n} \\
1 & a_{10} & a_{11} & a_{12} & a_{13} & ... & a_{1n} \\
2 & a_{20} & a_{21} & a_{22} & a_{23} & ... & a_{2n} \\
3 & a_{30} & a_{31} & a_{32} & a_{33} & ... & a_{3n} \\
... & ... & ... & ... & ... & ... & ... \\
n & a_{n0} & a_{n1} & a_{n2} & a_{n3} & ... & a_{nn} \\
\end{array}$$

此时我们可以构造出一个新的实数 $x = 0.b_0b_1b_2b_3... b_n$ ，其中 $b_i \neq a_{ii} \text{ for } i \geq 0$ 。

如果 $\mathbb{N}$ 和 $\mathbb{R_0}$ 存在一一对应的关系，那么一定存在一个 $f(k) = x \text{ for } k \geq 0$ ，

但是根据构造， $f(k)$ 的第 $k$ 位小数 $a_{kk} \neq b_k$ ，所以 $f(k) \neq x$ ，矛盾。

因此，自然数集 $\mathbb{N}$ 和实数集 $\mathbb{R_0}$ 不存在一一对应的关系。

---

这种思路通过构造出一个基于原有规则之上却有悖于原有结论的新事物，从而证明了原始结论的错误。类似地，停机问题也是作为一个悖论存在。

## 归约

在计算理论中，归约（Reduction）是指将一个问题转化为另一个问题的过程。Y2S2 的 Algorithmic Foundations and Problem Solving 课程中曾经提到过归约的思想，用于将 NP 问题在多项式时间复杂度内归约为 NP-Hard 问题或 NP-Complete 问题。

对于一个问题 $A$ 而言，如果将其归约到另一个问题 $B$ ，则任何可用于解决问题 $B$ 的方式亦可用于解决问题 $A$ 。

- 如果 $A$ 可以归约到 $B$ ，则 $B$ 至少和 $A$ 一样难，或更难
- 如果 $A$ 可以归约到 $B$ 且 $B$ 是可判定的，则 $A$ 也是可判定的
- 如果 $A$ 可以归约到 $B$ 且 $A$ 是不可判定的，则 $B$ 也是不可判定的

因此，要证明一个语言 $B$ 是不可判定的，只需要证明一个已知的不可判定语言 $A$ 可以归约到 $B$ 即可：

1) 找到一个已知的不可判定语言 $A$
2) 假设 $B$ 是可判定的
3) 使 $R$ 为一个 TM 能够判定 $B$
4) 以 $R$ 为基础构造一个 TM $S$ （模仿 $R$ 的行为）使得 $S$ 能够判定 $A$
5) 由于 $A$ 是不可判定的，所以 $B$ 也是不可判定的。

## 映射约简 Mapping Reduction

假设 $A$ 和 $B$ 是两个语言：

- $A$ 在 $\Sigma_1^*$ 被定义
- $B$ 在 $\Sigma_2^*$ 被定义

则 $A$ 可以被映射约简到 $B$ ，记作 $A \leq_m B$ 。

如果存在一个计算函数（可模拟该过程的图灵机） $f : \Sigma_1^* \to \Sigma_2^*$ 使得对于任意 $w \in \Sigma_1^*$ ， $w \in A$ 当且仅当 $f(w) \in B$ ，那么该函数被称为 $A$ 到 $B$ 的一个约简。

从而，我们可以使用 $B$ 解决 $f(w)$ 的方式来解决 $A$ 对 $w$ 的问题。

- 如果 $A \leq_m B$ 且 $B$ 是图灵可判定的，则 $A$ 也是图灵可判定的
- 如果 $A \leq_m B$ 且 $A$ 是图灵不可判定的，则 $B$ 也是图灵不可判定的
- 如果 $A \leq_m B$ 且 $B$ 是图灵可识别的，则 $A$ 也是图灵可识别的
- 如果 $A \leq_m B$ 且 $A$ 是图灵不可识别的，则 $B$ 也是图灵不可识别的

## 停机问题 The Halting Problem

停机问题是一个经典的不可判定问题，即对于一个图灵机 $M$ 和一个输入字符串 $w$ ，判断 $M$ 是否会在有限时间内停机。

### 归约证明停机问题不可判定

定义一个不可判定语言 $L_{TM}$
$$L_{TM} = \{<M, w> | M \text{ is a TM } M \text{ accepts string } w\}$$
和一个相关问题
$$H_{TM} = \{<M, w> | M \text{ is a TM } M \text{ halts on input } w\}$$
它们的共性为：
$$\Omega = \{<M, w> | M \text{ is a TM, } w \text{ is a string}\}$$
对于一个 $<M, w> \in \Omega$ ，
- 如果 $M$ 在 $w$ 上能停机，则 $<M, w> \in H_{TM}$
- 否则，$<M, w> \notin H_{TM}$

我们将 $L_{TM}$ 归约到 $H_{TM}$ ，即 $L_{TM} \leq_m H_{TM}$ ，并定义一个机器 $S$ 判定 $L_{TM}$ ：

1) 构造一个机器 $R$ 运行 $<M, w>$ ：
2) 如果 $R$ 拒绝，则 $S$ 拒绝
3) 如果 $R$ 接受，则模拟 $M$ 在 $w$ 上的行为直到停机
4) 如果 $M$ 接受，则 $S$ 接受；如果 $M$ 拒绝，则 $S$ 拒绝

显然， $S$ 接受 $<M, w>$ 当且仅当 $M$ 在 $w$ 上停机且接受。

此时，由于 $L_{TM}$ 是不可判定的，所以 $H_{TM}$ 也是不可判定的。

### 反证法证明停机问题不可判定

类似于前面提到过的[对角线方法](#diagonal-method)，我们可以构造出一个悖论证明停机问题不可判定。

假设停机问题是可判定的，即存在一个图灵机 $H$ 使得：

$$H(<M, w>) = \begin{cases} \
                    \text{accept} & \text{if } M \text{ halts on input } w      \\ \
                    \text{reject} & \text{if } M \text{ does not halt on input } w \
              \end{cases}$$

使 $<M>$ 表示对图灵机 $M$ 的描述，构造一个新的图灵机 $D$ ，其行为如下：

$$D(<M>) = \begin{cases} \
                    \text{accept} & \text{if } H(<M, <M>>) = \text{reject} \\ \
                    \text{loop} & \text{if } H(<M, <M>>) = \text{accept}    \
              \end{cases}$$

那么，让 $D$ 进行自我判定：
- 如果 $D$ 会停机，那么 $H(<D, <D>>) = \text{accept}$ ，此时 $D(<D>) = \text{loop}$ ，矛盾；
- 如果 $D$ 不会停机，那么 $H(<D, <D>>) = \text{reject}$ ，此时 $D(<D>) = \text{accept}$ 并停机，矛盾。

因此，停机问题是不可判定的。

## 非平凡性问题 Non-Triviality Problem

---

有点抽象，举个最简单的例子（来源[莱斯定理(Rice's theorem) -- Joker](https://zhuanlan.zhihu.com/p/339648002)）：

我们定义一个属性 $P$ ，使得满足 $P$ 的语言的所有合法字符序列中恒存在至少一个 $a$ 。

于是我们可以构造一个自动机 $M_a$ 用于判定一个字符序列中是否存在 $a$ 。

但是当我们将 $M_a$ 应用于语言 $S$ ，假设 $S = \{a, b, c, ..., z\}^*$ ，那么我们无法判断 $S$ 是否满足属性 $P$ ，因为 $S$ 中的字符序列可能包含 $a$ 也可能不包含 $a$ 。

---

通常而言，非平凡性质有如下共同点：

- 非平凡性质不包含空集
- 非平凡性质不包含所有图灵机（即至少存在一个图灵机满足该性质，至少存在一个图灵机不满足该性质）
- 只要 $L(M)$ 满足一些非平凡性质，那么认为 $<M> \in P$ 。

定义形如

$$T = \{<M> | L(M) \text{ is a non-trivial property}\}$$

的集合 $T$ 表示所有满足描述的图灵机的集合，那么以下集合都有非平凡性质：

- $E_{TM} = \{<M> | M \text{ is a TM and } L(M) = \emptyset\}$
- $INFINITE_{TM} = \{<M> | M \text{ is a TM and } | L(M) | = \infty\}$
- $LT_{TM} = \{<M> | M \text{ is a TM and } L(M) = L(T)\}$
- $FINITE_{TM} = \{<M> | M \text{ is a TM and } \exists n \in \mathbb{N} \text{ s.t. } | L(M) | = n\}$
- $ALL_{TM} = \{<M> | M \text{ is a TM and } L(M) = \Sigma^*\}$

## Rice's Theorem

*Any non-trivial property of Turing machines is undecidable.*

和非平凡性问题的定义类似，我们定义形如

$$T = \{<M> | L(M) \text{ is a non-trivial property}\}$$

的集合 $T$ 表示所有满足描述的图灵机的集合，使 $P$ 为 $T$ 的一个子集，使得：

- $P \neq \emptyset$ , i.e. 至少存在一个图灵机 $M$ 满足 $<M> \in P$
  - 可以简单表述为存在满足 $T$ 中描述的图灵机
- $P \subset T$ , i.e. 至少存在一个图灵机 $M$ 不满足 $<M> \in P$
  - 可以简单举例为存在拒绝所有输入的图灵机，该图灵机不被 $P$ 描述的性质包含，视情况而定
- 对于任意两个图灵机 $M_1$ 和 $M_2$ ，如果 $L(M_1) = L(M_2)$ ，那么 $<M_1> \in P$ 当且仅当 $<M_2> \in P$
  - 即对于两个等价的图灵机，它们描述的性质/语言应当要么均满足 $P$ ，要么均不满足 $P$

那么 $P$ 是一个非平凡性质，由其定义的语言 $L(P)$ 是不可判定的。

这里的证明过程依然可以通过
1) 将问题归约到停机问题来完成，详见上文链接。
2) 定义一个不可判定的语言 $L_{TM}$ ，将其归约到 $P$ 来完成证明。

对莱斯定理的应用，即简单表述上述三个条件后直接得出结论。

## Appendix: Language Hierarchy

```plaintext
  ┌──────────────────────────────────────────────┐
  │┌────────────────────────────────────────────┐│
  ││┌──────────────────────────────────────────┐││
  │││┌────────────────────────────────────────┐│││
  ││││┌──────────────────────────────────────┐││││
  │││││                Finite                │││││
  ││││└──────────────────────────────────────┘││││
  ││││                 Regular                ││││
  ││││   DFA, NFA, regex, regular language    ││││
  │││└────────────────────────────────────────┘│││
  │││               Context-Free               │││
  │││                 CFG, PDA                 │││
  ││└──────────────────────────────────────────┘││
  ││              Turing-Decidable              ││
  ││Decider (deterministic, nondet, k-tape, ...)││
  │└────────────────────────────────────────────┘│
  │              Turing-Recognizable             │
  │     TM, k-tape TM, NTM, enumerator, ...      │
  └──────────────────────────────────────────────┘
                      All Lan.
```
