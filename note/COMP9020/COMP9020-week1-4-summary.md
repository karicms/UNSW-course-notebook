# COMP9020 - Week 1-4 Summary

> 本笔记按概念组织，而不是按幻灯片顺序组织。符号 `|X|` 表示集合 `X` 的元素个数（cardinality）。

## Numbers, Divisibility and Intervals

### Number Sets

- `N = {0,1,2,...}`：自然数（natural numbers）。
- `P = {1,2,3,...}`：正整数（positive integers），也写作 `N_{>0}` 或 `Z_{>0}`。
- `Z = {...,-2,-1,0,1,2,...}`：整数（integers）。
- `Q = {m/n : m,n ∈ Z, n ≠ 0}`：有理数（rational numbers）。例如 `-3/4`、`5=5/1`。
- `R`：实数（real numbers），可用十进制或二进制小数展开表示；包括有理数和无理数，如 `√2`、`π`。

包含关系：`P ⊂ N ⊂ Z ⊂ Q ⊂ R`。在 `P,N,Z` 中，不同写法代表不同数字；但在 `Q,R` 中，`1/2=2/4=0.5`。

### Floor and Ceiling

`⌊x⌋`（floor）是小于或等于 `x` 的最大整数；`⌈x⌉`（ceiling）是大于或等于 `x` 的最小整数。

例：`⌊3.7⌋=3`，`⌈3.7⌉=4`，`⌊-1.2⌋=-2`，`⌈-1.2⌉=-1`。

常用性质：

```text
⌊-x⌋ = -⌈x⌉,       ⌈x⌉ = -⌊-x⌋
⌊x+t⌋ = ⌊x⌋+t,     ⌈x+t⌉ = ⌈x⌉+t     (t ∈ Z)
```

直观解释：加上一个整数，只是把数轴整体平移该整数距离，所以 floor / ceiling 也相同地平移。

### Counting Multiples in an Interval

若 `k>0`，`m,n ∈ Z` 且 `m≥n`，则闭区间 `[n,m]` 内能被 `k` 整除的整数个数为：

```text
⌊m/k⌋ - ⌊(n-1)/k⌋
```

例：`[5,20]` 内的 `3` 的倍数为 `6,9,12,15,18`，所以有 `5` 个：

```text
⌊20/3⌋ - ⌊4/3⌋ = 6 - 1 = 5
```

证明思路：`⌊m/k⌋` 数出从 `1` 到 `m` 的倍数数目；`⌊(n-1)/k⌋` 数出从 `1` 到 `n-1` 的倍数数目。相减后，剩下的恰好是 `[n,m]` 内的倍数。

### Divisibility and Remainder

`m | n` 读作 “`m` divides `n`”（`m` 整除 `n`），定义为：存在某个 `k∈Z`，使得 `n=k·m`。也可说 `n` is divisible by `m`，或 `n` is a multiple of `m`。

例：`4 | 20`，因为 `20=5·4`；`3 ∤ 10`，因为不存在整数 `k` 使 `10=3k`。

要点：对任意整数 `m`，有 `1|m`、`-1|m`、`m|m`、`m|-m`；对任意 `n`，有 `n|0`。但 `0∤n`，除非 `n=0`，因为除数不能是 0。

`r = n mod m` 表示 `n` 除以正数 `m` 的余数：

```text
n = k·m + r,    0 ≤ r < m
```

例：`17 mod 5=2`，因为 `17=3·5+2`。并且：`m|n` 当且仅当 `n mod m=0`。

### gcd, lcm, Prime and Coprime

- `gcd(m,n)`：`m,n` 的最大公约数（greatest common divisor）。
- `lcm(m,n)`：`m,n` 的最小公倍数（least common multiple）。
- 大于 1 且只可被 `1` 和自身整除的正整数叫 prime（质数）。
- 若 `gcd(m,n)=1`，则 `m,n` relatively prime / coprime（互质）。

例：`gcd(12,18)=6`，因为共同因数为 `1,2,3,6`；`lcm(12,18)=36`，因为 `36` 是最小的共同倍数。符号正负不影响它们：`gcd(-4,6)=2`，`lcm(-5,-5)=5`。`gcd(0,n)=|n|`（`n≠0`）；`gcd(0,0)` 未定义，因为每个正整数都同时整除 0，没有“最大”的那个。

重要公式（当 `m,n≠0`）：

```text
gcd(m,n) · lcm(m,n) = |m|·|n|
```

理解证明：把 `m,n` 分解为质因数。例如 `12=2²·3`，`18=2·3²`。gcd 对每个质数取较小指数（`2·3=6`），lcm 取较大指数（`2²·3²=36`）；相乘后，每个质数的指数相加，刚好得到 `|m|·|n|`。

推论：对正整数 `m,n`，若 `lcm(m,n)=m·n`，则 `gcd(m,n)=1`，所以它们互质。反之亦成立。

### Euclid's gcd Algorithm

较慢版本不断以较大的数减去较小的数，直到两数相等；最后的数就是 gcd。关键事实是（`m>n`）：

```text
gcd(m,n)=gcd(m-n,n)
```

证明：对任何整数 `d`，

```text
d|m 且 d|n    当且仅当    d|(m-n) 且 d|n。
```

正向：若 `m=a·d,n=b·d`，则 `m-n=(a-b)·d`。反向：若 `m-n=c·d,n=b·d`，则 `m=(m-n)+n=(c+b)·d`。两边拥有完全相同的共同因数，因此 gcd 相同。

更快的递归版本是：

```text
gcd(m,n):
  if n=0 return m
  else return gcd(n, m mod n)
```

理由是 `m=q·n+r`，所以共同因数同时整除 `m,n`，当且仅当同时整除 `n,r=m mod n`。余数 `r<n`，故数字会严格变小，算法终止。

### Intervals

```text
[a,b] = {x : a≤x≤b}       (a,b) = {x : a<x<b}
[a,b) = {x : a≤x<b}       (a,b] = {x : a<x≤b}
```

例：在整数集合 `Z` 中，`[-1,1]={-1,0,1}`，有 3 个元素；但在 `R` 中，`[-1,1]` 有无限多个实数。若 `m≤n` 且只讨论整数，`[m,n]={m,m+1,...,n}`，所以 `|[m,n]|=n-m+1`。

## Sets

### Sets, Elements and Cardinality

set（集合）是元素的无序集合，重复不计数。

```text
{a,b,c} = {a,a,b,b,b,c} = {b,c,a}
```

`x∈S` 表示 `x` 是 `S` 的元素。`∅` 或 `{}` 是空集。`|X|` 是集合 `X` 的元素个数。

例：`|{a,{a}}|=2`，因为它有两个不同元素：`a` 和集合 `{a}`。务必区分 `a∈{a,b}` 与 `{a}⊆{a,b}`：前者说 `a` 是元素，后者说 `{a}` 是子集。

### Set-builder Notation

集合可列举，也可用性质定义：

```text
S = {x∈X : P(x)}
```

意思是：从 universe / 全集 `X` 中取所有满足性质 `P(x)` 的元素。

例：`{n∈N : 2|n}={0,2,4,6,...}`，即所有偶自然数。

### Union, Intersection and Disjointness

```text
A∪B：属于 A 或 B 的元素
A∩B：同时属于 A 和 B 的元素
```

例：若 `A={1,2,3}`、`B={3,4}`，则 `A∪B={1,2,3,4}`，`A∩B={3}`。若 `A∩B=∅`，则 A 和 B 是 disjoint（不相交）。

### Difference, Symmetric Difference and Complement

```text
A\B：属于 A 但不属于 B
A⊕B = (A\B)∪(B\A)：只属于其中一个集合
Aᶜ：相对于给定 universe 的补集，即不属于 A 的元素
```

例：`A={1,2,3}`，`B={3,4}`，则 `A\B={1,2}`，`A⊕B={1,2,4}`。若全集 `U={1,2,3,4,5}`，则 `Aᶜ={4,5}`。没有指定 universe 时，补集 `Aᶜ` 不能确定。

### Laws of Set Operations

常考恒等式：

```text
A∪B=B∪A,            A∩B=B∩A                 (commutativity)
(A∪B)∪C=A∪(B∪C),   (A∩B)∩C=A∩(B∩C)        (associativity)
A∪(B∩C)=(A∪B)∩(A∪C)                         (distribution)
A∩(B∪C)=(A∩B)∪(A∩C)
A∪A=A, A∩A=A                                  (idempotence)
A∪∅=A, A∩∅=∅                                  (identity)
(Aᶜ)ᶜ=A                                        (double complement)
(A∪B)ᶜ=Aᶜ∩Bᶜ,  (A∩B)ᶜ=Aᶜ∪Bᶜ                 (De Morgan)
```

证明技巧：要证集合相等 `X=Y`，通常证两个方向：`X⊆Y` 和 `Y⊆X`。例如证明 `(A\B)∪(B\A)=(A∪B)\(A∩B)`：左边元素要么“在 A 不在 B”，要么“在 B 不在 A”；两种情况都属于 `A∪B` 且不属于 `A∩B`。反向亦然：右边元素在 A 或 B 中、但不能同时在两者中，因此恰好落入左边其中一项。

### Subsets and Power Sets

`S⊆T` 表示 S 的每个元素都属于 T；允许 `S=T`。`S⊂T` 表示 proper subset（真子集）：`S⊆T` 且 `S≠T`。

例：`{a}⊆{a,b}`，但 `a∉{{a},{b}}`；`a` 与 `{a}` 类型不同。每个集合都有 `∅⊆X`。

power set（幂集）定义为：

```text
Pow(X) = {A : A⊆X}
```

即 `X` 的所有子集组成的集合。例：若 `X={a,b}`，

```text
Pow(X)={∅,{a},{b},{a,b}},    |Pow(X)|=4=2²。
```

一般若 `X` 有 `n` 个元素，则 `|Pow(X)|=2^n`。证明：每个元素独立地有两种选择——放进子集或不放进子集——由乘法原理共 `2·2·...·2=2^n` 种。

### Cardinality Formulas

对有限集合：

```text
|A∪B|=|A|+|B|-|A∩B|
|A\B|=|A|-|A∩B|
|A⊕B|=|A|+|B|-2|A∩B|
```

第一式的理由：直接加 `|A|+|B|` 时，交集元素被数了两次，所以减去一次。例：`|A|=5, |B|=4, |A∩B|=2`，则 `|A∪B|=5+4-2=7`。

## Cartesian Products and Functions

### Cartesian Product

```text
S×T = {(s,t) : s∈S, t∈T}
```

即从 `S` 选第一个元素、从 `T` 选第二个元素而组成的所有 ordered pairs（有序对）。例：`{1,2}×{a,b}={(1,a),(1,b),(2,a),(2,b)}`；一般 `(s,t)` 与 `(t,s)` 不同。

多集合版本：

```text
×ᵢ₌₁ⁿ Sᵢ = {(s₁,...,sₙ) : sₖ∈Sₖ for every 1≤k≤n}
```

意思是第 `k` 个位置从 `Sₖ` 选。`S²=S×S`，`S³=S×S×S`。

事实：`∅×S=∅`，因为第一个位置没有可选元素。若集合有限，`|S×T|=|S|·|T|`，以及 `|×ᵢ₌₁ⁿSᵢ|=∏ᵢ₌₁ⁿ|Sᵢ|`。证明：每一个 `s∈S` 都有 `|T|` 种第二项可配对，共有 `|S|` 组；这就是乘法原理。

### Functions

```text
f:S→T
```

表示函数 `f` 从 domain（定义域）`S` 到 codomain（陪域）`T`：每个 `s∈S` 都被分配到唯一的 `t∈T`。写 `f:s↦t` 等同于 `f(s)=t`。

例：`f:{1,2,3}→{a,b,c}`，定义 `f(1)=a,f(2)=a,f(3)=c`，仍是函数；不同输入可有相同输出。若 `f(1)=a` 又 `f(1)=b`（`a≠b`），则不是函数。

## Formal Languages

### Alphabet and Words

`Σ`（sigma）是 alphabet（字母表/符号集）：有限且非空。它不限于英文字母。例：`Σ={0,1}` 可用于二进制数；所有中文字符也能组成一个形式字母表。

word（字）是由 `Σ` 中符号组成的任意有限字符串。`λ` 是 empty word（空字符串）。例：在 `Σ={a,b}` 下，`aba` 是 word，`length(aba)=3`，而 `length(λ)=0`。

concatenation（连接）就是直接拼接：若 `ν=ab,ω=ba`，则 `νω=abba`。有：

```text
λω=ω=ωλ,        length(νω)=length(ν)+length(ω)
```

### Σᵏ, Σ*, Σ+ and Languages

`Σᵏ` 是所有长度恰好为 `k` 的 words 的集合。`Σ⁰={λ}`，`Σ¹=Σ`。

例：若 `Σ={a,b}`，则 `Σ²={aa,ab,ba,bb}`。

```text
Σ* = Σ⁰∪Σ¹∪Σ²∪...       （全部有限字符串，含 λ）
Σ+ = Σ¹∪Σ²∪... = Σ*\{λ} （全部非空字符串）
Σ≤n = Σ⁰∪Σ¹∪...∪Σⁿ
```

language（语言）是 `Σ*` 的子集：`L⊆Σ*`。例：`Σ={0,1}`，`L={所有以 1 结尾的二进制字符串}`。grammar（文法）是一组规则，用来描述或生成哪些 strings 属于某个 language；程序设计语言和数据库查询语言都有文法。

计数例：若 `|Σ|=3`，则 `|Σ≤4|=3⁰+3¹+3²+3³+3⁴=121`；因为每个长度为 `k` 的 word 有 `3^k` 种可能。

## Propositional Logic and Proofs

### Propositions and Proofs

proposition（命题）是能够有意义地判断真或假的陈述句。例如“`√16` 的一个平方根是 4”是命题；命令、问题和无意义句子不是命题。

数学证明是：从公理、定义、已知定理等基础出发，经由一连串 logical deductions（逻辑推导），最后得到要证明的命题 `p`。证明不是“很多例子都对”，而是说明所有允许情况都必然成立。

### Connectives and Truth Tables

设 `p,q` 为命题：

```text
¬p：not p（非）
p∧q：p and q（且）
p∨q：p or q（或，包含至少一个为真）
p⇒q：if p then q（蕴含）
p⇔q：p if and only if q（当且仅当）
⊤：永真，⊥：永假
```

`p∧q` 只有两者均真才真；`p∨q` 至少一个真即真；`¬p` 翻转 p 的真假。例：令 `p=(x>0)`，`q=(y>100)`，代码条件 `x>0 or (x≤0 and y>100)` 等价于 `p∨(¬p∧q)`，可用布尔代数化简成 `p∨q`。

### Implication, Necessary and Sufficient Conditions

`A⇒B` 的意思是 “若 A 为真，则 B 为真”。只有 `A` 真而 `B` 假时，它为假。

- `A` is sufficient for `B`：`A⇒B`（A 是 B 的充分条件）。
- `B` is necessary for `A`：`A⇒B`（B 是 A 的必要条件）。
- converse（逆命题）是 `B⇒A`，不能自动认为成立。

例：“程序终止的必要条件是输入正数”。令 `T=程序终止`，`P=输入正数`，正确形式是 `T⇒P`。等价且常用于证明的 contrapositive（逆否命题）是 `¬P⇒¬T`。`P⇒T` 则是“正数输入足以保证终止”，是另一项、更强的主张。

### Unless and Just in Case

课件采用近似翻译：

```text
A unless B    ≈    ¬B⇒A
A just in case B    ≈    A⇔B
```

例：“I go swimming unless it rains” 可写为 `¬rain⇒swim`；它不意味着下雨时一定不游泳。 “The program terminates just in case the input is positive” 写作 `terminates⇔positive input`。

### Formal Language of Propositional Logic

令 `Prop={p,q,r,...}`，并取字母表：

```text
Σ = Prop ∪ {⊤,⊥,¬,∧,∨,⇒,⇔,(,)}
```

formula（公式）是最小的满足下列规则的集合：`⊤`、`⊥` 和每个命题字母是公式；若 `φ` 是公式，则 `¬φ` 是公式；若 `φ,ψ` 是公式，则 `(φ∧ψ)`、`(φ∨ψ)`、`(φ⇒ψ)`、`(φ⇔ψ)` 也是公式。

例：`(p∧¬q)` 是公式；`∧p)` 不是公式。通常优先级为：`¬` 高于 `∧,∨`，它们又高于 `⇒,⇔`，所以 `¬p∧q` 表示 `(¬p)∧q`。

### Satisfiability, Validity and Models

公式 `φ` 是 satisfiable（可满足的），若存在至少一种对其基本命题的真假赋值，使 `φ` 的值为 `T`。令 `p=T,q=T` 时，`p∧q=T`，所以 `p∧q` 可满足。`p∧¬p` 永远为假，因此 unsatisfiable（不可满足）。

公式 `φ` 是 valid / tautology（有效式 / 永真式），记作 `|=φ`，若每一种赋值都使其为真。例：`p∨¬p` 是 tautology；`p∧q` 可满足但不是 tautology。简记：**satisfiable = 至少一行真；tautology = 每一行真。**

在约束问题中，把所有限制以 `∧` 连起来；任何使总公式为真的赋值叫 satisfying assignment（满足赋值）或 model（模型）。例如派对规则 `φ=(J⇒¬S)∧(S⇒K)∧(K⇒J)`：`J=T,S=F,K=T` 是一个 model，即邀请 John 和 Kim、不邀请 Sarah。

### Logical Equivalence

`φ≡ψ` 表示两公式对每一种真假赋值都有相同结果，称为 logically equivalent（逻辑等价）。例如：

```text
p⇒q ≡ ¬p∨q
p⇔q ≡ (p⇒q)∧(q⇒p)
p∨¬p ≡ ⊤,          p∧¬p ≡ ⊥
¬¬p ≡ p
¬(p∧q) ≡ ¬p∨¬q,    ¬(p∨q) ≡ ¬p∧¬q
```

还可使用交换律、结合律和分配律，例如 `p∧(q∨r)≡(p∧q)∨(p∧r)`。证明等价可列完整 truth table，或逐步使用等价定律。若要否定某个“等价”主张，只需找一组使左右真假不同的 counterexample（反例）。

例：

```text
(r∧¬p)∨(r∧q)∨(¬r∧¬p)∨(¬r∧q)
≡ r∧(¬p∨q) ∨ ¬r∧(¬p∨q)
≡ (r∨¬r)∧(¬p∨q)
≡ ¬p∨q
≡ p⇒q.
```

### Arguments and Entailment

argument（论证）由 premises（前提）和 conclusion（结论）组成。记号：

```text
φ₁,...,φₙ |= ψ
```

表示前提 entail（蕴含 / 逻辑推出）结论 `ψ`：真值表中，只要 `φ₁,...,φₙ` 全真，`ψ` 也必真。此时论证 valid（有效）。不关心前提本身为假的行。

例：`Frd∨Tyta, Frd⇒Late, ¬Late |= Tyta`。若 Frank 开 Ford，他会迟到；现在他没迟到；而他必定开 Ford 或 Toyota，因此他只能开 Toyota。

三个重要等价关系：

```text
φ₁,...,φₙ |= ψ    当且仅当    |= (φ₁∧...∧φₙ)⇒ψ
φ≡ψ               当且仅当    |= φ⇔ψ
```

应用于软件 requirements（需求）：若所有需求的合取不可满足，则需求无法同时实现；若 `φ₁,...,φₙ|=ψ`，则每个正确实现都保证 `ψ`；若 `φ₁,...,φₙ₋₁|=φₙ`，则 `φₙ` 是冗余需求。

### Quantifiers

`∀` 表示 for all（对所有），`∃` 表示 there exists（存在）。例：`∀x∈Z,∃y∈Z(y≤x)` 为真，可每次取 `y=x`；但 `∃y∈Z,∀x∈Z(y≤x)` 为假，因为整数没有最小元素。

务必区分：

```text
∃y∀x P(x,y) ⇒ ∀x∃y P(x,y)
```

左边有一个固定的 `y` 能处理全部 `x`，所以右边当然成立；反方向一般不成立，因为右边允许每个 `x` 选择不同的 `y`。

### Proof Methods

**Proof by cases（分类讨论）**：为证明 `A`，找穷尽的情况 `B₁,...,Bₙ`，证明 `B₁∨...∨Bₙ`，并分别证明每个 `Bᵢ⇒A`。

**Proof by contrapositive（逆否证明）**：要证明 `A⇒B`，改为证明 `¬B⇒¬A`，因为两者逻辑等价。

**Proof by contradiction（反证法）**：要证明 `A`，先假设 `¬A`，并推出某个命题 `B` 及其否定 `¬B`；矛盾说明 `¬A` 不可能，因此 `A` 成立。只是在“假设结论不成立”后直接推出结论，并不算真正的反证法——必须明确得到矛盾。

例：证明“若 `n` 是偶数，则 `n²` 是偶数”最自然是直接证明：`n=2k`，故 `n²=4k²=2(2k²)`，所以 `n²` 是偶数。

### Substitution and Equivalent Replacement

substitution（替换）是把一个符号的每次出现都换成一个表达式。例如在 `x²+7y=2xz` 中，用 `2k+3` 替换 `x`，得到 `(2k+3)²+7y=2(2k+3)z`。

逻辑中的重要规则：若 `|=φ(P)`，则对任意公式 `α`，仍有 `|=φ(α)`。例如 `|=P⇒(P∨Q)`，所以 `|=(A∨B)⇒(A∨B∨Q)`。

如果子公式 `α≡β`，那么在任何大公式 `φ` 中以 `β` 替换 `α` 后，整体仍等价：`φ(α)≡φ(β)`。这解释了公式化简时每一步“局部替换”的合法性。

## Boolean Algebra

### Boolean Functions and Notation

一个有 `n` 个输入命题的公式可视为 Boolean function（布尔函数）：`{0,1}ⁿ→{0,1}`，其中 `0=F`、`1=T`。一元、二元和 n 元函数分别称 unary、binary、n-ary。

布尔代数记号和命题逻辑对应如下：

```text
p∧q  ↔  p·q 或 pq
p∨q  ↔  p+q
¬p   ↔  p'
⊥    ↔  0
⊤    ↔  1
```

`+` 和 `·` 不是普通加乘法；例如 `1+1=1`。数字电路可看作计算某个 Boolean function 的装置。

### Boolean Algebra Laws and Simplification

常用化简规则：

```text
x+x=x,       xx=x                         idempotence
xx'=0,       x+x'=1                       complements
x·0=0,       x·1=x,  x+0=x,  x+1=1        identities
(x+y)'=x'y', (xy)'=x'+y'                  De Morgan
(x')'=x                                    double negation
x(y+z)=xy+xz,  x+yz=(x+y)(x+z)            distribution
x+xy=x,     x(x+y)=x                      absorption
xy+xy'=x                                  combining opposites
```

例：`xy+xy'=x(y+y')=x·1=x`。每次化简最好标记所用 law。

### Boolean Algebra as a General Structure

只要一个集合有 `0,1`、join（`+`）、meet（`·`）、complement（`'`），并满足交换、结合、分配、单位元与互补律，它就是 Boolean algebra。

集合幂集是重要例子：对 `Pow(S)`，令 `0=∅`、`1=S`、`X+Y=X∪Y`、`X·Y=X∩Y`、`X'=S\X`。因此集合运算和逻辑运算遵循同一组规律。位向量也一样，例如 `(1,0,1)+(1,1,0)=(1,1,1)`，按位执行 OR。

### CNF and DNF

literal（文字）是 `p` 或 `p'`。CNF（conjunctive normal form，合取范式）的最外层是 AND / `·`，每个 clause 是 literals 的 OR / `+`，如 `(p+q+r')(p'+q)`。DNF（disjunctive normal form，析取范式）的最外层是 OR / `+`，每个 clause 是 literals 的 AND / `·`，如 `p+rsq'`。

每个 Boolean expression 都可转换为等价 CNF 和 DNF。转 CNF 的流程：先用 `p⇒q≡p'+q` 消去蕴含；以 De Morgan 和双重否定把否定推进到 literals；再用分配律获得最外层为 `·` 的形式。DNF 的处理方向相似。

### Canonical DNF

canonical DNF（标准析取范式）从真值表的每一行输出 `1` 构造：变量值为 `1` 就写该变量，值为 `0` 就写其否定；把该行变量全用 `·` 相连，再把所有“真行”的项以 `+` 相连。

例：若 `E` 在 `(x,y)=(0,0),(1,0),(1,1)` 时为 1，则：

```text
E_dnf=x'y'+xy'+xy.
```

它可进一步化简为 `x+y'`。标准形式保证统一表示，但不一定最短。

### Karnaugh Maps

Karnaugh map（卡诺图）是 2、3、4 个变量时的图形化简方法：将真值表的 `1` 标记在格子上，用尽量少且尽量大的矩形覆盖所有 1。矩形大小只能为 `1,2,4,8,...`，不能覆盖 0，边缘可相接（地图像环面）。

合并相邻格的原理是一个变量在其中取真和假，从而消失：`xy+xy'=x(y+y')=x`。因此矩形越大，最终项通常越短。

### Isomorphism

若两个布尔代数间有一一对应，并保持 `0,1,+,·,'`，则它们 isomorphic（同构），记作 `B₁≅B₂`。直觉上是元素名字不同，但运算结构相同。

例：`Pow({a,b,c})≅B³`；子集 `{a,c}` 可编码为位向量 `(1,0,1)`。并集、交集、补集分别对应按位 OR、AND、NOT。

## Quick Exam Checklist

- 遇到集合相等：写 `x∈` 左边，分情况证明 `x∈` 右边；再反向证明。
- 遇到“necessary”：把“结果/目标”放在箭头左边；遇到“sufficient”：把“条件”放在箭头左边。
- 遇到区间内“同时被 a、b 整除”：使用 `lcm(a,b)`；遇到“被 a 或 b 整除”：用 inclusion-exclusion，减去同时被整除的部分。
- 分清 `x∈A`（元素）与 `{x}⊆A`（子集），以及 `λ`（空字符串）与 `∅`（空集合）。
- 区分 `satisfiable`（至少一组赋值为真）、`tautology`（所有赋值为真）和 `entailment`（所有前提真时结论真）。
- 证明 `A⇒B` 可优先考虑直接证明、逆否证明 `¬B⇒¬A` 或反证法；反证法必须实际导出矛盾。
- 布尔表达式化简时，每一步写清使用的定律；CNF 看最外层 `·`，DNF 看最外层 `+`。

## Week 2 Detailed Preview Notes: Logic, Proofs and Boolean Algebra

### Satisfiable Formula

**Definition：** 若公式 `φ` 在至少一种 truth assignment（给基本命题赋 T/F 的方式）下值为 `T`，则 `φ` 是 satisfiable（可满足的）。

**Explanation：** 只需存在一个可行情况，不要求每一种情况都真。这可用于检查一组时间表、派对规则或系统需求是否能同时实现。

**Example：** `p∧q` 在 `p=T,q=T` 时为真，因此可满足；`p∧¬p` 在任何赋值下都为假，因此不可满足。

### Satisfying Assignment and Model

**Definition：** 使一个可满足公式为真的 assignment 叫 satisfying assignment，也叫 model（模型）。

**Explanation：** formula 是规则本身；model 是一份实际满足规则的选择方案。

**Example：** 对 `φ=(J⇒¬S)∧(S⇒K)∧(K⇒J)`，赋值 `J=T,S=F,K=T` 是一个 model：John 和 Kim 来、Sarah 不来。三个条件分别变成 `T⇒T`、`F⇒T`、`T⇒T`，均为真。

### Valid Formula / Tautology

**Definition：** 若公式 `φ` 对所有 truth assignments 都为真，则它 valid（有效）或是 tautology（永真式），记作 `|=φ`。

**Explanation：** satisfiable 只要求“至少一行真”；tautology 要求“所有行真”。

**Example：** `p∨¬p` 无论 `p` 是 T 还是 F 都为 T，因此是 tautology；`p∧q` 虽然可满足，但当 `p=F` 时为 F，所以不是 tautology。

### Logical Equivalence

**Definition：** 若公式 `φ,ψ` 在每一种基本命题赋值下都有相同真值，则 `φ≡ψ`，称为 logically equivalent（逻辑等价）。

**Explanation：** 等价不表示两个式子外观相同，而表示它们表达完全相同的真假条件。可用真值表证明，也可逐步使用已知等价律。

**Example：** `p⇒q≡¬p∨q`。当 `p=T,q=F` 时，两边都为 F；其他三种赋值时两边都为 T。

### Fundamental Equivalence Laws

**Definition：** 常用等价律包括：

```text
p∨¬p≡⊤                 p∧¬p≡⊥
¬¬p≡p                  p∨⊥≡p, p∧⊤≡p
p∨q≡q∨p               p∧q≡q∧p
¬(p∧q)≡¬p∨¬q          ¬(p∨q)≡¬p∧¬q
p∧(q∨r)≡(p∧q)∨(p∧r)
p∨(q∧r)≡(p∨q)∧(p∨r)
```

**Explanation：** De Morgan's laws 用来把否定推进或拉出括号；distribution（分配律）常用来展开或因式分解逻辑公式。

**Example：** `¬(p∨q)≡¬p∧¬q`。若“p 或 q”不成立，表示 p 不成立且 q 也不成立。

### Counterexample

**Definition：** counterexample（反例）是让某个普遍主张失败的一组具体取值。

**Explanation：** 若要反驳“`φ≡ψ`”，只要找到一组赋值让一边为 T、另一边为 F；不需要检查所有情况。

**Example：** `(p⇒q)⇒r` 不等价于 `p⇒(q⇒r)`。取 `p=F,q=T,r=F`：左边为 `T⇒F=F`，右边为 `F⇒(T⇒F)=T`。

### Argument and Logical Entailment

**Definition：** argument（论证）由 premises（前提）和 conclusion（结论）组成。`φ₁,...,φₙ|=ψ` 表示前提 logically entail（逻辑推出）结论。

**Explanation：** 真值表中，只考察所有前提同时为 T 的行；若每一条这样的行中 `ψ` 也是 T，该论证 valid（有效）。前提为 F 的行不影响有效性。

**Example：** `p⇒q, p |= q`。唯一满足两个前提同时为真的情况是 `p=T,q=T`，此时结论 q 也真；这是 modus ponens 的形式。

### Requirements and Redundancy

**Definition：** 若一组软件需求对应公式 `φ₁,...,φₙ`，且 `φ₁,...,φₙ₋₁|=φₙ`，则最后一条需求 `φₙ` 是 redundant（冗余的）。

**Explanation：** 其他需求已经自动强制 `φₙ` 成立；不写它，系统可实现的行为也不会增加。不过为方便人阅读，实际需求文档有时仍会保留它。

**Example：** `A⇒B, B⇒C |= A⇒C`。因此若前两条已经是需求，`A⇒C` 没有提供新限制。

### Quantifiers

**Definition：** `∀` 是 for all（对所有），`∃` 是 there exists（存在）。

**Explanation：** 量词的顺序极重要：`∀x∃y` 允许每个 x 使用不同 y；`∃y∀x` 要求同一个 y 对全部 x 有效。

**Example：** 在整数中，`∀x∈Z,∃y∈Z(y≤x)` 为真，取 `y=x` 即可；但 `∃y∈Z,∀x∈Z(y≤x)` 为假，因为不存在最小整数。

### Proof by Cases

**Definition：** 为证明命题 `A`，找互相覆盖全部可能性的 cases `B₁,...,Bₙ`，证明 `B₁∨...∨Bₙ`，再分别证明每个 `Bᵢ⇒A`。

**Explanation：** 分类必须不遗漏；两个常见 case 是 `B` 和 `¬B`。

**Example：** 证明任意整数 n 的平方是偶数或奇数。n 要么偶数，要么奇数；若 `n=2k`，则 `n²=2(2k²)` 是偶数；若 `n=2k+1`，则 `n²=2(2k²+2k)+1` 是奇数。

### Proof by Contrapositive

**Definition：** 为证明 `A⇒B`，可证明逻辑等价的逆否命题 `¬B⇒¬A`。

**Explanation：** 当从 A 很难直接得到 B，但从“非 B”很容易推出“非 A”时特别有用。

**Example：** 要证“若 `n²` 是奇数，则 n 是奇数”，证明逆否：“若 n 是偶数，则 `n²` 是偶数”。令 `n=2k`，则 `n²=4k²=2(2k²)` 为偶数，故原命题成立。

### Proof by Contradiction

**Definition：** 为证明 A，先假设 `¬A`，并由此推出某个命题 B 与其否定 `¬B`。矛盾说明 `¬A` 不可能，因此 A 为真。

**Explanation：** 反证的关键不是仅仅“证明了 A”，而是真正出现不可能同时成立的两件事。

**Example：** 证明 `√2` 无理。假设 `√2=m/n` 为最简分数，则 `m²=2n²`，所以 m 偶数，令 `m=2k`；代回得到 `n²=2k²`，所以 n 也偶数。这与 `m,n` 无公因数矛盾，故 `√2` 无理。

### Substitution and Equivalent Replacement

**Definition：** substitution（替换）是把某符号的所有出现替换为表达式；equivalent replacement（等价替换）是在较大公式中以逻辑等价的公式替换一个子公式。

**Explanation：** 若 `|=φ(P)`，把 P 替换为任意公式 α 后仍有 `|=φ(α)`；若 `α≡β`，则 `φ(α)≡φ(β)`。这让逐步公式化简有严格依据。

**Example：** `P⇒(P∨Q)` 永真。用 `A∨B` 替代 P，可得 `(A∨B)⇒(A∨B∨Q)` 仍永真。

### Boolean Function

**Definition：** 有 n 个命题变量的公式可看作函数 `{0,1}ⁿ→{0,1}`，称为 Boolean function（布尔函数）；`0=F`、`1=T`。

**Explanation：** 一个公式的真值表就是这个函数的输出表；数字电路正是在计算这类函数。

**Example：** `f(A,B)=(¬A∨¬B)∧B` 的输出在 `(A,B)=(0,1)` 时为 1，其他三种输入时为 0。

### Boolean Algebra Notation and Laws

**Definition：** 布尔代数通常写 `p·q` 或 `pq` 表示 AND，`p+q` 表示 OR，`p'` 表示 NOT；`0,1` 分别表示 F,T。

**Explanation：** `+`、`·` 不是普通加乘法。例如 `1+1=1`。常用法则有 absorption：`x+xy=x`，以及 combining opposites：`xy+xy'=x`。

**Example：** `xy+xy'=x(y+y')=x·1=x`。两个项唯一差异是 y 的真假，因此 y 被消去。

### Boolean Algebras Beyond Truth Values

**Definition：** 一个集合若配有 `0,1, +, ·, '` 并满足布尔代数规律，就是 Boolean algebra；它不必是 `{0,1}` 本身。

**Explanation：** 集合操作和位向量操作也遵循同一结构：这解释了逻辑、电路与集合论为何可用相似的化简规律。

**Example：** 在 `Pow(S)` 中，`0=∅`、`1=S`、`X+Y=X∪Y`、`X·Y=X∩Y`、`X'=S\X`。例如若 `S={a,b,c}`，`{a}+{b,c}={a,b,c}`。

### CNF and DNF

**Definition：** literal 是变量 `p` 或它的否定 `p'`。CNF（conjunctive normal form）最外层为 AND，每个 clause 是 literals 的 OR；DNF（disjunctive normal form）最外层为 OR，每个 clause 是 literals 的 AND。

**Explanation：** 名称看最外层运算。任何 Boolean expression 都能转为等价的 CNF 和 DNF：先消去 `⇒`，用 De Morgan 推进否定，最后用分配律整理。

**Example：** `(p+q)(p'+r)` 是 CNF；`p+q'r` 是 DNF。`p+q'` 同时是 CNF（一个 OR clause）和 DNF（两个单 literal clauses）。

### Canonical DNF

**Definition：** canonical DNF 从真值表的所有输出为 1 的行构造：值为 1 的变量原样写，值为 0 的变量取否定；每一行用 AND 连接，所有真行再用 OR 连接。

**Explanation：** 它提供每个布尔函数的统一表达方式，但可能比实际最短表达式长得多。

**Example：** 若 E 在 `(x,y)=(0,0),(1,0),(1,1)` 时为 1，则 canonical DNF 为 `x'y'+xy'+xy`；它可化简为 `x+y'`。

### Karnaugh Map

**Definition：** Karnaugh map（卡诺图）是适用于 2 至 4 个变量的图形化布尔化简法。把函数为 1 的真值表格子标出来，再用尽量少且尽量大的矩形覆盖它们。

**Explanation：** 每个矩形的格子数必须是 1、2、4、8 等 2 的幂，不能覆盖 0；边缘可相接。矩形合并能消去在其中同时出现真/假的变量。

**Example：** 两个相邻格对应 `xy` 和 `xy'`，可合并为 `x`，因为 `xy+xy'=x(y+y')=x`。

### Isomorphism

**Definition：** 若两个 Boolean algebras 间存在一一对应，且该对应保持 `0,1,+,·,'`，则它们 isomorphic（同构），记作 `B₁≅B₂`。

**Explanation：** 同构表示元素名称不同，但运算结构完全一样；研究一种结构即可理解另一种。

**Example：** `Pow({a,b,c})≅B³`。子集 `{a,c}` 对应向量 `(1,0,1)`；集合并、交、补分别对应按位 OR、AND、NOT。
