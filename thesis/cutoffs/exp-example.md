> Was a similar question already studied in MSO-related field? Namely: 
  >> what is the minimal size of the ring that satisfies the formula of the form $(\forall) \forall \exists .. \forall \exists \phi$? 
  >> (where $\phi$ can talk about adjacency and order, and the very first node is fixed to $0$) 

#### Idea
The idea is to build a formula which is satisfied iff the following game is won by E-player:
0. A-player chooses a node in a line which divide the line into two halves
1. E-player chooses a half, then chooses the node in that half which should not touch boundary nodes of that half
2. now, A-player chooses a half from halves in (1) and is free to choose any node in that half
3. Repeat (1)-(2).
E-player loses when it cannot choose a node that does not touch boundaries.


#### Formula
The formula is
$$
(\forall)\forall s^1 \exists m^1 \ \forall s^2 \exists m^2 ... \forall s^q \exists m^q:\\
\bigwedge_{k} m^{k}\textit{ does not touch any node of level } \leq k \land \\
\bigwedge_{k \in[1,q-1]}\\
s^{k+1} \in (prev(m^k), m^k) \rightarrow m^{k+1} \in (prev(m^k), m^k) \land \\
s^{k+1} \in (m^k, next(m^k)) \rightarrow m^{k+1} \in (m^k, next(m^k))
$$
where:
- $0$ is the initial node in a ring (you can say it is in the scope of the first quantifier "$(\forall)$", and is fixed by symmetry to $0$)
- $\in$ can be defined via the proposition $token$
- $prev(m^k)$, $next(m^k)$ are expressible via $0,m^1,...,m^{k-1},s^1,...,s^{k-1}$ using $token$. Informally, $prev(m^k)$ \[$next(m^k)$\] is the first node of level $\leq k$ before \[after\] $m^k$.

#### Examples
Consider an example with $q=1$. The formula is 
$$
(\forall)\forall s^1 \exists m^1:\\
m^{1}\textit{ does not touch } s^1, 0
$$
Manual checking gives that a ring should contain at least $7$ nodes to satisfy the formula.

Consider an example with $q=2$:
$$
(\forall) \forall s^1 \exists m^1 \ \forall s^2 \exists m^2:\\
m^{1}\textit{ does not touch } s^1,0 \land \\
m^{2}\textit{ does not touch } s^2, s^1, m^1, 0 \land \\
s^{2} \in (prev(m^1), m^1) \rightarrow m^{2} \in (prev(m^1), m^1) \land \\
s^{2} \in (m^1, next(m^1)) \rightarrow m^{2} \in (m^1, next(m^1))
$$
It is quite difficult to say how large should be a ring to satisfy the formula, but later we show that $\geq 27$.

#### Analysis
Let us analyze the minimal size of the ring that satisfy the formula. 

Consider the following game:

Two players: A-player, E-player.

They play on _lines_ with start $s$ and end $e$. This abstracts rings with _fixed_ initial node, when you bend a line $s,e$ into a ring you get $s=e$. Fixing initial node is "like" removing the very first quantifier from the formula.

Given: an alternating quantifier structure $\forall\exists...\forall\exists$ of arbitrary length $q:=|\forall\exists...\forall\exists|/2$, and a line of arbitrary length. (i.e., the very first quantifier "$(\forall)$" is not accounted)

If the current quantifier is $\forall$, then A-player moves, otherwise E-player moves (the current quantifier := the leftmost quantifier).

One _step_ is a move of A-player followed by a move of E-player. The game consists of $q$ steps.

Initially, the _current line_ is $s..e$. The current line is updated after each step of the game.

In step $k$, A-player chooses node $s^k$ on the current line. Then, E-player chooses $m^k$ node on the current line. The current line becomes $prev(m^k), next(m^k)$.

The game is winning for A-player if A-player has a strategy s.t. after some step $k$ $m^k$ is adjacent to the boundaries of the current line or to $s^k$.

> The game is losing for A-player iff the ring satisfies the formula (don't forget to add the very first quantifier $\forall/\exists$).

Let us analyze how large should be the line (i.e., ring) for the game to be winning for E-player.

Let us call nodes not chosen _internal_. Let $win_{q}$ be the minimal # of internal nodes in the line for the game with $2q$ alternating quantifiers to be winning for E-player.

In the beginning of step $k$, A-player moves, so the game for E-player to be winning, it should be winning no matter how A-player moves. Long story short, the number of internal nodes in the current line should be $win_{q} = 4*win_{q-1} +2$ -- see the figure below:

<img src="https://www.dropbox.com/s/fhvozfk68tezb8q/computing-minsize-illustration.jpg?raw=1"/>

Computing $n$th element gives: $win_q = 4^q(win_0 + \frac{2}{3})-\frac{2}{3}$, where $win_0 = 1$ (that means that after the last choice by E-player, there is something between that chosen node and other nodes). Note that this gives the number of internal nodes -- the total number of nodes in a ring should be $+1$, because we start playing with a line with one node fixed (we fixed 0 node). The cutoff thus is at least:
$$
cutoff_q \geq \frac{5*4^q + 1}{3}.
$$

This gives, for example:
  - `A AE -> 7` ($q=1$)
  - `A AEAE -> 27` ($q=2$)
  - `A AEAEAE -> 107` ($q=3$).

When using the fix-point algorithm implementation, it gives the same numbers for $q=1,2$ (and the quantifiers it produces are `A/E AEAE` and `A/E EAEA`).