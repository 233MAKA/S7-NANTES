# 1- 后面要新增的内容
## 1.1- 深度优先搜索，广度优先搜索

# 2- PPT正文（从第三章开始）
## 2.1- 之前的小句：Fait 15（背景）

> **Recherche et non choix → Utiliser la puissance de calcul brute de l’ordinateur**

翻译：  
**“只有搜索而没有‘聪明的选择’，就只能靠电脑的蛮力算力。”**

意思就是：  
这一章讲的是**不使用启发式（heuristic）**，只靠**穷举 / 回溯 / 遍历**的做法，所以叫 **Techniques exploratoires aveugles（盲目探索技术，uninformed search）**。

---

# 3- 🔹 3.1 Explorations récursives dans un graphe d’états

（在状态图里的递归探索）

---

### 3.1.1- Fait 16

> **L’approche récursive amène à une exploration d’arbre.**

翻译：  
**“递归的方法自然会把搜索过程变成一棵树的探索。”**

解释：

- 状态图（graph）里可能有很多环、交叉路径。
    
- 但如果你写一个**递归函数**：“从当前状态出发，尝试所有可能的下一步”，  
    实际上你在构造的就是一棵**搜索树（search tree）**：
    
    - 根：初始状态
        
    - 树的每条边：一次操作
        
    - 子节点：执行操作后的新状态
        

所以 PPT 在提醒：  
**一旦你用递归做搜索，你脑中应该想的是“树”，不是“图”。**

---

### 3.1.2- Lemme 17

> **La taille d’un arbre d’exploration peut être exponentielle en celle du graphe d’états sous-jacent, voire infinie…**

翻译：  
**“探索树的规模，相对于底下的状态图的规模，可以是指数级的，甚至可能是无限大的……”**

解释：

- 状态图里可能只有有限多个状态（比如 8-puzzle）。
    
- 但是递归搜索树会：
    
    - 重复访问同一个状态
        
    - 走很多不同的路径到达同一状态
        

所以：

- 底层图也许只有几千个状态；
    
- 递归搜索树可能有 **指数级（exponential）** 多的节点。
    

后面的“备注”又说：

> 图本身就可能是指数级甚至无限大，那搜索树就更可怕了。

总结一句：  
**递归盲搜 = 一不小心就“爆炸”（状态数指数增长）的那种。**

---

## 3.2- Retours en arrière（回溯搜索 backtracking）

这一小节讲的是**最朴素的递归搜索**。

---

### 3.2.1- 上面那句话

> **A priori suffisant pour rechercher une (la première) solution quand l’espace de recherche est un arbre … de taille finie.**

翻译：  
**“在搜索空间是一棵（所有子树都有限）有限大小的树时，用这种方式（回溯）来找一个（第一个）解，一般是足够的。”**

也就是说：

- 如果**没有环**，而且树是**有限**的，
    
- 那么简单的递归回溯就能保证：  
    只要有解，总能最后找到一个解。
    

---

### 3.2.2- Définition 18（Recherche par retours en arrière）

原文结构（我用简化符号抄一遍）：

> 我们定义一个函数  
> **R : E → O*  
> （从一个状态 e，返回一个操作序列）**
> 
> R(e) =
> 
> - `()` 如果 e ∈ F（已经是目标状态）
>     
> - `S` 如果存在某个操作 o ∈ O，使得 p(o,e) 为真且递归结果 SR ≠ ⊥
>     
> - `⊥` 否则（失败）
>     
> 
> 其中
> 
> - SR = R(o(e)) （在 o(e) 上递归调用）
>     
> - S = (o) ⊗ SR（把当前操作 o 接到后面得到的操作序列前面）
>     

这里的记号：

- **O*** = 所有“操作序列”的集合
    
- `()` = 空序列（什么也不做）
    
- `⊥` = 失败 / 没有解
    

---

### 3.2.3- 把它翻成超白话的算法：

对状态 **e**，回溯函数 **R(e)** 干三件事：

1. 如果 e 已经是目标状态：  
    → 返回空序列 `()`（不需要再做任何操作）。
    
2. 否则，依次尝试每个可用的操作 o：
    
    - 如果 o 在 e 上是可应用的（p(o,e) 为真），
        
    - 就对新状态 `e' = o(e)` 递归调用 R(e')。
        
    - 如果递归返回了一个真正的解（不是 ⊥），记为 `SR`，  
        那么整个解就是：  
        **S = 把 o 接到 SR 前面**  
        然后返回 S。
        
3. 如果所有操作都试完了都失败（每次递归都得到 ⊥）：  
    → 返回失败 `⊥`。
    

这就是**典型回溯**：  
“先试一个选择，如果走到死路就回头，换别的选择”。

---

### 3.2.4- Notations（符号说明）

PPT 在 Definition 18 下面还说明了几个记号：

- **A***：  
    表示所有“有限长度的 A 元素序列”的集合  
    （比如 O* = 所有有限长的操作序列）
    
- **⊥**：  
    表示“未定义 / 失败”，在 SQL 里类似 `NULL` 的意思。
    
- **⊗**：  
    表示把元素接到一个序列前面，或者是“拼接两个序列”。
    

---

### 3.2.5- Remarque（备注）

> **On peut renvoyer la séquence des états successifs au lieu des opérations…**

翻译：  
**“我们也可以返回状态序列，而不是操作序列；  
或者返回 ‘(操作, 状态)’ 成对的序列。”**

也就是说：

- 现在 R 返回的是“操作列表”：`[o1, o2, ..., on]`
    
- 你也可以改成返回：
    
    - 状态列表 `[e0, e1, ..., en]`
        
    - 或者 `[(o1, e1), (o2, e2), ...]`
        

意思：**形式不重要，逻辑是一样的**。

---

## 3.3- Détection des circuits（检测回路 / 环）

回溯在有**环（cycle）**的图里会出事：  
可能无限递归，比如在 A 和 B 之间来回跳。

这一节就是在回溯的基础上，加入“记住祖先状态”的方法。

---

### 3.3.1- 小标题下面那句话

> **Mémorisation des états ancêtres (A) de l’état courant …**

翻译：  
**“记住当前状态的祖先状态集合 A（即已经在当前路径上出现过的状态）。”**

目的：  
如果又回到 A 中的某个状态，说明形成了回路 → 立刻剪掉。

---

### 3.3.2- Définition 19（Recherche par retours en arrière avec détection des circuits）

现在 R 不再只接收一个状态 e，而是接收**二元组 (e, A)**：

- e：当前状态
    
- A：当前路径上已经出现过的所有状态（祖先集）
    

原文结构简化：

> R(e, A) =
> 
> - `()` 如果 e ∈ F
>     
> - `S` 如果 e ∉ A 且存在可应用的操作 o，递归结果 SR ≠ ⊥
>     
> - `⊥` 否则
>     
> 
> 递归调用时：
> 
> - SR = R(o(e), A ∪ {e})
>     
> - S = (o) ⊗ SR
>     

翻译要点：

1. **e ∉ A**：  
    当前状态不能是祖先状态之一，否则就是回路 → 直接判失败。
    
2. **每次下探时，把当前状态加入 A**：  
    `A ∪ {e}`
    

这样，任何路径上如果出现重复状态，就不会继续扩展。

---

### 3.3.3- 这段在说什么？

**这是“带环检测的回溯搜索”：**

- 纯回溯：可能在图里转圈圈
    
- 回溯 + 祖先记忆 A：
    
    - 不允许在同一条路径中重复状态
        
    - 保证不会在环里无限下去
        

注意，这里的 A 只记“当前路径上的状态”，  
**不是整张图所有访问过的状态**（那个是 memoing，后面讲）。

---

## 3.4- Recherche de profondeur bornée（有深度上界的搜索）

这一节解决另一个问题：  
**即使没有回路，树也可能无限深**——要是你一直往深处钻，又不设上限，也会“掉进无底洞”。

---

### 3.4.1- 上方文字

> **Pour éviter les branches infinies …**

翻译：  
**“为了避免无穷分支（例如图中有回路，也可能状态空间本身就是无限的），我们引入深度上界。”**

---

### 3.4.2- Définition 20（Recherche de profondeur bornée）

现在 R 接收参数 (e, B)：

- e：当前状态
    
- B：剩余的“允许最大深度”（一个非负整数）
    

结构简化：

> R(e, B) =
> 
> - `()` 如果 e ∈ F
>     
> - `S` 如果 B > 0 且存在可应用的操作 o，使得对 (o(e), B-1) 递归结果 SR ≠ ⊥
>     
> - `⊥` 否则（包括 B = 0）
>     

解释：

1. 如果到达目标状态：OK
    
2. 否则，只有在 **B > 0** 的情况才继续向下递归；  
    每往下一层，深度限制减一（B-1）。
    
3. 如果 B = 0，还没到目标，就**强制停止并返回失败**。
    

这就是标准的 **depth-limited search（深度限制搜索）**。

---

### 3.4.3- 和前面两种的关系：

- **基础回溯**：不防环，不限深度
    
- **回溯 + 检测环**：防环，但可能仍然无限深
    
- **深度限制搜索**：强制搜索树最多到某一深度 B
    

你可以组合使用：  
“祖先检测 + 深度限制”。

---

## 3.5- Recherche étagée（分层搜索 / 迭代加深）

这一节讲的其实就是**迭代加深搜索 iterated deepening**，但 PPT 用的是“Recherche étagée”。

目标：

> **“Pour rechercher une meilleure solution, en nombre d’étapes, en évitant l’écueil de la profondeur à choisir / trouver manuellement.”**

翻译：

**“为了在步骤数上找到一个更好的解，同时避免必须手工选择 / 设定一个合适的深度上限 B —— 我们使用分层搜索。”**

---

### 3.5.1- Définition 21（Recherche étagée）

形式上：

- 参数是 (e, B, Bmax)：
    
    - e：初始状态
        
    - B：当前正在使用的深度上限
        
    - Bmax：最大允许的深度上限（一般是 ∞）
        

大致定义（简化）：

> RE(e, B, Bmax) =
> 
> - S，如果 B ≤ Bmax 且 S ≠ ⊥
>     
> - ⊥，如果 B > Bmax
>     
> 
> 其中 S = R(e, B)  
> 如果 S = ⊥ 且 B ≤ Bmax，则继续用 B+1 再调用：RE(e, B+1, Bmax)

翻译成口语：

1. 先用深度限制 B 做一次搜索：`S = R(e, B)`
    
2. 如果找到了一个解 S（≠ ⊥），返回它。
    
3. 如果没找到，并且 B 还没有超过最大深度 Bmax：
    
    - 把 B 加一
        
    - 再做一次深度限制搜索
        
4. 如果 B 已经超过 Bmax，还没有解：
    
    - 返回失败。
        

这就是标准的 **迭代加深（iterated deepening）**：

> B = 0,1,2,3,4,... 逐层增加最大深度，每次都跑一次“深度限制搜索”。

---

### 3.5.2- Théorème 22（复杂度）

> **Le temps d’une recherche étagée n’est que deux fois plus important que celui de la recherche bornée avec la profondeur optimale.**

翻译：

__“分层搜索在时间上的代价，最多只比‘用最优深度 B_ 的一次深度限制搜索’多一倍左右。”_*

直观解释：

- 虽然你看起来重复搜索了很多次：  
    B=1，B=2，B=3……
    
- 但**浅层节点被访问很多次，深层节点只访问一次**，
    
- 大头成本来自最深那一层，前面那些重复访问的总耗时加起来不会太夸张。
    

所以迭代加深在理论上是“划算”的。

---

### 3.5.3- Théorème 23

> **Une recherche étagée est similaire à une recherche en largeur d’abord.**

翻译：

**“分层搜索类似于宽度优先搜索（breadth-first search）。 ”**

含义：

- 它同样是“按层推进”，只是用的是一系列深度优先 + 增加深度的方式来实现。
    
- 在步骤数相同代价的情况下，它也能保证找到“步数最少”的解。
    

---

### 3.5.4- Corollaire 24（Complétude）

> **La recherche étagée est complète.**

翻译：

**“分层搜索是完全的。”**

这里用到了后面会正式给的概念：

- **完全性（complétude, completeness）**：  
    只要存在解，算法一定能找到。
    

迭代加深会逐渐增大深度 B →  
只要解存在于有限深度内，就一定会在某个 B 被找到。

---

## 3.6- « Memoing » ou la détection des cycles

（Memoing：用记忆避免重复 / 检测环）

这一节是把“记忆”引入搜索。  
和 3.1.2 的“祖先记忆”不同，这里记的是“**所有已经解决过的状态**”。

---

### 3.6.1- 开头文字

> **Pour éviter une explosion combinatoire due aux cycles dans les graphes denses.**

翻译：

**“为了避免在稠密图中因为环而导致的组合爆炸，我们使用 memoing。”**

---

### 3.6.2- Définition 25（« Memoing » avec « effet de bord »）

大概结构：

- 我们维护一个映射 M：  
    **状态 e → 解 S 或 ⊥**
    
- 初始：M ← ∅
    

R(e) 定义为：

> R(e) =
> 
> - `()` 如果 e ∈ F
>     
> - M(e) 如果 M(e) ≠ ⊥（这个状态以前已经算过了）
>     
> - S 如果存在操作 o，可以应用在 e 上，且递归 R(o(e)) 得到 SR ≠ ⊥；  
>     并且把 (e, S) 加入 M。
>     
> - `⊥` 否则
>     

翻译成白话版：

**当你想求解一个状态 e：**

1. 如果 e 是目标状态：返回 `()`。
    
2. 如果在 M 里已经存过 e 的结果：直接取出来，不用再算。
    
3. 否则：像普通回溯一样，尝试操作 o：
    
    - 找到一条成功解 S 后，把 **M(e) = S** 存起来；
        
    - 下次再遇到 e 就可以直接用。
        

这就叫 **memoing（带备忘录的搜索）**。

PPT 还说：

> “Dans le cas général, c’est un cache.”

**也就是说：M 就是一个“缓存（cache）”。**

---

### 3.6.3- 几条 Remarque（备注）的意思：

- M 不是只能记具体的解，也可以记“这个状态是死路（⊥）”；  
    这样下次遇到 e 就不用再展开。
    
- 很多策略可以用来控制 M 的大小，避免把整个搜索图全记下来。
    
- 极端情况下，memoing 演化成**动态规划（dynamic programming）**。
    

---

## 3.7- Séparation et évaluation（分离与评估）

这一节的形式化比较复杂，本质是一个**branch & bound（分支限界）风格**的盲搜索：

目标：

> **Pour rechercher une des meilleures solutions, en coût total des opérations.**

翻译：

**“为了按操作总成本，寻找一个较优的解（成本较小）。”**

核心思想（不引入新内容，只按 PPT 的文字解释）：

- 参数里出现了：
    
    - e：当前状态
        
    - B：当前允许的最大成本（一个 bound）
        
    - A：一个操作集合
        
- 定义了一个辅助函数 T(e, B, A)：  
    对当前可选的一组操作做试探和剪枝：
    
    - 如果 A 为空：失败
        
    - 否则在 A 中选一个操作 o，  
        检查对应的递归解是否在成本 B 之内，  
        如果可以 → 返回成功；  
        否则从 A 中删掉这个 o，继续尝试剩余操作。
        

备注里说：

- 如果 B ≠ +∞，而且 c(o,e)=1，  
    这种搜索就退化成一种**深度限制搜索**。
    
- 如果 B 太大，还是有可能掉进一条无限分支。
    
- 总体上，这是用“成本上界 B”来剪枝的一种方式。
    

你只要抓住 PPT 想表达的点：

> **“分离与评估 = 按成本做剪枝的一种盲搜索方式。”**

不用死抠公式细节，考试通常也是问“思想”。

---

## 3.8- Comparaison（比较）

这一小节给出两个定义 + 一张表。

---

### 3.8.1- Définition 27（Complétude 完全性）

> **Un algorithme est complet s’il trouve une solution quand elle existe.**

翻译：

**“如果只要解存在，这个算法就一定能找到解，那么这个算法就是完全的（complet / complete）。 ”**

---

### 3.8.2- Définition 28（Optimalité 最优性）

> **Un algorithme est optimal s’il trouve la meilleure solution, quand elle existe.**

翻译：

**“如果在解存在的情况下，这个算法能找到其中最好的解，那么这个算法是最优的（optimal）。 ”**

---

### 3.8.3- 表格（你截图下面那张）

表里横向列的是几种算法：

- retours en arrière（纯回溯）
    
- retours en arrière + détection de circuits（带环检测）
    
- recherche avec « memoing »（带备忘录）
    
- recherche étagée（迭代加深）
    
- séparation et évaluation（分离与评估）
    

纵向比较的维度大致有：

- complete 吗？
    
- 时间复杂度（最小、最大）
    
- 空间复杂度
    
- 是否 optimal
    

你现在不需要把表背下，只要记住几个关键信息（都写在 PPT 中）：

- **纯回溯**：一般不完全（可能卡在无限分支里）；也不最优。
    
- **回溯 + 检测环**：在有限空间下是完全的；不保证最优。
    
- **memoing**：在有限空间下完全，时间好很多，但用更多空间。
    
- **迭代加深**：
    
    - 完全
        
    - 和宽度优先类似，若代价一致，可以找到步数最短解
        
- **分离与评估**：
    
    - 用成本上界 B 做剪枝
        
    - 如果初始 B 合理（或逐步调整），可以接近“branch & bound”效果
        

PPT 最后一行 remark 说：

> 这些算法配合第 8 定义里的建模，是**前向链推理器 + 状态空间模型**，构成所谓“专家系统”的核心引擎之一。

---

## 3.9- 整个 3.1 的超短总结

- **3.1 = 一整套递归盲搜索变体。**
    

从简单到复杂依次是：

1. 回溯（backtracking）
    
2. 回溯 + 环检测（ancestor set A）
    
3. 深度限制搜索（加入深度 B）
    
4. 迭代加深（从 B=0,1,2,…反复跑 3）
    
5. memoing（记忆状态 → 避免重复展开）
    
6. 分离与评估（按成本 B 做剪枝）
    

再用两句话压缩：

- **完全性（completeness）= 肯定能找到解吗？**
    
- **最优性（optimality）= 找到的是最好的解吗？**
    

---

# 4- #️⃣ **4 启发式与知情搜索（Heuristiques et recherches informées）**

---

## 4.1- **✦ Fait 29**

### 4.1.1- **【法语】**

Recherche **et** choix ⇒ Utiliser _raisonnablement_ la puissance de calcul de l’ordinateur.

### 4.1.2- **【中文翻译】**

“搜索 + 选择” ⇒ 应当**合理地使用**计算机的计算能力。

### 4.1.3- **【解释】**

第 3 章我们讲的是“盲目搜索”，也就是：  
所有分支都要尝试。

而第 4 章开始强调：

> 搜索不能只靠暴力枚举，要靠“选择”  
> 要让算法更聪明，而不是更累。

“合理使用计算能力” = 不要盲搜 → 用**启发式（heuristic）**。

---

## 4.2- **✦ Fait 30**

### 4.2.1- **【法语】**

Il s’agit ici de fournir au système une connaissance **experte** sur le problème,  
c’est-à-dire d’introduire enfin un peu d’« intelligence » dans le programme.

### 4.2.2- **【中文翻译】**

这里要做的是向系统提供一些关于问题的**专家知识**，  
也就是：终于在程序中加入一点“智能”。

### 4.2.3- **【解释】**

所谓启发式，就是给算法加点“聪明的大脑”。

例子：A* 搜索

- h(n) = 当前状态到目标的预估距离
    
- 这个“预估法”就是“专家知识”
    

---
## 4.3- #️⃣ **4.1 方法家族（Familles d’approches）**

---

### 4.3.1- **【法语】**

1. algorithme **exact** dans les **cas pratiques**
    
2. algorithme **efficace** pour des **sous-classes** de problèmes
    
3. algorithme **approximatifs**, avec garanties…
    
4. algorithme **approximatifs**, sans garanties…
    

### 4.3.2- **【中文翻译】**

1. **精确算法（exact）**（在实际规模下可用）
    
2. **高效算法**（只针对某些子问题类别）
    
3. **近似算法，有保证**（approximation with guarantees）
    
4. **近似算法，无保证**
    

### 4.3.3- **【解释】**

有些问题（如 TSP）：

- 精确算法（exact）在理论上可做，但太慢
    
- 于是我们有启发式、贪心、A*、估价函数等方法
    
- 这些都属于启发式家族
    

---

---

## 4.4- #️⃣ **4.2 非确定性或完美选择（Non-déterminisme ou le choix parfait）**

---

## 4.5- **Définition 31：Oracle（神谕）**

### 4.5.1- **【法语】**

Un oracle, choix_nd(X), renvoie toujours la (une) valeur x ∈ X qui amène à une solution, si elle existe  
(échec implicite, ⊥, si X = ∅).

### 4.5.2- **【中文翻译】**

一个神谕（oracle）choix_nd(X) 总是返回集合 X 中**能够通往解的那个 x**（如果存在）。  
如果 X 空，则返回失败（⊥）。

### 4.5.3- **【解释】**

这是“完美选择器”：  
给它一组操作，它总选对的那个（超自然能力）。

现实中当然不存在，但它用于**定义**带 oracle 的搜索模型。

---

## 4.6- **Définition 32：无回溯的搜索（带 oracle）**

### 4.6.1- **【中文翻译】**

[  
R(e) =  
\begin{cases}  
() & \text{如果 } e \in F \  
S & \text{如果 } SR \neq \bot \  
\bot & \text{否则}  
\end{cases}  
]

其中：

- (o = choix_nd({o \in O : p(o,e)}))
    
- (SR = R(o(e)))
    
- (S = (o) \otimes SR)
    

### 4.6.2- **【解释】**

如果有神谕：

- 每次都选**正确操作 o**
    
- 所以完全**不需要回溯**
    
- 直接一步走到底
    

→ “完美选择”模型。

---

## 4.7- **练习：**

解决旅行商问题（TSP）——假设你有神谕。  
（当然结果是瞬间解出。）

---

## 4.8- **备注：Oracle 在实践中靠什么实现？**

1. 尽可能“有信息”的选择（heuristic）
    
2. 如果选错，再回溯
    

在 Prolog 中，oracle 就是：

```
member(X, L)
```

---

---

## 4.9- #️⃣ **4.3 启发式用于完整搜索（Heuristiques pour recherches totales）**

---

## 4.10- 意思概述：

> 使用启发式进行搜索  
> 让“选择”更加聪明  
> 优先探索更有希望的分支

## 4.11- 4.11**4.3.1 死胡同检测（Détection des impasses）**

---

### 4.11.1- **Définition 33：Impasse（死胡同）**

### 4.11.2- **【法语】**

Un état e est une impasse si, et seulement si,  
quels que soient les chemins issus de e dans le graphe,  
aucun n’arrive à un état final f ∈ F.

### 4.11.3- **【中文翻译】**

一个状态 e 是死胡同，当且仅当：  
从 e 出发的所有路径都无法到达任何终态 f。

### 4.11.4- **【解释】**

Impasse ≠ 没有操作  
它可能能继续走，但最终 **永远不可能成功**。

例如：

- 8-puzzle 中某些排列不可达
    
- 图搜索中某些节点会通往无限循环，但不抵达终点
    

启发式可以提前识别“必死状态”，直接剪枝。

---

## 4.12- **备注：**

死胡同不像 precondition（前置条件）那样简单：

- precondition 只管当前操作是否能执行
    
- impasse 要考虑状态“未来能不能解决”
    
- 因此不适合放入操作的前置条件中
    
- 否则要在所有操作的 precondition 中重复 impasse 检测
    

---

---

## 4.13- 5-**4.3.2 选择顺序优化（Ordonnancement des choix）**

### 4.13.1- **【法语】**

Il est préférable qu’une (ou la meilleure) solution se trouve dans le premier fils d’un état.  
En cas de chance extrême, elle serait même trouvée en temps linéaire !

### 4.13.2- **【中文翻译】**

最好情况下，某个（或最优）解就位于当前节点的第一个子节点。  
如果极其幸运，甚至能在线性时间内找到解！

### 4.13.3- **【解释】**

这是“把最可能成功的分支放在最前面”的策略。

例：在 TSP 中先尝试最近的城市。

---

## 4.14- **Définition 34：加入排序启发式的分离与评估**

### 4.14.1- **关键变化：**

把候选操作集合

```
{o ∈ O : p(o,e) ∧ c(o,e) ≤ B}
```

交给：

```
tri_heuristique(...)
```

排序成“更可能成功的顺序”。

### 4.14.2- **解释：**

就是让 branch & bound **从最有希望的分支开始展开**。

---

---

## 4.15- 5-**4.3.3 启发式评估（Évaluation heuristique）**

---

## 4.16- **Définition 35：代价下界启发式（minorante）**

### 4.16.1- **【中文】**

定义一个启发式集合 (H = { h : O \times E \to \mathbb{R}^+ })，  
满足：

[  
h(o,e) \le \text{coût}(R_{BB}(e))  
]

意思：  
**h 是实际代价的保守估计（永远比真实代价低）**

### 4.16.2- **解释：**

这是 A* 的下界启发式（admissible heuristic）思想。

---

## 4.17- **Définition 36：加入评估启发式的分离与评估**

主要变化：

- 过滤时用 `h(o,e) ≤ B`
    
- 排序时对操作集合进行 `tri`（可能按 h 排）
    
- 如果 `h(o,e) > B` 则直接剪枝
    

→ 完整版 branch & bound + 启发式。

---

## 4.18- **备注：**

- T 中额外的测试（h(o,e) > B）是为了在 B 收紧时剪掉以前打开的分支
    
- impasse（死胡同）可以被视作 h(e)=∞ 的启发式
    

---

---

## 4.19- **4.3.4 约束传播（Propagation de contraintes）**

---

## 4.20- **Fait 37**

### 4.20.1- **中文：**

加入约束能减少搜索空间，无论：

- 静态地（E）
    
- 动态地（R）
    

---

## 4.21- **Fait 38：约束传播的原则**

1. 选择**最受约束的元素**
    
2. 尽可能传播选择的后果
    

### 4.21.1- **解释：**

这是约束满足问题（CSP）中的：

- MRV（Minimum Remaining Value）
    
- Forward Checking
    
- AC-3（Arc Consistency）
    

---

## 4.22- **示例：N 皇后问题**

两种表示 E：

1. 直接表示（矩阵 n×n） → 太大
    
2. 按列表示（长度 n 的数组）→ 约束小很多
    

R 的几个策略：

1. 按顺序放置 → 8^8 ≈ 10^7
    
2. 在合法行中选择 → 8! ≈ 10^4
    
3. 约束传播 → 约 10^2
    

→ 得到巨大改进。

---

---

## 4.23- 🎯 最终总结（你看懂了就是通关 4 章）

第 4 章主要讲：

---

## 4.24- 🌟 启发式的三大方向：

### 4.24.1- **死胡同检测（impasse）**

- 识别必死的状态
    
- 提前剪枝
    
- 大幅减少搜索
    

### 4.24.2- **选择顺序优化（ordonnancement）**

- 把“更可能成功”的分支放前面
    
- 可能极端情况下在线性时间就找到解
    

### 4.24.3- **启发式评估（evaluation heuristique）**

- 加一个估价函数 h
    
- 结合 branch & bound
    
- 得到 A* 级别的智能搜索

---
## 4.25- 🌟 最终效果：

**让搜索变“聪明”而不是“暴力”。**

---
